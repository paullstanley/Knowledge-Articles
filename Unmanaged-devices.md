# Internal Knowledge Article: Understanding Device Unmanagement in Jamf Pro

## Overview

This document provides comprehensive instructions on unmanaging devices in Jamf Pro while retaining their inventory records. It clarifies common misconceptions and details alternative methods to achieve this, ensuring accurate representation of device management status in Jamf Pro's inventory.

## Methods to Unmanage Devices

### Method 1: Removing MDM Profile via Management Tab

This method involves removing the MDM profile to stop management tasks without deleting the computer's inventory record.

#### Steps:
1. In Jamf Pro, navigate to **Computers**.
2. Search for the target computer.
3. Click on the **Management** tab.
4. Select **Remove MDM Profile**.

**Note**: The device will no longer be managed by Jamf Pro; however, the General section in the inventory may still show the device as "Managed". This is because the device cannot report its management status back to Jamf Pro after the management command to remove the MDM Profile completes.

### Method 2: Editing Management Settings in General Section

This alternative method ensures that the device's management status is accurately reflected in the General section of its inventory record.

#### Steps:
1. In Jamf Pro, navigate to **Computers**.
2. Search for the target computer.
3. In the **General** section of the device’s Inventory Page, click **Edit**.
4. Uncheck the radio button labeled **Allow Jamf Pro to perform management tasks**.

**Outcome**: Selecting this option will unmanage the device and the inventory record will accurately reflect the device's unmanaged status.

## Unmanaging via Command Line

If remote or physical access to the device is available, you can also unmanage a computer using command-line operations:

### Steps:
1. Open Terminal on the target computer, or remotely access it via SSH.
2. Execute the following command to remove all Jamf management framework components installed by Jamf Pro:
sudo /usr/local/bin/jamf removeFramework

**Outcome**: This will remove the Jamf management framework, ensuring that Jamf Pro can no longer perform management tasks or communicate with the computer.

## Wiping and Unmanaging Computers

Wiping a computer is more comprehensive and removes the MDM profile, Jamf management framework, all settings, and applications installed by Jamf Pro.

### Methods:
- **Manual Erasure**: Navigate to System Settings > General > Transfer or Reset > Erase All Content and Settings.
- **Remote Command**: Send the **Wipe Computer** remote command from Jamf Pro.

For further details, refer to the [Erase Apple devices in Apple Platform Deployment](https://learn.jamf.com/en-US/bundle/jamf-pro-documentation-11.7.0/page/Unmanaging_Computers.html).

## Conclusion

Understanding these methods and their outcomes is essential for effectively managing devices within Jamf Pro. The described methods address specific needs for unmanaging devices while maintaining accurate inventory records. For additional support, refer to Jamf Pro's official documentation or contact your support line.
