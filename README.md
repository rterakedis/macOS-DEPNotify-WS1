# macOS-DEPNotify-WS1

DEPNotify is a great splash screen program to notify macOS users during the build process about whats happening on their system. I've decided to use this time to
give a little history lesson about our company and while installing applications in the background. All credit for this program goes to Joel Rennich and the folks
that help support it on the macadmins slack channel. I wanted to create a proper workflow for those folks that use Workspace One as an MDM. Heres an 18 minute video of the script in use and a brief explanation
of the process https://drive.google.com/file/d/1NQJsglx5Mm4qKh04rpUkT9ymXAfQD8un/view


# The main application - DEPNotify

DEPNotify works with several MDM's and allows you to "tail" the log files of these MDM's to report back what's installing. I had to modify the code in xcode to add the -airwatch switch. Below
are the instructions on how this was accomplished:

In the TrackProgress.swift file in the Xcode project, add this to the `enum OtherLogs` section:

Near line 17: `**static let airwatch = "/Library/Application Support/AirWatch/Data/Munki/Managed Installs/Logs/ManagedSoftwareUpdate.log"`

Near line 47: `**case "-airwatch" :
                additionalPath = OtherLogs.airwatch`

Now, look for `OtherLogs.munki` and add a section for `OtherLogs.airwatch`
I copied the MUNKI settings:
```swift
case OtherLogs.airwatch :
    if (line.contains("Installing") || line.contains("Downloading") || line.contains("Install of")) && !line.contains(" at ") && !line.contains(" from ") {
        do {
            let installerRegEx = try NSRegularExpression(pattern: "^.{0,27}")
            let status = installerRegEx.stringByReplacingMatches(in: line,
            options: NSRegularExpression.MatchingOptions.anchored,
            range: NSMakeRange(0, line.count),
            withTemplate: "").trimmingCharacters(in: .whitespacesAndNewlines)
            statusText = status
        } catch {
            NSLog("Couldn't parse ManagedSoftwareUpdate.log")
        }
    }
```


# The PLIST file used to launch DEPNotify
This file should be packaged with the application and placed in the LaunchDaemons folder

# The depinstall script
Customizable and easily deployed via a distribution package

# The postinstall script
What launches the PLIST that launches the script

## Watch the video for further explanation https://drive.google.com/file/d/1NQJsglx5Mm4qKh04rpUkT9ymXAfQD8un/view 
