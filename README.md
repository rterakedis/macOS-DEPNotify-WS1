# macOS-DEPNotify-WS1

DEPNotify is a great splash screen program to notify macOS users during the build process about whats happening on their system. I've decided to use this time to
give a little history lesson about our company and while installing applications in the background. All credit for this program goes to Joel Rennich and the folks
that help support it on the macadmins slack channel. Included in the project are four files:

1. The main application DEPNotify

DEPNotify works with several MDM's and allows you to "tail" the log files of these MDM's to report back what's installing. I had to modify the code in xcode to add the -airwatch switch. Below
are the instructions on how this was accomplished:

In the TrackProgress.swift file in the Xcode project, add this to the `enum OtherLogs` section:

Near line 17: **static let airwatch = "/Library/Application Support/AirWatch/Data/Munki/Managed Installs/Logs/ManagedSoftwareUpdate.log"

Near line 47: **case "-airwatch" :
                additionalPath = OtherLogs.airwatch

Now, look for OtherLogs.munki and add a section for OtherLogs.airwatch. I copied the MUNKI settings:

case OtherLogs.airwatch :
                    if (line.contains("Installing") || line.contains("Downloading") || line.contains("Install of"))
                        && !line.contains(" at ") && !line.contains(" from ") {

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


2. The PLIST file used to launch DepNotify
3. The depinstall script
4. The postinstall script
