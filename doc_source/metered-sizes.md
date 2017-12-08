# Metering – How Amazon EFS Reports File System and Object Sizes<a name="metered-sizes"></a>

This section explains how Amazon EFS reports file system sizes and sizes of objects within a file system\.

## Metering Amazon EFS File System Objects<a name="metered-sizes-fs-objects"></a>

Customer\-visible objects in an Amazon EFS system can be regular files, directories, symbolic links, and special files \(FIFOs and sockets\)\. Each of these objects is metered for 2 KiB \(kibibytes\) of metadata \(for its inode\) and one or more increments of 4 KiB of data\. The following list explains the metered data size for different types of file system objects\.

+ **Regular files** – The metered data size of a regular file is the logical size of the file rounded to the next 4 KiB increment, except that it may be less for sparse files\.

  A sparse file is a file to which data is not written to all positions of the file before its logical size is reached\. For a sparse file, if the actual storage used is less than the logical size rounded to the next 4 KiB increment, Amazon EFS reports actual storage used as the metered data size\.

+ **Directories** – The metered data size of a directory is the actual storage used for the directory entries and the data structure that holds them, rounded to the next 4 KiB increment \(it does not include the actual storage used by the file data\)\.

+ **Symbolic links and special files** – The metered data size for these objects is always 4 KiB\.

When Amazon EFS reports the space used for an object, through the NFSv4\.1 `space_used` attribute, it includes the object's current metered data size, but not its metadata size\. There are two utilities available for measuring the disk usage of a file, the `du` and `stat` utilities\. Here's an example of how to use the `du` utility, on an empty file, with the `-k` option to return the output in kilobytes:

```
$ du -k file
4      file
```

Here's an example of how to use the `stat` utility on an empty file to return the file's disk usage:

```
$ /usr/bin/stat --format="%b*%B" file | bc
4096
```

To measure the size of a directory, use the `stat` utility, find the `Blocks` value, and then multiply that value by the block size\. Here's an example of how to use the `stat` utility on an empty directory:

```
$ /usr/bin/stat --format="%b*%B" . | bc 
4096
```

## Metering an Amazon EFS File System<a name="metered-sizes-fs"></a>

The metered size of an entire Amazon EFS file system is the sum of the sizes \(including metadata\) of all of its current objects\. The size of each object is calculated from a representative sampling that represents the size of the object during the metered hour, for example the hour from 8:00 am to 9:00 am\.

For example, an empty file contributes 6 KiB \(2 KiB metadata \+ 4 KiB data\) to the metered size of its file system\. Upon creation, a file system has a single empty root directory and therefore has a metered size of 6 KiB\.

The metered sizes of a particular file system define the usage for which the owner account is billed for that file system for that hour\.

**Note**  
The computed metered size does not represent a consistent snapshot of the file system at any particular time during that hour\. Rather, it represents the sizes of the objects that existed in the file system at varying times within each hour or possibly the hour before it, which are summed to determine the file system's metered size for the hour\. The metered size of a file systems is thus eventually consistent with the metered sizes of the objects stored when there are no writes to the file system\.

This metered size for an Amazon EFS file system can be seen in the following ways:

+ **DescribeFileSystems API** – Used in SDKs, HTTP, and the AWS CLI\.

+ **File Systems table** – For each file system listed in the AWS Management Console\.

+ **DF command** – In Linux, the `df` command can be run at the terminal prompt of an EC2 instance\.

**Note**  
The metered size is also used to determine your I/O throughput baseline and burst rates\. For more information, see [Throughput Scaling in Amazon EFS](performance.md#bursting)\. 