# Managing file system throughput<a name="managing-throughput"></a>

You can modify the file system throughput mode after you create it\. There are two throughput modes to choose from for your file system, *Bursting Throughput* and *Provisioned Throughput*\. With *Bursting Throughput* mode, throughput scales as the size of your file system in the standard storage class grows\. For more information about EFS storage classes, see [EFS storage classes](storage-classes.md)\. With *Provisioned Throughput *mode, you can instantly provision the throughput of your file system \(in MiB/s\) independent of the amount of data stored\. For more information about Amazon EFS throughput modes, see [Throughput modes](performance.md#throughput-modes)\.

**Note**  
You can decrease your file system throughput in *Provisioned Throughput* mode as long as it’s been more than 24 hours since the last decrease\. Additionally, you can change between *Provisioned Throughput* mode and the default *Bursting Throughput* mode as long as it’s been more than 24 hours since the last throughput mode change\.

You can manage the file system throughput mode using the Amazon EFS console, the CLI, and API\.

**To manage file system throughput \(console\)**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose **File systems** to display the list of EFS file systems in your account\.

1. Choose the file system on which you want to modify lifecycle policies\.

1. On the file system details page, in the **General** panel, choose **Edit**\. The **Edit >> General** settings page displays\.  
![\[Edit a file system's throughput on the Edit general settings page in the EFS console.\]](http://docs.aws.amazon.com/efs/latest/ug/images/edit-fs-general-throughput.png)

1. Modify the **Throughput mode** setting\. If you are changing to Provisioned throughput mode, or changing the amount of Provisioned throughput, enter the new value in MiBps\. You can see the estimated cost of the new throughput value, along with the read throughput for the setting\.

1. Choose **Save changes** to implement your changes\.

**To manage file system throughput \(CLI\)**
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/efs/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/efs/update-file-system.html) CLI command, or the [UpdateFileSystem](API_UpdateFileSystem.md) API action to make changes to a file system's throughput mode\.