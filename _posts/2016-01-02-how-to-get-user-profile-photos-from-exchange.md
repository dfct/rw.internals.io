---
layout: post
title: "How to Get User Profile Photos From Exchange"
tags:
- useraccounts
- exchange
---


An update, there is a newer API available if it is an API you seek: [https://msdn.microsoft.com/en-us/office/office365/api/photo-rest-operations](https://msdn.microsoft.com/en-us/office/office365/api/photo-rest-operations)

The method below is still useful if you're just looking for a URL to hit. Cheers [@elryry](https://twitter.com/elryry)

- - - -

(or Lync or Outlook)
I needed to acquire in bulk the user profile photos for my team. Quite clearly Outlook and Lync were able to find and download them, but I didn't see any easy means to pull them from Outlook nor its data files, and Lync seemed to only cache arbitrary photos in my local app data folder:

`%localappdata%\Microsoft\Office\15.0\Lync\sip_<your sip>\Photo`

A bit more digging and I found a easy method - just ask Exchange for them! The following URL will grab the highest resolution user profile photo available for a given email address:

`https://mail.yoursite.com/EWS/Exchange.asmx/s/GetUserPhoto?email=yourmail@yoursite.com&size=HR648x648`

Plug in your Exchange server address, append the _EWS_[...] Exchange Web Services call with the email address in question, and you're set! In my case, that and a for loop rolling through a list of emails knocked things out.

More info on this call here: [https://msdn.microsoft.com/en-us/library/jj194329.aspx](https://msdn.microsoft.com/en-us/library/jj194329.aspx)
