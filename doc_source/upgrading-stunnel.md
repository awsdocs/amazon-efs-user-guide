# Upgrading Stunnel<a name="upgrading-stunnel"></a>

Using encryption of data in transit with the Amazon EFS mount helper requires OpenSSL version 1\.0\.2 or newer, and a version of stunnel that supports both OCSP and certificate hostname checking\. The Amazon EFS mount helper uses the stunnel program for its TLS functionality\. Note that some versions of Linux don't include a version of stunnel that supports these TLS features by default\. When using one of those Linux distributions, mounting an Amazon EFS file system using TLS fails\.

After installing the Amazon EFS mount helper, you can upgrade your system's version of stunnel with the following instructions\.

**To upgrade stunnel on Amazon Linux, Amazon Linux 2, and other supported Linux distributions**

1.  In a web browser, go to the stunnel downloads page [https://stunnel\.org/downloads\.html](https://www.stunnel.org/downloads.html)\. 

1.  Locate the latest stunnel version that is available in tar\.gz format\. Note the name of the file as you will need it in the following steps\. 

1. Open a terminal on your Linux client, and run the following commands in the order presented\.

1. 

   ```
   sudo yum install -y gcc openssl-devel tcp_wrappers-devel
   ```

1. Replace *latest\-stunnel\-version* with the name of the file you noted previously in Step 2\.

   ```
   sudo curl -o latest-stunnel-version.tar.gz https://www.stunnel.org/downloads/latest-stunnel-version.tar.gz
   ```
**Warning**
Verify the signature on this file before proceeding by using the gpg key with fingerprint `AC91 5EA3 0645 D9D3 D4DA  E4FE B104 8932 DD3A AAA3`. For an example of how to do this, see [this link](https://docs.aws.amazon.com/systems-manager/latest/userguide/verify-agent-signature.html).

1. 

   ```
   sudo tar xvfz latest-stunnel-version.tar.gz
   ```

1. 

   ```
   cd latest-stunnel-version/
   ```

1. 

   ```
   sudo ./configure
   ```

1. 

   ```
   sudo make
   ```

1. The current amazon\-efs\-utils package is installed in `bin/stunnel`\. So that the new version can be installed, remove that directory with the following command\.

   ```
   sudo rm /bin/stunnel
   ```

1. 

   ```
   sudo make install
   ```

1. 
**Note**  
The default CentOS shell is csh, which has different syntax than the bash shell\. The following code first invokes bash, then runs\.

   ```
   bash
   ```

   ```
   if [[ -f /bin/stunnel ]]; then
   sudo mv /bin/stunnel /root
   fi
   ```

1. 

   ```
   $ sudo ln -s /usr/local/bin/stunnel /bin/stunnel
   ```

**To upgrade stunnel on macOS Big Sur**
+ Open a terminal on your EC2 Mac instance, and run the following command to upgrade to the latest version of stunnel\.

  ```
  brew upgrade stunnel
  ```

After you've installed a version of stunnel with the required features, you can mount your file system using TLS with the Amazon EFS recommended settings\.

## Disabling Certificate Hostname Checking<a name="disable-cert-hn-checking"></a>

If you are unable to install the required dependencies, you can optionally disable certificate hostname checking inside the Amazon EFS mount helper configuration\. We do not recommend that you disable this feature in production environments\. To disable certificate host name checking, do the following:

1. Using your text editor of choice, open the `/etc/amazon/efs/efs-utils.conf` file\.

1. Set the `stunnel_check_cert_hostname` value to false\.

1. Save the changes to the file and close it\.

For more information on using encryption of data in transit, see [Mounting EFS file systems](mounting-fs.md)\.

## Enabling Online Certificate Status Protocol<a name="tls-ocsp"></a>

 In order to maximize file system availability in the event that the CA is not reachable from your VPC, the Online Certificate Status Protocol \(OCSP\) is not enabled by default when you choose to encrypt data in transit\. Amazon EFS uses an [Amazon certificate authority](https://www.amazontrust.com) \(CA\) to issue and sign its TLS certificates, and the CA instructs the client to use OCSP to check for revoked certificates\. The OCSP endpoint must be accessible over the Internet from your Virtual Private Cloud in order to check a certificate's status\. Within the service, EFS continuously monitors certificate status, and issues new certificates to replace any revoked certificates it detects\. 

 In order to provide the strongest security possible, you can enable OCSP so that your Linux clients can check for revoked certificates\. OCSP protects against malicious use of revoked certificates, which is unlikely to occur within your VPC\. In the event that an EFS TLS certificate is revoked, Amazon will publish a security bulletin and release a new version of EFS mount helper that rejects the revoked certificate\. 

**To enable OCSP on your Linux client for all future TLS connections to EFS**

1. Open a terminal on your Linux client\.

1.  Using your text editor of choice, open the `/etc/amazon/efs/efs-utils.conf` file\. 

1.  Set the `stunnel_check_cert_validity` value to true\. 

1.  Save the changes to the file and close it\. 

**To enable OCSP as part of the `mount` command**
+  Use the following mount command to enable OCSP when mounting the file system\. 

  ```
         $ sudo mount -t efs -o tls,ocsp fs-12345678:/ /mnt/efs
  ```
