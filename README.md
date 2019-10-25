Xcode plugin
------------

This plugin adds the ability to call Xcode command line tools to automate build and packaging iOS applications (iPhone, iPad, ...).
*  More documentation about how to use it : https://wiki.jenkins-ci.org/display/JENKINS/Xcode+Plugin
*  Report all issues or features requests in Jira : https://issues.jenkins-ci.org/browse/JENKINS/component/16124

Contact the Jenkins Community by [mail](http://jenkins-ci.org/content/mailing-lists) or [irc](http://jenkins-ci.org/content/chat) to have support.

BLOCK{jenkins-plugin-info:pluginId=xcode-plugin\|jiraComponent=xcode-plugin\|adopt-message=The
maintainer is looking for a co-maintainer.}I need your help!!

To help us maintain the plugin, you can take contact to jerome.lacoste
*at* gmail.com in particular if you are able to test upcoming releases

Thank you!

A problem, An idea ?

*Please use our tasks and issues tracker to report bugs, improvements or
new feature.*

-   [Report a
    bug](https://issues.jenkins-ci.org/secure/CreateIssueDetails!init.jspa?Create=Create&components=16124&pid=10172&issuetype=1)
-   [Ask for a new
    feature](https://issues.jenkins-ci.org/secure/CreateIssueDetails!init.jspa?Create=Create&components=16124&pid=10172&issuetype=2)
-   [Ask for an improvement of an existing
    feature](https://issues.jenkins-ci.org/secure/CreateIssueDetails!init.jspa?Create=Create&components=16124&pid=10172&issuetype=4)

Also if you want to propose some code change using a Github pull
request, please open also a Jira issue. It is easier for developers to
track them.

Important!

We will soon release a new version of the Jenkins Xcode Integrate
Plugin.

In this version, compatibility with the current version is maintained.
However, major changes have been made to the handling of macOS
keychains.

Information to access the keychain is moved from configuration to
credentials.  
After that, the information for accessing the keychain must be
registered in the credentials.

BLOCK

This plugin adds the ability to call Xcode command line tools to
automate build and packaging iOS applications (iPhone, iPad, ...).

Table of Contents

#### Features

##### Versioning

This builder can invoke agvtool to dynamically update the
`CFBundleVersion` (a.k.a. Technical Version) or
`CFBundleShortVersionString` (a.k.a. Marketing Version). It supports all
macros from the . For example the value `${BUILD_NUMBER`} will be
replaced with the current build number. We advice you to generate a
unique value for each build for the `CFBundleVersion` if you want for
example deploy it into a private store. In that case you can use for
example : `${JOB_NAME}-${BUILD_NUMBER`}

##### Building

The target (optional), configuration (e.g. `Debug`, `Release`) and SDK
(optional) can be specified in the per-project config along with whether
to perform a clean before the build phase.

##### Packaging

The builder can be used to package the .app into a .ipa. Since a .app is
actually a directory, it can be awkward to work with and, especially,
distribute. We can simplify this by packaging the .app into a single
.ipa file, which is just a  
zip file with a well-known internal structure

##### Signing

This plugin has two features that help you simplify the code signing
process.

###### Allowing Jenkins to stage developer profile

This plugin builds on top of to allow you to store your Apple Developer
Profile (\*.developerprofile) file. [This file contains a code signing
private key, corresponding developer/distribution certificates, and
mobile provisioning
profiles](http://stackoverflow.com/questions/15154211/what-data-are-stored-in-developer-profile-file-exported-from-xcode).
You can [create this file from your
XCode](http://stackoverflow.com/questions/10404931/copying-keys-and-certificates-to-another-one-mac-for-iphone-development).

To upload the developer profile into Jenkins, go to "Manage Credentials"
in the system configuration page, and select "Apple Developer Profile"
from the "Add" button. See for further details.

To use this profile for signing, add "Import developer profile" as a
build step before you run xcode, and select the developer profile to
import into the build machine. This build step will install the
developer profile into the slave's keychain.

###### Work with existing populated keychains

If you create a dedicated keychain that stores the code signing private
key and certificate on every build slave, you can configure the plugin
to just unlock this keychain (so that xcode can use it for signing)

Unlocking

If you store your keys in the user keychain (most probably) and if you
run jenkins as daemon, make sure you installed a recent version of
jenkins for Mac, or configured the daemon to create a session (through
the CreationSession=true parameter in your org.jenkins-ci.plist daemon
config file). [See
also](http://stackoverflow.com/questions/9626447/unable-to-sign-ios-builds-with-jenkins)

Timeouts

The keychain has a default timeout of 300 seconds. One can override it
using the security set-keychain-settings command, which isn't supported
in the plugin today. Support in the plugin could be improved by adding
options to modify the timeout as part of the build process. See
[JENKINS-17184](https://issues.jenkins-ci.org/browse/JENKINS-17184).

##### Unit Testing

This plugin will listen to the xcodebuild output when running OCUnit or
XC tests and write out JUnit-format test reports that Jenkins can
understand. Jenkins will then use these to publish test failure reports
and graphs.

##### Sample of the configuration panel

#### Documentation

##### Installation guide

Obviously, the build machine has to be an OSX machine with XCode
developer tools installed.  
Certificates, Identities and Provisions must be installed on the build
machine separately.  
Just install the latest version of the plugin from the update center and
configure a freestyle job (see \#Usage Guide) that will use your OSX
node (If your central instance isn't running under OSX).  
If xcode related binaries aren't stored in the default location, update
the global configuration of the plugin (Manage Jenkins -\> Configure
System)  

  

If you want to use multiple versions of Xcode to build your application,
use ’Global Tool Configuration’ to register the distinguished name and
location of the installed Xcode.

##### Usage guide

###### Import developer profile step

By adding the Import developer profile step to Build, you can import
developer profiles exported to Xcode into temporary keychains or
existing keychains.  
The developer profile contains a set of certificates, secret keys and
provisioning profiles necessary for compiling and signing applications,
which is useful for distributing with multiple Jenkins nodes.

###### Import developer profile configuration parameters

| Parameter                     | Pipeline Parameter         | Since version | Description                                                                                                                                                                                                                                                                                                                                |
|-------------------------------|----------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Developer Profile             | profileId                  | 2.0.1         | Select the developer profile you exported from Xcode Preference to import into the keychain. Developer profile must be registered with Jenkins in advance by "Credentials". If you do not select this column, you can specify the identifier of the developer profile registered in "Credentials" with the next "Developer Profile ID".    |
| Import into existing Keychain | importIntoExistingKeychain | 2.0.6         | If it is checked, the developer profile will be imported into the existing keychain. If not checked, create a new key chain with the file name "jenkins-" + "job name" newly, and import the developer profile into this. (In this case, a random character string is automatically generated and used for the password)                   |
| Target keychain               | keychainName               | 2.0.6         | The name of the configured keychain to use to import certificates from developer profile to sign the package.                                                                                                                                                                                                                              |
| Keychain path                 | keychainPath               | 2.0.6         | Keychain path to import developer profile.                                                                                                                                                                                                                                                                                                 |
| Keychain password             | keychainPwd                | 2.0.6         | Password to unlock keychain importing developer profile.                                                                                                                                                                                                                                                                                   |

Limitations

The Xcode Integration Plugin has the function of importing the secret
key and certificate of the developer account into the temporary key
chain, but as the constraint of macOS can not handle different key chain
for each session, the job handling the key chain If you execute them in
parallel, this will not work properly.

As one of workarounds, it is impossible to limit the maximum number of
jobs around Jenkins' node to 1 and to execute multiple nodes with
different macOS accounts.  

###### Setting up a build step

Add the XCode build step to a free-style project and set the target
(e.g. `MyApp`), configuration (e.g. `Release`) and check the build IPA
and update version number options. This will give you a single,
versioned .ipa file. You can also select to clean the project before a
build; while this will make sure that the project is starting from a
clean state, it will make large projects take a lot longer.

###### Setting up a unit test step

Add the XCode build step and this time specify your unit test target
(e.g. `MyAppTests`), configuration (e.g. `Debug`) and the SDK (e.g.
`Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator4.1.sdk/`).  
Leave all the checkboxes unselected. The SDK needs to be supplied here
as the unit tests will only be run when building under the simulator's
SDK.  
Check the "Publish JUnit test result report" option in the project
config and set the value `"*``/test-reports/``*.xml"` under "Test report
XMLs". This will tell Jenkins to pick up the JUnit-format test reports.

###### XCode Builder configuration parameters

[TABLE]

###### Setting up a Export IPA step

If you add the Export IPA step and set up the information necessary for
exporting the IPA file, the location of the archive, the export method,
code signature information, etc., you can export the IPA from the
archive output in the Xcode Build step.

###### Export IPA configuration parameters

[TABLE]

#####  Using multiple versions of xcode.

One can easily support multiple versions by using environment variables.
But first one need to install multiple versions on the build server.
Here's one strategy:

###### install multiple versions of xcode

0\. Note current config.

xcode-select -p

1\. download xcode DMGs from <http://developer.apple.com/downloads/>

2\. enable install from everywhere (under System preferences / Security)

Without that I had problems installing xcode, without graphical
feedback, tested on 10.9.5, freshly booted.

Symptoms were:

-   app not responsive
-   Getting errors like in /var/log/system.log

Someone attempted to start application App:"Xcode" asn:0x0-1b62b61
pid:57656 refs=6 @ 0x7fa5d9f6df40 but it still has
\_kLSApplicationLockedInStoppedStateKey=true, so it is is staying
stopped. : LASApplication.cp \#2468 SetApplicationInStoppedState()
q=LSSession 100027/0x186bb queue

3\. open DMG file, copy app to /Applications. This might take a while.

4\. close /Volumes/Xcode (otherwise installation won't start)

5\. start Xcode, accept agreement, install missing packages if necessary.
This might take a while. Close xcode after GUI opens.

6\. move freshly installed Xcode to new path, e.g.

sudo mv /Applications/Xcode.app /Applications/Xcode6.1.app

(I use this so that the xcode app appears with version number under
spotlight)

7\. reset default xcode-select if necessary (installation might have
changed it / reset to /Applications/Xcode.app)

sudo xcode-select -s /Applications/Xcode6.1.app

(Note the part above could be fully automated....)

###### Select the xcode version at runtime

1\. Use EnvInject plugin  
2. for jobs that require the non default do something like

DEVELOPER\_DIR=/Applications/Xcode6.0.1.app/Contents/Developer

under Inject environment variables to the build process

###### Select the xcode version at runtime (Version 2.0.3 or later)

1. Register the distinguished name and location of the installed Xcode
using ’Global Tool Configuration’.  
2. Select Xcode registered in 'Global Tool Configuration' in Job's
'Xcode Tools Version'.

That's it.

#### FAQ

##### User interaction is not allowed

When code-signing, a prompt may appear on the build machine asking
whether to allow keychain access.  
This will block the build with a message like
`"User interaction is not allowed"` until it is dismissed.  
Just select 'Always Allow' the first time and it shouldn't need to ask
again.  

If this prompt is not showing on the build machine, you can force it to
appear by running the codesign command that failed from a terminal on
the build machine:   /usr/bin/codesign --force --sign "iPhone
Distribution: .....

##### No Xcode folder is set

\[sources\] $ /usr/bin/xcodebuild -version xcode-select: Error: No Xcode
folder is set. Run xcode-select -switch \<xcode\_folder\_path\> to set
the path to the Xcode folder. Error: /usr/bin/xcode-select returned
unexpected error. FATAL: Check your XCode installation. Jenkins cannot
retrieve its version.

You probably just installed XCode (by default it is in `/Developer`) but
its path isn't configured. You can fix it with

xcode-select -switch /Developer/

You should now be able to execute `xcodebuild`. Ex :

host:\~ user$ /usr/bin/xcodebuild -version Xcode 4.2 Build version 4C199

##### Xcode 4.3

XCode 4.3 changes [2
things](https://developer.apple.com/library/mac/#documentation/DeveloperTools/Conceptual/WhatsNewXcode/Articles/xcode_4_3.html)
that are important for our usage on a slave or a master :

-   **Command-Line Tools Are Optional** : The command-line tools are not
    bundled with Xcode 4.3 by default. Instead, they have to be
    installed using the Components tab of the Downloads preferences
    panel  
-   **/Developer No Longer Exists** : You may have to reconfigure its
    path like this:

    sudo xcode-select -switch
    /Applications/Xcode.app/Contents/Developer/

#### Changelog

##### Version 2.0.12 (27th May 2019)

-   ##### Allow to unlock/lock keychain on demand. ([JENKINS-56909](https://issues.jenkins-ci.org/browse/JENKINS-56909))

-   Modified to encrypt the keychain password. ([PR
    \#102](https://github.com/jenkinsci/xcode-plugin/pull/102))
-   Fix display bug developer profile credentials and more. ([PR
    \#103](https://github.com/jenkinsci/xcode-plugin/pull/103))

##### Version 2.0.11 (19th Feb 2019)

-   ##### Added a function to obtain the status of the test from the outline of the test result 'TestSummaries.plist'.

##### Version 2.0.10 (19th Nov 2018)

-   ##### Fix broken Xcode Project Parser. ([JENKINS-54414](http://JENKINS-54414), [JENKINS-54113](https://issues.jenkins-ci.org/browse/JENKINS-54113))

##### Version 2.0.9 (2nd Nov 2018)

-   ##### Added a option to use the 'Legacy Build System' instead of 'New Builld System' which became available from Xcode 9.

##### Version 2.0.8 (10th Oct 2018)

-   Corresponds when there is no DEVELOPMENT\_TEAM entry in the old
    Xcode project.
-   Delete unnecessary error messages.
-   When copying the provisioning profile from the project location, it
    needs to be done before Xcode compilation, so it is fixed.
-   Fixed a bug that error message got mixed when parsing provisioning
    profile and analysis failed.

##### Version 2.0.7 (20th Sep 2018)

-   Added the ability to copy provisioning profile files to manual code
    signing.
-   Change the help for importing developer profile to more detailed
    one.
-   Enhance backward compatibility.
-   Added a function to retrieve information necessary for CodeSign from
    Xcode project.

##### Version 2.0.6 (16th Aug 2018)

-   Fixed a bug that key chain was not unlocked properly when importing
    developer profile to keychain.
-   Added input validation when setting up developer profile loader.
-   Added input validation when setting up export IPA.
-   Fix some bugs.

##### Version 2.0.5 (9th Aug 2018)

-   More compatibility for Pipeline.
-   Added function to import developer profile into existing keychain.
-   Added a function to set exportOptions.plist an option for deleting
    Swift symbols when exporting IPA.

##### Version 2.0.4 (22th Jun 2018)

-   I will display an error message if the developer profile is not
    loaded.

##### Version 2.0.3 (18th Jun 2018)

-   Implemented the ability to export IPA files from already compiled
    archives.
-   [JENKINS-50266](https://issues.jenkins-ci.org/browse/JENKINS-50266)
    : Fix custom xcodebuild arguments not passed through to export
    archive step.
-   [JENKINS-51418](https://issues.jenkins-ci.org/browse/JENKINS-51418)
    : Fix to always be able to use developer team ID as a parameter
    regardless of automatic code signature.
-   Fix the Plugin is abnormally terminate if the out of order of lines
    from Xcodebuild output is changed.
-   Added a function to retrieve necessary information for code
    signature from compiled archive.
-   Support multiple versions of Xcode without using 'EnvInject Plugin'.

##### Version 2.0.2 (30th Apr 2018)

-   We released again due to infrastructure related problems, but the
    contents are the same as Ver 2.0.1

##### Version 2.0.1 (26th Apr 2018)

-   Supports Xcode 9 ([PR
    \#86](https://github.com/jenkinsci/xcode-plugin/pull/86),
    [PR\#87](https://github.com/jenkinsci/xcode-plugin/pull/87), [JENKINS-47744](https://issues.jenkins-ci.org/browse/JENKINS-45509), [JENKINS-45509](https://issues.jenkins-ci.org/browse/JENKINS-45509))
-   Adding Pipeline support for importDeveloperProfile and adding
    symbols ([PR
    \#89](https://github.com/jenkinsci/xcode-plugin/pull/89))

##### Version 2.0.0 (24th May 2017)

-    The plugin now requires Java 7 and Jenkins core \>= 1.625.1

-    The plugin now requires Xcode \>= 7

-   New : Supports Xcode 7/8
    ([PR\#76](https://github.com/jenkinsci/xcode-plugin/pull/76),
    [PR\#78](https://github.com/jenkinsci/xcode-plugin/pull/78), [JENKINS-44203](https://issues.jenkins-ci.org/browse/JENKINS-44203){.issue-link}, [JENKINS-44151](https://issues.jenkins-ci.org/browse/JENKINS-44151){.issue-link}, [JENKINS-43226](https://issues.jenkins-ci.org/browse/JENKINS-43226){.issue-link}, [JENKINS-43163](https://issues.jenkins-ci.org/browse/JENKINS-43163){.issue-link}, [JENKINS-38799](https://issues.jenkins-ci.org/browse/JENKINS-38799){.issue-link}, [JENKINS-38777](https://issues.jenkins-ci.org/browse/JENKINS-38777){.issue-link},
    [JENKINS-34307](https://issues.jenkins-ci.org/browse/JENKINS-34307){.issue-link}) 
    -   Replace 'xcrun PackageApplication' by 'xcodebuild
        -exportArchive' to package ipa

    -   New Development Team parameter which can be configured globally
        in Jenkins Global settings or locally at the project level

-   New : Adds regex for parsing UI test failures (for Xcode 8)
    ([PR\#75](https://github.com/jenkinsci/xcode-plugin/pull/75), [JENKINS-40938](https://issues.jenkins-ci.org/browse/JENKINS-40938){.issue-link})

-   New : Adds support
    ([PR\#74](https://github.com/jenkinsci/xcode-plugin/pull/74), [JENKINS-42457](https://issues.jenkins-ci.org/browse/JENKINS-42457){.issue-link}, [JENKINS-33355](https://issues.jenkins-ci.org/browse/JENKINS-33355){.issue-link})

##### Version 1.4.11 (21th September 2016)

Because XXXX happens

-   Fix: Support configurable ID for developer profile (JENKINS-32987)

##### Version 1.4.10 (20th September 2016)

-   New: Support configurable ID for developer profile (JENKINS-32987)
-   New: Allow the option not to sign the IPA with xcrun (JENKINS-32370)
-   Fix: use textarea for Xcode Build Arguments (JENKINS-30228)
-   Fix: Generating an archive builds the project twice (JENKINS-30362)
-   Fix: Allowed the use of environment variables in plist url
    (JENKINS-27236)

##### Version 1.4.9 (23rd September 2015)

-   Fix: only prepend -allTargets for -project builds (JENKINS-28256)
-   New: Add support for Xcode 7 date format in Unit Testing build task
    (pull request xcode-plugin/63)

##### Version 1.4.8 (28th February, 2015)

-   Fix: Add timeout to xcodebuild -list to avoid hang on xcode 6 (pull
    request xcode-plugin/58)
-   New: Support multiple targets to be passed into xcodebuild (pull
    request xcode-plugin/43)

##### Version 1.4.7 (5th January, 2015)

-   Fix: XCTest output parsing: support nested/namespaced test suites
    (JENKINS-26295)
-   New: XCTest output parsing: handle tests suites exiting with an
    error (pull request xcode-plugin/54)

##### Version 1.4.6 (16th December, 2014)

-   Fix: broken dSYM packaging (since 1.4.5)
-   Fix: BUILD\_DATE does not produce the correct last modified date if
    built on a slave machine (pull request xcode-plugin/50)
-   Fix: make sure to set provideApplicationVersion properly upon
    upgrade from per-1.4.1 (JENKINS-26027)
-   New: developerProfileLoader: use show-keychain-info to display job
    specific keychain information
-   New: support XC test output (JENKINS-19955)
-   New: display warning when simulator SDK selected and IPA about to be
    packaged (JENKINS-21293)
-   New: document xcodebuildArguments (JENKINS-13930)

##### Version 1.4.5 (10th December, 2014)

-   Fix: only zip the DSYM if the DSYM file was actually created and
    fail the build if the operation failed
-   Fix: Fail the build if we fail to create an ipa.
-   Fix: "keychain with the same name already exists" (JENKINS-22130)
-   New: XCode 6 compatibility (pull request xcode-plugin/48)
-   New: generate\_manifest added ability to generate enterprise
    distribution manifest plist (pull request xcode-plugin/45)
-   Fix: getKeychain returns a global keychain preferred over path (pull
    request xcode-plugin/41)
-   New: Ability to change the Bundle identifier (CFBundleIdentifier)
    for an xcode build (pull request xcode-plugin/39)

##### Version 1.4.2 (31st December, 2013)

-   Fix: Avoid NPE on plugin version update (JENKINS-19886, pull request
    xcode-plugin/37)

##### Version 1.4.1 (30th October, 2013)

-   Fix: performance regression in output parsing (JENKINS-20037, pull
    request xcode-plugin/36)

##### Version 1.4 (2nd Oct, 2013)

-   New: added an option to produce .xcarchive (JENKINS-14719)
-   Fix: Fixing show-keychain-info call (JENKINS-xcode-plugin/30)
-   New: Build wrapper to restore the keychain at the end of the build
    (pull request xcode-plugin/31)
-   New: Adding the possibility to provide a pattern for the .ipa file
    name (pull request xcode-plugin/33)
-   New: Added an ability to import \*.developerprofile into a build
    (pull request xcode-plugin/34)
-   New: UI update: group options into 3 categories (pull request
    xcode-plugin/28)
-   New: Introduced the ability to create global keychain configurations
-   New: Add new option "allow failing build results" (pull request
    xcode-plugin/25)
-   Fix: xcodebuild output parser is more lenient (pull request
    xcode-plugin/19)
-   Fix: Can't add xcodebuild parameters including whitespace
    (JENKINS-12800)
-   New: Add a field for entering code signing identity (pull request
    xcode-plugin/6)

##### Version 1.3.1 (27th March 2012)

-   Fix: Custom xcodebuild arguments values are not persisted
    (JENKINS-12510).

##### Version 1.3 (20th January 2012)

-   New : Ability to specify custom arguments to xcodebuild (so that
    values in project file can be overridden). It is needed to specify
    custom build options to make in-app unit tests work.
-   New : Ability to disable clean up of test reports on per-target
    level, so that it is possible to run several targets in single job
    and not mess-up test reports.
-   Fix : The plugin fails by searching for a double .app extension
    while compressing .dsym (JENKINS-12273)
-   Fix : The plugin fails to delete previous generated IPA results in a
    failed build (JENKINS-12237).
-   Fix : The plugin fails to set default keychain when using an
    alternate (non login) keychain (By default the login keychain is the
    default keychain) (JENKINS-12217).
-   Fix : Restore Java 1.5 runtime compatibility. (JENKINS-12378)

##### Version 1.2.2 (19th December 2011)

-   Fix : Build IPA fails if the plugin is launched on a remote agent
    using a relative path for its FS Root directory (JENKINS-12144)

##### Version 1.2.1 (18th December 2011)

-   Fix : Build IPA switch doesn't work properly for a default Xcode
    project always results in FATAL error (JENKINS-12089)

##### Version 1.2 (8th December 2011)

-   New build parameter to specify the build output directory. This
    overrides the setting in the user's project file, so it will be put
    into a known directory rather than XcodeDerivedData, etc. This makes
    it much easier to set up subsequent build steps in Jenkins that do
    something with the output of the build. It does this by passing the
    setting CONFIGURATION\_BUILD\_DIR to xcodebuild if a path is set for
    this new job config value.
-   Add support for building schemes and workspaces : The plugin now
    supports two extra configuration parameters `xcodeScheme` and
    `xcodeWorkspaceFile`. The scheme maps to xcodebuild's parameter
    "-scheme" and the workspace to "-workspace". The scheme takes
    precedence over the target setting and the workspace takes
    precedence over the project setting.

##### Version 1.1 (29th November 2011)

-   Upgrade the token macro plugin to version 1.5.1 to support
    environment and build variables (JENKINS-11892)
-   New configuration parameter to set the SYMROOT value passed to the
    build. This parameter accepts environment and build variables and
    Macros (JENKINS-11813)
-   Various code cleanup and improvements.

##### Version 1.0.1 (14th November 2011)

-   Minor fix about the default value and the documentation of the
    keystore path parameter.

##### Version 1.0 (14th November 2011)

-   Initiated from [Ray Yamamoto Hilton's Xcode plugin for
    Hudson](http://rayh.com.au/xcode-hudson-plugin) with few changes :
    -   It improves jenkins compatibility, and is available in its
        update center.
    -   It uses the to configure values of `CFBundleVersion` and
        `CFBundleShortVersionString`
    -   It allows to configure the keychain to use and to unlock it
    -   It is ready for internationalization

If you previously used the version provided by Ray you should be able to
test this one in parallel as it has a new identity. Also you'll have to
reconfigure a large part of it if you want to upgrade thus don't forget
to save your settings.

#### Known issues

true

