# IOS-Statick-Analysis

Reference : https://www.slideshare.net/securitylearnwordpress/hacking-and-securing-ios-applications

Written on Objective-c language 
IPA file structure

#JailbrokenBypassApps
tsProtector 8+
TwekRestrictor
A-Bypass tweak
JailProtect
UnSub
Shadow

https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning
https://www.guardsquare.com/en/blog/iOS-SSL-certificate-pinning-bypassing
https://medium.com/@appmattus/android-security-ssl-pinning-1db8acb6621e

1>Sensitive Informaiton in Info.plist[App's Info.plist property list]
#About
It is an XML file, that is used to store basic key-value data.
It contains information about IOS project.
Designed to store user's properties and configuration information.
But apps store user data,names,password,session info.

Ex: contains below informaiton
App name for diaplsy --> CFBundleName [IOS uses it to display your app’s name on an iPhone’s home screen]
App Package name --> CFBundleIdentifier [com.knolskape.aurora]
App version  --> CFBundleShortVersionString

#Test
1.Install ipa file in device and use it completely.
2.Exract data using imaging tool.
3.Now extract data from app.imazing file.
4.look for info.plist in Container folder.
5.open file using plist reader tool.

#Mitigation
Avoid storing any sensitive information in this file, is require use strong encryption technique

2>NSAllowsArbitraryLoads set to True
#About
App Transport Security overview[ATS]
Defnition: A Boolean value indicating whether App Transport Security restrictions are disabled for all network connections.
Default : No/False [protection enabled]

If it is set to True or Yes check other attributes[Exception domains]
If set to yes its protection is disabled
#Test
1.Get app ipa file
2.Extract the content
3.Navigate to Payload directory and search for Info.plist file.
4.Open plist file and look for NSAllowsArbitraryLoads key

NSAllowsArbitraryLoads
NSAllowsArbitraryLoadsForMedia
NSAllowsArbitraryLoadsInWebContent
NSExceptionAllowsInsecureHTTPLoads
NSExceptionMinimumTLSVersion
#Mitigation
Enable the ATS feature by setting the flag to No.
#Reference
https://www.nowsecure.com/blog/2017/08/31/security-analysts-guide-nsapptransportsecurity-nsallowsarbitraryloads-app-transport-security-ats-exceptions/


3>Cookies.binarycookies
#About
Binary file to store cookies.
iOS applications store the persistent cookies in Cookies.binarycookies file.
Most iOS apps does not prompt the user for login every time so use persistant cookie for further commni.


#Test
1.Install ipa file in device and use it completely.
2.Exract data using imaging tool.
3.Now extract data from app.imazing file.
4.look for Cookies.binarycookies in Container folder[Container/Library/Cookie].
5.Using BinaryCookieReader.py tool read the file.

#Mitigation
For the critical app don't create persistant cookie.
#Reference
http://www.securitylearn.net/2012/10/27/cookies-binarycookies-reader/

4>Keychain data of the application can be dump
#About
Sqlite data base for sensitive information
Secure place to store keys and passwords.
Located at /var/KeyChains/keychain-2.db
Keychain encryption is tied to device and protected entries are tied to user's passcode.

#Test
1.Install app in jailbroken device
2.use keychain viewer- by sogeti tool to view data.

#Mitigation
Use custome encryption to store data in keychain also.

5>Screenshot [Sensitive info in snapshot]
#About
Home button shrinks your application with a nice effect.
iOS takes screenshot of the app to create that effect and stores it in Library/Caches/Snapshots folder.

#Test
Click home button.

#Mitigation
Change the screen or prevent app running in back-groud by making changes in info.plist file. "Application does not run in backgroud"


6>Run Time analysis
#About
*Address Space Layout Randomization --> [Protection against Memory curroption vulnerabilities]
Randomizes location of objects in memory. Ex: location of binary,libraries,dynamic linker,stack and heap memory addresses are all randomized. 
the application should be compiled with the -fPIE –pie flag 
*Stack Smashing Protection --> [Protection against stack overflow attack]
the application should be compiled with the –fstack-protector-all flag
*Automatic Reference Counting --> [Protection against Memory curroption vulnerabilities]
ARC can be enabled in an application within XCode by setting the compiler option “Objective-C Automatic Reference Counting” to “yes”. By default it is marked as “yes”.
Automatic Reference Counting (ARC) is a memory management system that handles the reference count of objects automatically at compile time, instead of leaving this task to the developer. This feature was introduced with iOS 5, but it can be backported to previous versions because the operations are performed at compile time.

3 runtime protection features, additional layer of security for applicaiton

#Test
Mac OS
Otool –Vh ApplicaitonBinary[Applicaiton Name]  [PIE FLag]
Otool –I –v ApplicationBinary[Applicaiton Name] | grep stack
Otool –I –v ApplicationBinary[Application Name] | grep _objc_release

Linux --> Jtool

https://resources.infosecinstitute.com/penetration-testing-for-iphone-applications-part-5/#gref
https://books.nowsecure.com/secure-mobile-development/en/ios/implement-protections-against-buffer-overflow-attacks.html

7>Source Code not ecrypted
#Test
Otoo –l ApplicationBinary | grep crypt

Will find out solution fir this then will report.


8>Project.properties file
#About
Use to store basic information regarding project. like Appname,Company identifier,version,supported device range,language of app.

Open the ipa file in Xcode to 

9>Keyboad Cache
#About
iPhones record everything that a user types.
Designed to auto complete the predictive common words.
Located at -/var/mobile/Library/Keyboard/en_GB-dynamic-text.dat 
			

Secure fields and all digits are not stored.
Data typed in text fields are cached [usernames, security ans, profile details].

#Test
Navigate to path
open files use hex editor
#Mitigation
Disable auto complete of all text fields[ possible sensitive fields].
*mark the field as secure 
mytextField.secureTextEntry = Yes
*Disable auto cocrrection
mytextField.autocorrectionType = UITextAutoCorrectionTypeNo;

10>Investigating Local Storage 
Check app data directory /var/mobile/Containers/Data/Application/<app Bundle>/
.db -- using sqlite 
plists 

11>check file changes
before install run below cmd and save the result
ls -lR --full-time 
after install and usage of app run same cmd and save the result
sdiff -s o1 o2
check manipulated files

12>Check Cache files
/User/Library/Caches/\*/cache.db
/Library/Caches/\*/cache.db

13>SnapShots
/private/var/mobile/Containers/Data/Application/A800C66B-F722-437C-AFB7-5DFFAEC20F86/Library/Caches/Snapshots

14>Read NSLog
Logs
/var/log/syslogs --> erros,reqresp
Or use console app in MacOs  to view the syslogs

https://github.com/benvium/libimobiledevice-macosx/blob/master/README.md

#ApplicationBInary.app Directory location
/private/var/containers/Bundle/Application
cat */* | grep app

https://www.slideshare.net/OWASP_Poland/introduction-to-ios-penetration-testing

https://spaceraccoon.dev/low-hanging-apples-hunting-credentials-and-secrets-in-ios-apps
https://spaceraccoon.dev/from-checkra1n-to-frida-ios-app-pentesting-quickstart-on-ios-13
https://www.allysonomalley.com/2018/12/20/ios-pentesting-tools-part-3-frida-and-objection/
https://medium.com/@yogendra_h1/ios-application-security-jailbreak-12-4-5e3fc0dc0726
