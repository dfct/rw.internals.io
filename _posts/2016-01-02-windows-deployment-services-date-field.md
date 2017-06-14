---
layout: post
title: "Windows Deployment Services' Date field"
tags:
- winpe
- drives
- adk
- wds
- wim
---


I ran into a mild annoyance while working with Windows Deployment Services. Whenever I imported an image, WDS would use the **current** date_time in the Date field for the image, rather than the date_time when the image was created. As information inside the administrative WDS console, fine. But this same date is also displayed to users inside Windows Setup, under the title "Date Modified".

This proved a mild annoyance. For my use of WDS, the date WDS **imported** an image is not meaningful. If there must be a date (and I've found no means to hide the column in Windows Setup visible to users), then I'd rather it present the modified date the image had **prior** to WDS importing and (apparently) modifying it. In my case, that date would be the date of the release by Microsoft.

Unfortunately, the WDS console does not allow you to edit the date.

### Changing the Date

I turned to learning where the date was pulled from. The first obvious place to check is the NTFS file system, and indeed the file dates on the individual WIM files were similar to those in the console. Unfortunately, changing their NTFS timestamps (both the records in $STANDARD _INFORMATION and those in $FILE_ NAME) did not reflect back into the console.
I moved to exploring the various WDS databases. In subfolders of .\RemoteInstall, WDS has several ESE databases - DdpDb.mdb, binlsvcdb.mdb, and WDSMD.mdb. Popping them into [ESEDatabaseView](http://nirsoft.net/utils/ese_database_view.html), DdpDb appears focused on drivers (though does have a field for when a driver was added to WDS). Binlsvcdb appears focused solely on devices authorizations, but WDSMD looked promising! It holds information on the images in the store including name, group, and unique IDs, but maintained no date information.

The only remaining data in .\RemoteInstall was temporary logs, network boot files, BCDs and the WIMs. It was easy to rule out BCDs by enum'ing them and popping them into Regedit, leaving just the WIM files themselves as a reasonable location.

Opening the WIM in 7-Zip, there was nothing unusual stored alongside the folders and files. Displaying the WIM info with dism.exe /get-wiminfo also returned nothing unusual. Thankfully, Microsoft [released a whitepaper](http://go.microsoft.com/fwlink/?LinkId=92227) detailing the WIM file format back in 2007 that contained the clues needed.

The WIM header is laid out like so:

```
typedef struct _WIMHEADER_V1_PACKED
{
   CHAR              ImageTag[8];        // "MSWIM\0\0"
   DWORD             cbSize;
   DWORD             dwVersion;
   DWORD             dwFlags;
   DWORD             dwCompressionSize;
   GUID              gWIMGuid;
   USHORT            usPartNumber;
   USHORT            usTotalParts;
   DWORD             dwImageCount;
   RESHDR_DISK_SHORT rhOffsetTable;
   RESHDR_DISK_SHORT rhXmlData;
   RESHDR_DISK_SHORT rhBootMetadata;
   DWORD             dwBootIndex;
   RESHDR_DISK_SHORT rhIntegrity;
   BYTE              bUnused[60];
}
WIMHEADER_V1_PACKED, *LPWIMHEADER_V1_PACKED;
```

And the XML data holds the answer:

```
<WIM>
  <TOTALBYTES>121960277</TOTALBYTES>
  <IMAGE INDEX="1">
    <DIRCOUNT>526</DIRCOUNT>
    <FILECOUNT>3030</FILECOUNT>
    <TOTALBYTES>258581030</TOTALBYTES>
    <CREATIONTIME>
      <HIGHPART>0x01C6FE73</HIGHPART>
      <LOWPART>0x4ADB2D90</LOWPART>
    </CREATIONTIME>
    <LASTMODIFICATIONTIME>
      <HIGHPART>0x01C6FE73</HIGHPART>
      <LOWPART>0x5E8DBB1E</LOWPART>
    </LASTMODIFICATIONTIME>
  </IMAGE>
</WIM>
```

While dism.exe doesn't show this, the older imagex.exe does. If you grab a copy of imagex and run `imagex.exe /info <wimfile>`, you can easily dump this information. Reconstruct the HIGHPART LOWPART in a [FILETIME structure](http://msdn.microsoft.com/en-us/library/windows/desktop/ms724284(v=vs.85).aspx) and throw it at [FileTimeToSystemTime](http://msdn.microsoft.com/en-us/library/windows/desktop/ms724280(v=vs.85).aspx) and you'll find a date that exactly matches the one in Windows Deployment Services :)

\#TurnsOut, when a wim file is written to this date in the XML is automatically updated. I wrote a small tool that hijacks the call to get the current date time and swaps in whichever date you'd like to have saved. Check it out [over on github](https://github.com/dfct/WimXML).

Output from /?:

```
WimXML - Display and Replace the XML image information in WIM files.

Version: 0.0.1

   /wimfile <file> [/showxml | /savexml <file> | /replacexml <file>]

   /wimfile <file>           Path to the WIM file
      /showxml               Show the XML image info
      /savexml <file>        Save the XML image info to a file
      /replacexml <file>     Replace stored XML image info with file contents
      /usecreateasmodified   Replace the stored Modified times with Create times

Examples:
      wimxml.exe /wimfile mywim.wim /showxml
      wimxml.exe /wimfile "C:\myFolder\install.wim" /savexml C:\myXML.xml
```
