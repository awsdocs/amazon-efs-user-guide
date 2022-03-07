# Installing `botocore`<a name="install-botocore"></a>

The Amazon EFS client uses `botocore` to send a file system's mount status to Amazon CloudWatch Logs\. For more information, see [Monitoring EFS file system mount status](how-to-monitor-mount-status.md)\. This section describes how install and upgrade `botocore` on an Amazon EC2 instance\.

## To install botocore as an RPM package<a name="install-botocore-rpm"></a>

1. Run the following command to install `wget`\.

   ```
   sudo yum -y install wget
   ```

1. Use the following script to install the appropriate version of the `pip` package manager\.

   ```
   if [[ "$(python3 -V 2>&1)" =~ ^(Python 3.5.*) ]]; then
       sudo wget https://bootstrap.pypa.io/3.5/get-pip.py -O /tmp/get-pip.py
   elif [[ "$(python3 -V 2>&1)" =~ ^(Python 3.4.*) ]]; then
       sudo wget https://bootstrap.pypa.io/3.4/get-pip.py -O /tmp/get-pip.py
   else
       sudo wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py
   fi
   ```

1. Run the following commands to install `botocore`\.

   ```
   sudo python3 /tmp/get-pip.py
   sudo pip3 install botocore
   ```

   Or

   ```
   sudo /usr/local/bin/pip3 install botocore
   ```

## To install botocore as an DEB package<a name="install-botocore-rpm"></a>

1. Run the following commands to install `wget`\.

   ```
   sudo apt-get update
   sudo apt-get -y install wget
   ```

1. Use the following script to install the appropriate version of the `pip` package manager\.

   ```
   if echo $(python3 -V 2>&1) | grep -e "Python 3.5"; then
       sudo wget https://bootstrap.pypa.io/3.5/get-pip.py -O /tmp/get-pip.py
   elif echo $(python3 -V 2>&1) | grep -e "Python 3.4"; then
       sudo wget https://bootstrap.pypa.io/3.4/get-pip.py -O /tmp/get-pip.py
   else
       sudo apt-get -y install python3-distutils
       sudo wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py
   fi
   ```

1. Run the following commands to install `botocore`\.

   ```
   sudo python3 /tmp/get-pip.py
   sudo pip3 install botocore
   ```

   Or

   ```
   sudo /usr/local/bin/pip3 install botocore
   ```

   If you are installing on Debian10 or Ubuntu20, use the following commands to install `botocore` in the specified target folder\. For Debian10:

   ```
   sudo python3 /tmp/get-pip.py
   sudo pip3 install --target /usr/lib/python3/dist-packages botocore
   ```

   For Ubuntu20:

   ```
   sudo /usr/local/bin/pip3 install --target /usr/lib/python3/dist-packages botocore
   ```

## To install `botocore` on a Mac instance<a name="install-botocore-mac"></a>
+ Run the following command to install botocore on your Mac instance\.

  ```
  sudo pip3 install botocore
  ```

## Upgrading `botocore`<a name="upgrade-botocore"></a>

To upgrade to the latest compatible version of botocore, use the `--upgrade` option\. For example:

```
sudo pip3 install botocore --upgrade
```