# Extending a Windows File System after Resizing the Volume<a name="recognize-expanded-volume-windows"></a>

Use the Windows Disk Management utility to extend the disk size to the new size of the volume\. You can begin resizing the file system as soon as the volume enters the `Optimizing` state\.

**Important**  
Before extending a file system that contains valuable data, it is a best practice to create a snapshot of the volume that contains it in case you need to roll back your changes\. For information about EBS snapshots, see [Creating an Amazon EBS Snapshot](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html)\.

For information about extending a Linux file system, see [Extending a Linux File System after Resizing the Volume](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To extend a Windows file system**

1. Log in to your Windows instance using Remote Desktop\.

1. In the **Run** dialog, type **diskmgmt\.msc** and press Enter\. The Disk Management utility opens\.  
![\[Windows Server Disk Management Utility\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Expand-Volume-Win2008-before.png)

1. On the **Disk Management** menu, choose **Action**, **Rescan Disks**\.

1. Open the context \(right\-click\) menu for the expanded drive and choose **Extend Volume**\.  
![\[Windows Server Disk Management Utility\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Expand-Volume-Win2008-before-menu.png)

1. In the Extend Volume wizard, choose **Next**\. For **Select the amount of space in MB**, enter the number of megabytes by which to extend the volume\. Normally, you set this to the maximum available space\. The highlighted text under **Selected** is the amount of space that is added, not the final size the volume will have\. 

   Complete the wizard\.  
![\[Windows Server Extend Volume Wizard\]](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/images/Extend-Volume-Wizard-Win2008.png)
**Tip**  
If the increased available space on your volume remains invisible to the system, try re\-initializing the volume as described in [Initializing Amazon EBS Volumes](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-initialize.html)\.