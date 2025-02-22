# AWS Systems Manager Distributor<a name="distributor"></a>

Distributor, a capability of AWS Systems Manager, helps you package and publish software to AWS Systems Manager managed nodes\. You can package and publish your own software or use Distributor to find and publish AWS\-provided agent software packages, such as **AmazonCloudWatchAgent**, or third\-party packages such as **Trend Micro\. **Publishing a package advertises specific versions of the package's document to managed nodes that you identify using node IDs, AWS account IDs, tags, or an AWS Region\. To get started with Distributor, open the [Systems Manager console](https://console.aws.amazon.com/systems-manager/distributor)\. In the navigation pane, choose **Distributor**\.

After you create a package in Distributor, you can install the package in one of the following ways:
+ One time by using [AWS Systems Manager Run Command](execute-remote-commands.md)
+ On a schedule by using [AWS Systems Manager State Manager](systems-manager-state.md)

**Important**  
Distributor doesn't guarantee that the third\-party packages you install are free of any potential malware\. We encourage that you conduct your own additional due diligence to ensure compliance with your internal security controls\. Security is a shared responsibility between AWS and you\. This is described as the shared responsibility model\. To learn more, see the [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/)\.

## How can Distributor benefit my organization?<a name="distributor-benefits"></a>

Distributor offers these benefits:
+  **One package, many platforms** 

  When you create a package in Distributor, the system creates an AWS Systems Manager document \(SSM document\)\. You can attach \.zip files to this document\. When you run Distributor, the system processes the instructions in the SSM document and installs the software package in the \.zip file on the specified targets\. Distributor supports multiple operating systems, including Windows, Ubuntu Server, Debian Server, and Red Hat Enterprise Linux\. For more information about supported platforms, see [Supported package platforms and architectures](#what-is-a-package-platforms)\.
+  **Control package access across groups of managed instances** 

  You can use Run Command or State Manager to control which of your managed nodes get a package and which version of that package\. Run Command and State Manager are capabilities of AWS Systems Manager\. Managed nodes can be grouped by instance or device IDs, AWS account numbers, tags, or AWS Regions\. You can use State Manager associations to deliver different versions of a package to different groups of instances\.
+  **Many AWS agent packages included and ready to use** 

  Distributor includes many AWS agent packages that are ready for you to deploy to managed nodes\. Look for packages in the Distributor `Packages` list page that are published by `Amazon`\. Examples include `AmazonCloudWatchAgent` and `AWSPVDriver`\.
+  **Automate deployment ** 

  To keep your environment current, use State Manager to schedule packages for automatic deployment on target managed nodes when those machines are first launched\.

## Who should use Distributor?<a name="distributor-who"></a>
+ Any AWS customer who wants to create new or deploy existing software packages, including AWS published packages, to multiple Systems Manager managed nodes at one time\.
+ Software developers who create software packages\.
+ Administrators who are responsible for keeping Systems Manager managed nodes current with the most up\-to\-date software packages\.

## What are the features of Distributor?<a name="distributor-features"></a>
+  **Deployment of packages to both Windows and Linux instances** 

  With Distributor, you can deploy software packages to Amazon Elastic Compute Cloud \(Amazon EC2\) instances and AWS IoT Greengrass core devices for Linux and Windows Server\. For a list of supported instance operating system types, see [Supported package platforms and architectures](#what-is-a-package-platforms)\.
**Note**  
Distributor isn't supported on the macOS operating system\.
+  **Deploy packages one time, or on an automated schedule** 

  You can choose to deploy packages one time, on a regular schedule, or whenever the default package version is changed to a different version\. 
+  **Completely reinstall packages, or perform in\-place updates** 

  To install a new package version, you can completely uninstall the current version and install a new one in its place, or only update the current version with new and updated components, according to an *update script* that you provide\. Your package application is unavailable during a reinstallation, but can remain available during an in\-place update\. In\-place updates are especially useful for security monitoring applications or other scenarios where you need to avoid application downtime\.
+  **Console, CLI, PowerShell, and SDK access to Distributor capabilities** 

  You can work with Distributor by using the Systems Manager console, AWS Command Line Interface \(AWS CLI\), AWS Tools for PowerShell, or the AWS SDK of your choice\.
+  **IAM access control** 

  By using AWS Identity and Access Management \(IAM\) policies, you can control which members of your organization can create, update, deploy, or delete packages or package versions\. For example, you might want to give an administrator permissions to deploy packages, but not to change packages or create new package versions\.
+  **Logging and auditing capability support** 

  You can audit and log Distributor user actions in your AWS account through integration with other AWS services\. For more information, see [Auditing and logging Distributor activity](distributor-logging-auditing.md)\.

## What is a package?<a name="what-is-a-package"></a>

A *package* is a collection of installable software or assets that includes the following\.
+ A \.zip file of software per target operating system platform\. Each \.zip file must include the following\.
  + An install and an uninstall script\. Windows Server\-based managed nodes require PowerShell scripts \(scripts named `install.ps1` and `uninstall.ps1`\)\. Linux\-based managed nodes require shell scripts \(scripts named `install.sh` and `uninstall.sh`\)\. AWS Systems Manager SSM Agent reads and carries out the instructions in the install and uninstall scripts\.
  + An executable file\. SSM Agent must find this executable to install the package on target managed nodes\.
+ A JSON\-formatted manifest file that describes the package contents\. The manifest isn't included in the \.zip file, but it's stored in the same Amazon Simple Storage Service \(Amazon S3\) bucket as the \.zip files that form the package\. The manifest identifies the package version and maps the \.zip files in the package to target managed node attributes, such as operating system version or architecture\. For information about how to create the manifest, see [Step 2: Create the JSON package manifest](distributor-working-with-packages-create.md#packages-manifest)\.

When you choose **Simple** package creation in the Distributor console, Distributor generates the installation and uninstallation scripts, file hashes, and the JSON package manifest for you, based on the software executable file name and target platforms and architectures\.

### Supported package platforms and architectures<a name="what-is-a-package-platforms"></a>

You can use Distributor to publish packages to the following Systems Manager managed node platforms\. A version value must match the exact release version of the operating system Amazon Machine Image \(AMI\) that you're targeting\. For more information about determining this version, see step 4 of [Step 2: Create the JSON package manifest](distributor-working-with-packages-create.md#packages-manifest)\.

**Note**  
Systems Manager doesn't support all of the following operating systems for AWS IoT Greengrass core devices\. For more information, see [Setting up AWS IoT Greengrass core devices](https://docs.aws.amazon.com/greengrass/v2/developerguide/setting-up.html) in the *AWS IoT Greengrass Version 2 Developer Guide*\.


| Platform | Code value in manifest file | Architecture | 
| --- | --- | --- | 
|  Windows Server  |   `windows`   |  `x86_64` or `386`  | 
|  Debian Server  |   `debian`   |  `x86_64` or `386`  | 
|  Ubuntu Server  |   `ubuntu`   |  `x86_64` or `386` `arm64` \(Ubuntu Server 16 and later, A1 instance types\)  | 
|  Red Hat Enterprise Linux \(RHEL\)  |   `redhat`   |  `x86_64` or `386` `arm64` \(RHEL 7\.6 and later, A1 instance types\)  | 
|  CentOS  |   `centos`   |  `x86_64` or `386`  | 
|  Amazon Linux and Amazon Linux 2  |   `amazon`   |  `x86_64` or `386` `arm64` \(Amazon Linux 2, A1 instance types\)  | 
|  SUSE Linux Enterprise Server \(SLES\)  |   `suse`   |  `x86_64` or `386`  | 
|  openSUSE  |   `opensuse`   |  `x86_64` or `386`  | 
|  openSUSE Leap  |   `opensuseleap`   |  `x86_64` or `386`  | 
|  Oracle Linux  |   `oracle`   |  `x86_64`  | 

**Topics**
+ [How can Distributor benefit my organization?](#distributor-benefits)
+ [Who should use Distributor?](#distributor-who)
+ [What are the features of Distributor?](#distributor-features)
+ [What is a package?](#what-is-a-package)
+ [Setting up Distributor](distributor-getting-started.md)
+ [Working with Distributor](distributor-working-with.md)
+ [Auditing and logging Distributor activity](distributor-logging-auditing.md)
+ [Troubleshooting AWS Systems ManagerDistributor](distributor-troubleshooting.md)