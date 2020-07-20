# Metering: How Amazon EFS reports file system and object sizes<a name="metered-sizes"></a>

In the following section, find how Amazon EFS reports file system sizes and sizes of objects within a file system\.

## Metering Amazon EFS file system objects<a name="metered-sizes-fs-objects"></a>

Objects that you can view in an Amazon EFS system include regular files, directories, symbolic links, and special files \(FIFOs and sockets\)\. Each of these objects is metered for 2 kibibytes \(KiB\) of metadata \(for its inode\) and one or more increments of 4 KiB of data\. The following list explains the metered data size for different types of file system objects:
+ **Regular files** – The metered data size of a regular file is the logical size of the file rounded to the next 4\-KiB increment, except that it might be less for sparse files\.

  A *sparse file* is a file to which data is not written to all positions of the file before its logical size is reached\. For a sparse file, in some cases the actual storage used is less than the logical size rounded to the next 4\-KiB increment\. In these cases, Amazon EFS reports actual storage used as the metered data size\.
+ **Directories** – The metered data size of a directory is the actual storage used for the directory entries and the data structure that holds them, rounded to the next 4\-KiB increment\. The metered data size doesn't include the actual storage used by the file data\.
+ **Symbolic links and special files** – The metered data size for these objects is always 4 KiB\.

When Amazon EFS reports the space used for an object, through the NFSv4\.1 `space_used` attribute, it includes the object's current metered data size but not its metadata size\. You can use two utilities for measuring the disk usage of a file, the `du` and `stat` utilities\. Following is an example of how to use the `du` utility on an empty file, with the `-k` option to return the output in kilobytes\.

```
$ du -k file
4      file
```

Following is an example of how to use the `stat` utility on an empty file to return the file's disk usage\.

```
$ /usr/bin/stat --format="%b*%B" file | bc
4096
```

To measure the size of a directory, use the `stat` utility\. Find the `Blocks` value, and then multiply that value by the block size\. Following is an example of how to use the `stat` utility on an empty directory:

```
$ /usr/bin/stat --format="%b*%B" . | bc 
4096
```

## Metering an Amazon EFS file system<a name="metered-sizes-fs"></a>

The metered size of an Amazon EFS file system includes the sum of the sizes of all current objects in the standard and IA storage classes\. The size of each object is calculated from a representative sampling that represents the size of the object during the metered hour\. An example is the hour from 8 AM to 9 AM\.

For example, an empty file contributes 6 KiB \(2 KiB metadata \+ 4 KiB data\) to the metered size of its file system\. Upon creation, a file system has a single empty root directory and therefore has a metered size of 6 KiB\.

The metered sizes of a particular file system define the usage for which the owner account is billed for that file system for that hour\.

**Note**  
The computed metered size doesn't represent a consistent snapshot of the file system at any particular time during that hour\. Instead, it represents the sizes of the objects that existed in the file system at varying times within each hour, or possibly the hour before it\. These sizes are summed to determine the file system's metered size for the hour\. The metered size of a file system is thus eventually consistent with the metered sizes of the objects stored when there are no writes to the file system\.

You can see this metered size for an Amazon EFS file system in the following ways:
+ Call the [DescribeFileSystems operation](API_DescribeFileSystems.md) using one the SDKs, HTTP, or the AWS CLI\.
+ View the **File Systems** table, for each file system listed in the AWS Management Console\.
+ Run the `df` command in Linux at the terminal prompt of an EC2 instance\. 

  Use the `df` command and not the `du` command\. Don't use the `du` command on the root of the file system for storage metering purposes\. The results don't provide full data\.

**Note**  
The metered size of the Standard storage class is also used to determine your I/O throughput baseline and burst rates\. For more information, see [Throughput Scaling with Bursting Mode](performance.md#bursting)\. 

### Metering for infrequent access<a name="metered-sizes-IA"></a>

Infrequent Access \(IA\) storage is metered in 4 KiB increments\. IA file metadata \(2 KiB per file\) is always stored and metered in the Standard storage class\. Data access for IA storage is metered in 1 MiB increments\.