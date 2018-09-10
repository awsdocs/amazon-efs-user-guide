# Amazon EFS File Sync<a name="get-started-file-sync"></a>

Amazon EFS File Sync copies files from an existing on\-premises or cloud file system into an Amazon EFS file system\. EFS File Sync securely and efficiently copies files over the internet or an AWS Direct Connect connection\. It copies file data and file system metadata such as ownership, timestamps, and access permissions\. For information about how to use AWS Direct Connect, see [Walkthrough 5: Create and Mount a File System On\-Premises with AWS Direct Connect](efs-onpremises.md)\. 

**Note**  
You don’t need to set up AWS Direct Connect to use EFS File Sync\.

**Note**  
EFS File Sync currently doesn't support syncing from an Amazon EFS source to a NFS destination\.

**Topics**
+ [Requirements for EFS File Sync](#file-sync-requirements)
+ [EFS File Sync Architecture](#file-sync-architecture)
+ [How EFS File Sync Transfers Files](#transfering-files)

## Requirements for EFS File Sync<a name="file-sync-requirements"></a>

Unless otherwise noted, the following are required for creating Amazon EFS File Sync\. 

### Hardware Requirements<a name="file-sync-hardware"></a>

When deploying Amazon EFS File Sync on\-premises, you must make sure that the underlying hardware on which you are deploying the file sync VM can dedicate the following minimum resources:
+ Four virtual processors assigned to the VM\. 
+  32 GB of RAM assigned to the VM 
+  80 GB of disk space for installation of VM image and system data 

When deploying Amazon EFS File Sync on Amazon EC2, the instance size must be at least **xlarge** for your Amazon EFS File Sync to function\. We recommend using one of the **Memory optimized r4\.xlarge** instance types\. 

### Supported Hypervisors and Host Requirements<a name="file-sync-hosts-requirements"></a>

You can choose to run EFS File Sync either on\-premises as a virtual machine \(VM\), or in AWS as an Amazon Elastic Compute Cloud \(Amazon EC2\) instance\.

EFS File Sync supports the following hypervisor versions and hosts:
+ VMware ESXi Hypervisor \(version 4\.1, 5\.0, 5\.1, 5\.5, 6\.0 or 6\.5\) – A free version of VMware is available on the [VMware website](http://www.vmware.com/products/vsphere-hypervisor/overview.html)\. You also need a VMware vSphere client to connect to the host\. 
+ EC2 instance – EFS File Sync provides an Amazon Machine Image \(AMI\) that contains the EFS File Sync VM image\. We recommend using the **Memory optimized r4\.xlarge** instance types\.

### Supported NFS Protocols<a name="requirements-nfs-versions"></a>

EFS File Sync supports NFS v3\.x, NFS v4\.0 and NFS v4\.1\.

### Allowing EFS File Sync Access Through Firewalls and Routers<a name="file-sync-endpoints"></a>

EFS File Sync requires access to the following endpoints to communicate with AWS\. If you use a firewall or router to filter or limit network traffic, you must configure your firewall and router to allow these service endpoints for outbound communication to AWS\.

The following endpoints are required by EFS File Sync\.

```
cp-sync.$region.amazonaws.com       
activation-sync.$region.amazonaws.com
ec2-*.amazonaws.com
repo.$region.amazonaws.com
repo.default.amazonaws.com
packages.$region.amazonaws.com
0.amazon.pool.ntp.org
1.amazon.pool.ntp.org
2.amazon.pool.ntp.org
3.amazon.pool.ntp.org
54.201.223.107
169.254.169.123
```

For information about supported AWS Regions, see [Amazon Elastic File System](http://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region) in the AWS General Reference\.

The Amazon CloudFront endpoint is required before activation for the sync agent to get the list of available AWS Regions\.

```
https://d4kdq0yaxexbo.cloudfront.net/
```

### Network and Port Requirements<a name="sync-network"></a>

EFS File Sync requires the following ports for its operation\.


|  From  |  To  |  Protocol  |  Port  |  How Used  | 
| --- | --- | --- | --- | --- | 
|  EFS File Sync VM  |  AWS  |  TCP  |  443 \(HTTPS\)  |  For communication from EFS File Sync VM to the AWS service endpoint\. For information about service endpoints, see [ Allowing EFS File Sync Access Through Firewalls and Routers](#file-sync-endpoints)\.  | 
|  Your web browser  |  EFS File Sync VM  |  TCP  |  80 \(HTTP\)  |  By local systems to obtain the sync agent activation key\. Port 80 is used only during activation of the EFS File Sync agent\.  EFS File Sync VM doesn't require port 80 to be publicly accessible\. The required level of access to port 80 depends on your network configuration\. If you activate your sync agent from the Amazon EFS Management Console, the host from which you connect to the console must have access to port 80\.  | 
|  EFS File Sync VM  |  Domain Name Service \(DNS\) server  |  TCP/UDP  |  53 \(DNS\)  |  For communication between EFS File Sync VM and the DNS server\.  | 
|  EFS File Sync VM  |  AWS  |  TCP  |  22 \(Support channel\)  |  Allows AWS Support to access your EFS File Sync to help you with troubleshooting EFS File Sync issues\. You don't need this port open for normal operation, but it is required for troubleshooting\.  | 
|  EFS File Sync VM  |  NTP server  |  UDP  |  123 \(NTP\)  |  By local systems to synchronize VM time to the host time\.   | 
|  EFS File Sync VM  |  NFS Server  |  TCP/UDP  |  2049 \(NFS\)  |  By EFS File Sync VM to mount a source NFS file system\. Supports NFS v3\.x, NFS v4\.0 and NFS v4\.1\.  | 

Following is an illustration of the required ports and lists the ports required by EFS File Sync\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-port-requirements2.png)

## EFS File Sync Architecture<a name="file-sync-architecture"></a>

EFS File Sync provides the following benefits:
+ Efficient high\-performance parallel data transfer that tolerates unreliable and high\-latency networks\.
+ Encryption of data transferred from your IT environment to AWS\.
+ Data transfer rate up to five times faster than standard Linux copy tools\.
+ Full and incremental syncs for repetitive transfers\.

The following diagram shows a high\-level view of the EFS File Sync architecture\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/sync-file-sync-architecture.png)

**To sync files from an existing file system to Amazon EFS**

1. Open the Amazon EFS Management Console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Download and deploy a sync agent\. For on\-premises deployment, the sync agent is provided as a virtual machine \(VM\) image for VMware ESXi\. For an AWS Cloud deployment, you can create an Amazon EC2 instance from the community Amazon Machine Image \(AMI\)\.

1. Create a sync task and configure your source and destination file systems\.

1. Start your sync task to begin syncing files from the source file system to the Amazon EFS file system\.

1. Monitor your sync task on the Amazon EFS console or from Amazon CloudWatch\. For more information, see [Monitoring EFS File Sync with Amazon CloudWatch](monitoring-file-sync.md)\.

For more details on the EFS File Sync process, see the following:
+ For information about how to sync files from an on\-premises file system to Amazon EFS, see [Walkthrough 7: Sync Files from an On\-Premises File System to Amazon EFS by Using EFS File Sync](walkthrough-file-sync-onpremise.md)\.
+ For information about how to sync files from Amazon EC2 to Amazon EFS, see [Walkthrough 8: Sync a File System from Amazon EC2 to Amazon EFS Using EFS File Sync](walkthrough-file-sync-ec2.md)\.

## How EFS File Sync Transfers Files<a name="transfering-files"></a>

When a sync task starts, it goes through three different statuses: **Preparing**, **Syncing** and **Verifying**\. In the **Preparing** status, EFS File Sync examines the source and destination file systems to determine which files to sync\. It does so by recursively scanning the contents of the source and destination file systems for differences\. The files it examines include files that have been modified, deleted, added and files that have their metadata modified\.

After the scanning is done, and the differences are calculated, EFS File Sync transitions to the **Syncing** status\. At this point, EFS File Sync starts transferring files from the source file system to the destination Amazon EFS file system\. Only files that have been added, modified, or deleted are transferred\. This incremental transfer doesn't depend on the sync task you use but rather depends on the contents of your source and destination file system\. In the Configure sync settings dialog box, you can choose which metadata in source file system you want to preserve\. You can also configure your sync tasks settings to keep or delete files in the destination even if they aren't found in the source file system\.

After the syncing is done, EFS File Sync verifies consistency between the source and destination file systems\. This is the **Verifying** status\. By default, EFS File Sync uses a full consistency verification of your source and destination when transferring files\. During the **verifying** phase of a sync task, EFS File Sync rescans the content of the source and destination for any differences\. If no differences are found, the task succeeds\. Otherwise, the task is marked with a verification failure\. For information about EFS File Sync status, see [Understanding Sync Task Status](managing-file-sync.md#sync-task-status)\.

### Best Practices for Data Transfer from an On\-Premises Storage Array<a name="best-practices-storage-array"></a>

You might want to transfer data from an on\-premises enterprise storage array to Amazon EFS\. In this case, files in the source file system might be modified by another application while the files are being transferred from Network File System \(NFS\) to Amazon EFS\. 

To ensure that EFS File Sync successfully performs a transfer with full consistency verification, we recommend that the source location point to a read\-only snapshot\. This setup ensures that files at the source location can’t be modified while the files are being transferred, and makes sure that verification works\.

For information about how to take a snapshot in an enterprise storage array, see one of the following: 
+ EMC VNX: [How to create a VNX snapshot and attach it to a server](https://community.emc.com/docs/DOC-24251)
+ EMC VMAX: [EMC TimeFinder Product Description Guide](https://www.emc.com/collateral/software/timefinder.pdf)
+ NetApp: [Snapshot management](https://library.netapp.com/ecmdocs/ECMP1635994/html/GUID-DF14D62D-99D1-4B2B-8065-884C9E914259.html)
+ HDS: [Hitachi Copy\-on\-Write Snapshot User Guide](https://support.hds.com/download/epcra/rd701311.pdf)

#### Related Topics<a name="related"></a>

[Step 2: Create a Sync Task](walkthrough-file-sync-onpremise.md#create-sync-task)

[Understanding Sync Task Status](managing-file-sync.md#sync-task-status)