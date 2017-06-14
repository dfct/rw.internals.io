---
layout: post
title: "Bullets on [mobile] testing tools"
tags:
- automation
- testing
- mobile
---


Mid last year, I spent a weekend looking into what testing tools were out there and if/how they supported mobile. I planned to take the research and turn it into something (perhaps at automate.qa, a domain I'd picked up) but never blocked some time to do so. Instead, this post contains the list as I had it back then.

Bear in mind I originally collected these as quick notes to myself, without intention to publish them as is. I think they're helpful as a loose survey of what's available tool-wise, but less so as an exact description of any specific tool.

- - - -

**Browser and Native Testing**

Sikuli &#124; SikuliX - [http://www.sikuli.org](http://www.sikuli.org/) &#124; [http://www.sikulix.com](http://www.sikulix.com/)
* UI driven testing powered by Selenium-like framework
* Works via image recognition, supports anything that can be seen
* No direct mobile support unless emulated or otherwise displayed on a desktop

Applitool eyes - [http://applitools.com](http://applitools.com/)

* Automated visual testing with major browsers including mobile and native apps
* Integrates with selenium, Appium, etc.

eggPlant - [http://www.testplant.com/eggplant/](http://www.testplant.com/eggplant/)

* Image-based Selenium-style software
* Supports all mobile platforms, physical and emulated

Keynote - [http://www.keynote.com/solutions/testing/mobile-testing](http://www.keynote.com/solutions/testing/mobile-testing)

* Formerly known as Keynote Device Anywhere
* GUI-created commands to automate mobile devices (only, no browser)
* Has a Java API but primarily focused on GUI script creation
* Supports text, image, or object based recognition
* Supports all mobile platforms
* Supports physical devices locally or in cloud
* No programming knowledge required

Expirtest SeeTestAutomation - [http://www.experitest.com/automation/](http://www.experitest.com/automation/)

* UI driven testing powered by Selenium-like framework
* Works via native properties or image recognition
* Supports all mobile devices (iOS, Android, Windows Phone, BB)
* Works with physical devices or emulators and does not require a library
* Can have a local 'cloud' to test via

Selenium/Appium - [http://www.seleniumhq.org](http://docs.seleniumhq.org/docs/03_webdriver.jsp#selenium-webdriver-s-drivers) / [www.appium.io](http://www.appium.io) 

* Drivers exist for browsers (Selenium) and iOS and Android (Appium)
* [Selendroid](http://selendroid.io/) provides Android native and web app support
	* Selendroid can be used on physical or emulated devices
	* Physical devices are via USB, does not require app to be linked against a library
	* Leverages Android instrumentation framework

* [iOS-driver](http://ios-driver.github.io/ios-driver/) provides iOS native and web app support
	* iOS-driver can be used on physical or emulated devices
	* Does not require jailbreak or change to the source

* [Appium](http://appium.io/) provides Android and iOS native and web app support
	* Does not require changing source
		* API is 'cross-platform', tests can work against both platforms
		* Supports physical devices
		* Uses Selendroid for testing on older 2.3+ but <4.2 Android

Calabash - [http://calaba.sh/](http://calaba.sh/)

* Automation library for iOS and Android backed by Xamarin
* Similar to Selenium WebDriver but strongly mobile oriented (gestures)
* Requires small server to be compiled into app
* Uses [Cucumber](http://cukes.info/) BDD
* Xamarin Test Cloud can be used to test on a ton of real devices

AppThwack - [http://www.appthwack.com](http://www.appthwack.com/)

* Provides cloud testing on almost 300 physical mobile devices
* Upload your app & tests that use any major framework

TestObject - [https://testobject.com/](https://testobject.com/)

* Cloud testing on physical android devices
* Manual testing service via remote control in a browser
* Supports native and web apps

Mobitaz - [http://www.msys-tech.com/mobitaz/](http://www.msys-tech.com/mobitaz/) and [PDF](http://www.msys-tech.com/mobitaz/downloads/Mobitaz-brochure.pdf)

* Android automation tool, Selenium IDE style
* Supports testing on physical devices
* Does not appear to support direct development of tests

Scirocco cloud - [http://www.scirocco-cloud.com/](http://www.scirocco-cloud.com/)

* Android testing, both manual and automated
* Supports testing on physical devices
* Uses Google's NativeDriver and Selenium's WebDriver
* Pretty small shop, based out of Japan

Perfecto MobileCloud - [http://www.perfectomobile.com/](http://www.perfectomobile.com/Products/MobileCloud_Automation)

* Cloud testing on real devices, supports manual and automated testing
* iOS and Android devices, native and web apps
* Test Scripts can run across iOS and Android
* Supports web objects or image-based for native apps

Jamo M-eux (Mobile End User Experience) - [http://jamosolutions.com/](http://www.jamosolutions.com/product/test-automation/)

* Automation of native or web apps on all mobile platforms
* Selenium-IDE style framework can record tests, play them back
* Supports Jamo cloud or on premise cloud
* Integrates with everything ever, MTM, Visual Studio, Eclipse, HP QTP
* Supports physical devices and emulators, requires an installed agent
* Requires linking against M-eux library

Ranorex - [http://www.ranorex.com](http://www.ranorex.com/)

* Selenium-style software with programming language & recorder
* Supports native and web apps
* Supports object recognition for a huge number of native app languages
* Supports image-based recognition
* Requires linking against their library

TestComplete - [http://smartbear.com/](http://smartbear.com/products/qa-tools/automated-testing-tools/)

* Supports desktop web & native desktop & mobile devices
* Object recognition for web & native iOS and Android apps
* Supports writing scripts or recording tests
* Does not need to be compiled into app

Telerik Test Studio - [www.telerik.com/mobile-testing](http://www.telerik.com/mobile-testing)

* JavaScript-based test language, supports native and web apps
* Language is platform independent
* Supports physical and emulated devices
* Requires linking against Telerik library

MonkeyTalk - [https://www.cloudmonkeymobile.com](https://www.cloudmonkeymobile.com/)

* Automated testing framework for native and web mobile apps
* Supports real devices and emulated devices
* Selenium-style language and recorder
* Does not need to be compiled into app
* Supports native gestures and object recognition
* Supports Java, JavaScript, or MonkeyTalk languages

- - - -

**Browser Testing**

Testomato - [http://www.testomato.com/](http://www.testomato.com/)

* High Level Automated Testing
* Great for smoke and regression tests
* No programming knowledge required

Ghost Inspector - [https://ghostinspector.com/](https://ghostinspector.com/)

* Chrome extension to create automated tests
* Tests run in the Ghost Inspector's cloud on webkit,
* No mobile support
* Tests can be exported for use in Selenium
* No programming knowledge required, though has an API

Fake - [http://www.fakeapp.com](http://www.fakeapp.com/)

* OS X Browser for web testing
* Analogous to Selenium IDE
* No programming knowledge required

SahiPro - [http://www.sahipro.com](http://www.sahipro.com/)

* Selenium-like framework, requires injected JavaScript
* Can run on mobile devices due to the injected JavaScript

Wetator - [http://www.wetator.org/](http://www.wetator.org/)

* Selenium-like framework, requires Java / HtmlUnit
* Does not support mobile devices nor true browsers
Testling - [http://www.testling.com](http://www.testling.com/)

* Automated testing via JavaScript tests
* Minimal mobile support

Ghostlab - [http://www.vanamco.com/ghostlab/](http://www.vanamco.com/ghostlab/)

* Synchronized testing across multiple devices and browsers
* Works via injected JavaScript
* Supports Android 2.3+ and iOS

Browserling - [http://www.browserling.com](http://www.browserling.com/)

* 'VNC-to-Browser' to test all desktop browsers from inside of one
* No mobile support

BrowserStack - [http://www.browserstack.com](http://www.browserstack.com/)

* Browser testing via real and emulated desktop_mobile browsers. Very wide range of browser_OS support
* Supports Selenium
* Native app testing is still "on the roadmap"

Spoonium - [http://www.spoonium.net](http://www.spoonium.net/)

* Virtual Machine / docker-esque service, supports selenium testing
* No mobile support

Browser Studio - [http://www.browserstudio.com](http://www.browserstudio.com/)

* Provides real browsers from Spoon.net virtual machines
* Supports plugins and extensions
* No mobile support

TestingBot - [http://www.testingbot.com](http://www.testingbot.com/)

* Desktop and Mobile browser testing with Selenium
* Native mobile app testing via Appium (uses emulators)
* Effectively a Selenium grid service provider

Adobe Edge Inspect - [http://creative.adobe.com/products/inspect](http://creative.adobe.com/products/inspect)

* Displays websites on multiple devices simultaneously
* Similar to Ghostlab but only with display & navigation, no interaction otherwise

SauceLabs - [https://saucelabs.com/](https://saucelabs.com/)

* Cloud testing of websites against major OS/browser combos
* Supports automated testing with Selenium and Appium

- - - -

**Android Testing (Coding)**

Android JUnit - [http://developer.android.com/tools/testing/testing_android.html](http://developer.android.com/tools/testing/testing_android.html)

* Java Unit testing framework supported by Google

UIAutomator - [http://developer.android.com/tools/testing/testing_ui.html](http://developer.android.com/tools/testing/testing_ui.html)

* Android UI testing framework supported by Google

Robotium - [https://code.google.com/p/robotium/](https://code.google.com/p/robotium/)

* Native and hybrid testing, full black-box UI testing support
* Has a recorder as well

Expresso - [https://code.google.com/p/android-test-kit/wiki/Espresso](https://code.google.com/p/android-test-kit/wiki/Espresso)

* Native and hybrid testing, full black-box UI testing support

- - - -

**iOS Testing (Coding)**

XCTest - [http://developer.apple.com](https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/testing_with_xcode/Introduction/Introduction.html)

* Unit Testing framework built into Xcode 5+
* Numerous Community extensions like Specta

FBSnapshotTestCase - [https://github.com/facebook/ios-snapshot-test-case](https://github.com/facebook/ios-snapshot-test-case)

* Generate saved images of UIView renders and 'test' source code against them

KIF Keep It Functional - [https://github.com/kif-framework/KIF](https://github.com/kif-framework/KIF)

* Leverages XCTest to allow driving via Xcode Test Navigator
* Leverages accessibility attributes to simulate user interaction
* Requires dev builds linked against KIF library

Apple UI Automation Javascript Library - [http://developer.apple.com](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/UsingtheAutomationInstrument/UsingtheAutomationInstrument.html)

* Leverages accessibility attributes to simulate user interaction
* Selenium-like framework
* Uses Instruments and the iOS Simulator

Frank - [http://www.testingwithfrank.com/](http://www.testingwithfrank.com/)

* Compile small server into application, can then run test scripts
* Can test in emulators or on a physical device
* Uses [Cucumber](http://cukes.info/) BDD
