# Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Windows Instances in a VPC<a name="enhanced-networking-ena"></a>

To prepare for enhanced networking with the ENA network adapter, set up your instance as follows:

+ Launch the instance in a VPC\. \(You can't enable enhanced networking if the instance is in EC2\-Classic\.\)

+ Install and configure the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](http://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\. 

+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `enaSupport` attribute, may render incompatible instances or operating systems unreachable; if you have a recent backup, your data will still be retained if this happens\.


+ [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)
+ [Enabling Enhanced Networking with ENA on Windows](#enable-enhanced-networking-ena-WIN)
+ [Amazon ENA Driver Versions](#ena-adapter-driver-versions)

## Testing Whether Enhanced Networking with ENA Is Enabled<a name="test-enhanced-networking-ena"></a>

To test whether enhanced networking with ENA is already enabled, verify that the driver is installed on your instance and that the `enaSupport` attribute is set\. 

**Instance Attribute \(enaSupport\)**

To check whether an instance has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.

+ [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

  ```
  aws ec2 describe-instances --instance-ids instance_id --query "Reservations[].Instances[].EnaSupport"
  ```

+ [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
  ```

**Image Attribute \(enaSupport\)**  
To check whether an AMI has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.

+ [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)

  ```
  aws ec2 describe-images --image-id ami_id --query "Images[].EnaSupport"
  ```

+ [Get\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Image -ImageId ami_id).EnaSupport
  ```

## Enabling Enhanced Networking with ENA on Windows<a name="enable-enhanced-networking-ena-WIN"></a>

If you launched your instance and it does not have enhanced networking enabled already, you must download and install the required network adapter driver on your instance, and then set the `enaSupport` instance attribute to activate enhanced networking\. You can only enable this attribute on supported instance types and only if the ENA driver is installed\. For more information, see [Enhanced Networking Types](enhanced-networking.md#supported_instances)\.

**To enable enhanced networking with ENA**

1. Connect to your instance and log in as the local administrator\.

1. \[Windows Server 2016\] Run the following EC2Launch PowerShell script to configure the instance after the driver is installed\.

   ```
   PS C:\> C:\ProgramData\Amazon\EC2-Windows\Launch\Scripts\InitializeInstance.ps1 -Schedule
   ```

1. From the instance, install the driver as follows:

   1. Download the Amazon ENA adapter driver for your operating system:

      + [Windows Server 2008 R2](https://s3.amazonaws.com/ec2-windows-drivers/ENA.zip)

      + [Windows Server 2012 and later](https://s3.amazonaws.com/ec2-windows-drivers/ENA_1_0_8.zip)

   1. Extract the zip archive\.

   1. Change to the folder that corresponds to your OS version\. \(For Windows Server 2016, use the `2012R2` folder\.\) This folder contains the following driver files: `ena.cat`, `ena.inf`, and `ena.sys`\.

   1. Install the driver using the following command:

      ```
      pnputil -i -a ena.inf
      ```

      The following is example output if the installation is successful:

      ```
      Microsoft PnP Utility
                                      
      Processing inf :            ena.inf 
      Succesfully installed the driver on a device on the system.
      Driver package added successfully.
      Published name :            oem9.inf
                                  
      Total attempted:                 1                            
      Number successfully imported:    1
      ```

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. Enable ENA support on your instance as follows:

   1. From your local computer, check the EC2 instance ENA support attribute on your instance by running one of the following commands\. If the attribute is not enabled, the output will be "\[\]" or blank\. `EnaSupport` is set to `false` by default\.

      + [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

        ```
        aws ec2 describe-instances --instance-ids instance_id --query "Reservations[].Instances[].EnaSupport"
        ```

      + [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

        ```
        (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
        ```

   1. To enable ENA support, run one of the following commands:

      + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

        ```
        aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
        ```

      + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

        ```
        Edit-EC2InstanceAttribute -InstanceId instance_id -EnaSupport $true
        ```

      If you encounter problems when you restart the instance, you can also disable ENA support using one of the following commands:

      + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

        ```
        aws ec2 modify-instance-attribute --instance-id instance_id --no-ena-support
        ```

      + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

        ```
        Edit-EC2InstanceAttribute -InstanceId instance_id -EnaSupport $false
        ```

   1. Verify that the attribute has been set to `true` using describe\-instances or Get\-EC2Instance as shown previously\. You should now see the following output:

      ```
      [
          true
      ]
      ```

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance using the AWS OpsWorks console so that the instance state remains in sync\.

1. On the instance, validate that the ENA driver is installed and enabled as follows:

   1. Right\-click the network icon and choose **Open Network and Sharing Center**\.

   1. Choose the Ethernet adapter \(for example, **Ethernet 2**\)\.

   1. Choose **Details**\. For **Network Connection Details**, check that **Description** is **Amazon Elastic Network Adapter**\.

1. \(Optional\) Create an AMI from the instance\. The AMI inherits the `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with ENA enabled by default\. For more information, see [Creating a Custom Windows AMI](Creating_EBSbacked_WinAMI.md)\.

## Amazon ENA Driver Versions<a name="ena-adapter-driver-versions"></a>

Windows AMIs include the Amazon ENA driver to enable enhanced networking\. The following table summarizes the changes for each release\.


| Driver version | Details | 
| --- | --- | 
|  1\.0\.9\.0  |  Includes some reliability fixes\. Applies only to Windows Server 2008 R2\. Not recommended for other versions of Windows Server\.  | 
|  1\.0\.8\.0  |  The initial release\. Included in AMIs for Windows Server 2008 R2, Windows Server 2012 RTM, Windows Server 2012 R2, and Windows Server 2016\.  | 