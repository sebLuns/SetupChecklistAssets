# HelperScripts

This folder contains shell scripts that can be deployed alongside JSC to provide extra functionality. All are meant to be run in user-context, though may require additional resources or PPPC configurations for JSC to properly run.

Deployment can usually be handled by placing these scripts in their own package and deployed to a location that is configured to have the proper read and execute permissions to run. If using an onboarding program such as [Jamf Setup Manager](https://github.com/jamf/Setup-Manager), this can be done by simply compiling all these scripts into a package and adding to the same policy that JSC is deployed with. 

As an example of deployment, my scripts are packaged via [Jamf Composer](https://www.jamf.com/products/jamf-composer/) and install at `/Library/Application Support/SetupChecklistHelpers/`. All files are deployed with `rwx` permissions set to `755`.

Scripts can be invoked in the background like any other shell command by appending it with the `&` operator. As these are invoked by a configuration profile, all uses of `&` be replaced with the proper escape character sequence, `&amp;`.

Specific information on each script and their requirements can be found below.

## clearNotifications.sh

This script uses AppleScript to clear all notifications that may distract a user during enrollment. Supports clearing both individual and grouped notifications.

Because this script relies on UI manipulation, **JSC must be configured using the profile found in HelperConfigProfiles to use this script.**

## dockCleanup.sh

This script provides a simple method for changing the user's dock to provide a cleaner interface. While JSC did deploy with a `Dock` step, this script can be invoked in the background as part of a `prepareScript` or `activateScript` so that the user does not have the option to skip it. Once complete, it creates an empty file at `~/Library/Logs/dockCleanup` so that it isn't run more than once.

This script requires [Kyle Crawford's dockutil](https://github.com/kcrawford/dockutil/releases/tag/3.1.3) to be installed in order to run. A copy of the pkg can be added into the same policy that JSC is deployed with so it can be installed concurrently.

## JSCeudo_PSSO.sh

A heavily stripped down and modified version of [Kevin M. White's PSEUDO](https://github.com/Macjutsu/pseudo/releases/tag/v1.0.0-beta5) script for enforcing Platform SSO enrollment.

This script is not meant to replace the original. It is strictly meant to be run directly post-enrollment, and emphasizes simplicity and speed for a smoother onboarding. As environment variance is vastly reduced compared to an existing fleet where certain configurations may not have properly deployed, it assumes that all major components (PSSO Extension and Configuration Profile) are properly set up and installed on the target device. Additionally, as JSC provides a consistent interface for providing information to the user, extra dialogs via SwiftDialog and related code for fetching it has been stripped.

On launch, `JSCeudo_PSSO.sh` will auto-enable Passkey autofill for Company Portal if `SecureEnclave` auth is configured. It will then open up the PSSO registration dialog, and force the user to focus on it until registration has been completed. Additional checks are also in place to keep JSC open and on the PSSO Script Step, as well as keeping the Jamf Conditional Access in view should it pop up during registration. Once all PSSO related tasks are complete, the script will exit, and the user will be free to continue setting up their device.

Because this script relies on UI manipulation, **JSC must be configured using the profile found in HelperConfigProfiles to use this script.**