# Recommended NFS Mount Options<a name="mounting-fs-nfs-mount-settings"></a>

We recommend the following default values for mount options on Linux: 
+ `rsize=1048576` – Sets the maximum number of bytes of data that the NFS client can receive for each network READ request\. This value applies when reading data from a file on an EFS file system\. We recommend that you use the largest size possible \(up to `1048576`\) to avoid diminished performance\.
+ `wsize=1048576` – Sets the maximum number of bytes of data that the NFS client can send for each network WRITE request\. This value applies when writing data to a file on an EFS file system\. We recommend that you use the largest size possible \(up to `1048576`\) to avoid diminished performance\.
+ `hard` – Sets the recovery behavior of the NFS client after an NFS request times out, so that NFS requests are retried indefinitely until the server replies\. We recommend that you use the hard mount option \(`hard`\) to ensure data integrity\. If you use a `soft` mount, set the `timeo` parameter to at least `150` deciseconds \(15 seconds\)\. Doing so helps minimize the risk of data corruption that is inherent with soft mounts\.
+ `timeo=600` – Sets the timeout value that the NFS client uses to wait for a response before it retries an NFS request to 600 deciseconds \(60 seconds\)\. If you must change the timeout parameter \(`timeo`\), we recommend that you use a value of at least `150`, which is equivalent to 15 seconds\. Doing so helps avoid diminished performance\.
+ `retrans=2` – Sets to 2 the number of times the NFS client retries a request before it attempts further recovery action\.
+ `noresvport` – Tells the NFS client to use a new Transmission Control Protocol \(TCP\) source port when a network connection is reestablished\. Doing this helps make sure that the EFS file system has uninterrupted availability after a network recovery event\.
+ `_netdev` – When present in `/etc/fstab`, prevents the client from attempting to mount the EFS file system until the network has been enabled\.

If you don't use the preceding defaults, be aware of the following:
+ In general, avoid setting any other mount options that are different from the defaults, which can cause reduced performance and other issues\. For example, changing read or write buffer sizes or disabling attribute caching can result in reduced performance\.
+ Amazon EFS ignores source ports\. If you change Amazon EFS source ports, it doesn't have any effect\.
+ Amazon EFS doesn't support any of the Kerberos security variants\. For example, the following mount command fails\.

  ```
   $ mount -t nfs4 -o krb5p <DNS_NAME>:/ /efs/ 
  ```
+ We recommend that you mount your file system using its DNS name\. This name resolves to the IP address of the Amazon EFS mount target in the same Availability Zone as your Amazon EC2 instance\. If you use a mount target in an Availability Zone different from that of your Amazon EC2 instance, you incur standard EC2 charges for data sent across Availability Zones\. You also might see increased latencies for file system operations\.
+ For more mount options, and detailed explanations of the defaults, see the [http://man7.org/linux/man-pages/man5/fstab.5.html](http://man7.org/linux/man-pages/man5/fstab.5.html) and [https://linux.die.net/man/5/nfs](https://linux.die.net/man/5/nfs) pages in the Linux documentation\.

**Note**  
If your EC2 instance needs to start regardless of the status of your mounted EFS file system, add the `nofail` option to your file system's entry in your `/etc/fstab` file\.