# Mounting with an IP Address through AWS Console<a name="mounting-fs-mount-ip-addr"></a>

As an alternative to mounting your Amazon EFS file system with the DNS name, Amazon EC2 instances can mount a file system using a mount target’s IP address\. Mounting by IP address works in environments where DNS is disabled, such as VPCs with DNS hostnames disabled, and EC2\-Classic instances mounting using ClassicLink\. For more information on ClassicLink, see [ClassicLink](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.

You can also configure mounting a file system using the mount target IP address as a fallback option for applications configured to mount the file system using its DNS name by default\. When connecting to a mount target IP address, EC2 instances should mount using the mount target IP address in the same Availability Zone as the connecting instance\.

You can get the mount target IP address for your EFS file system through the console using the following procedure\.

**To obtain the mount target IP address for your EFS file system**

1. Open the Amazon Elastic File System console at [https://console\.aws\.amazon\.com/efs/](https://console.aws.amazon.com/efs/)\.

1. Choose the **Name** value of your EFS file system for **File systems**\.

1. In the **Mount targets** table, identify the **Availability Zone** that you want to use to mount your EFS file system to your EC2 instance\.

1. Make a note of the **IP address** associated with your chosen **Availability Zone**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/efs/latest/ug/images/mount-ip-600w.png)

You can specify the IP address of a mount target in the `mount` command, as shown following\.

```
$ sudo mount -t nfs -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport mount-target-IP:/   ~/efs-mount-point  
```
# Mounting with an IP Address through AWS Cnf<a name="mounting-fs-mount-ip-addr"></a>
You can also configure mounting a file system using the mount target IP address via AWS CloudFormation.

Before mounting to a target Ip address, you have to create EFS with a specific IP address. It's just like you can select an IP from the range of Ip's subnets have.

*Creating-EFS-IP-addr.config*
```
option_settings:
  aws:elasticbeanstalk:customoption:
    EFSVolumeName: "EFS_Dir"
    VPCId: "xxxx"
## Subnet Options
    SubnetA: "xxxx"
  aws:elasticbeanstalk:application:environment:
    FILE_SYSTEM_ID: '`{"Ref" : "FileSystem"}`'
    MOUNT_TARGET_IP: 'xxxx'
    MOUNT_DIRECTORY: '/efs'
    REGION: '`{"Ref": "AWS::Region"}`'
Resources:
## Mount Target Resources
  MountTargetA:
    Type: AWS::EFS::MountTarget
    Properties:
      FileSystemId: {Ref: FileSystem}
      IpAddress: xx.xx.xx.xx
      SecurityGroups:
      - {Ref: MountTargetSecurityGroup}
      SubnetId:
        Fn::GetOptionSetting: {OptionName: SubnetA}

##############################################
#### Do not modify values below this line ####
##############################################

  FileSystem:
    Type: AWS::EFS::FileSystem
    Properties:
      FileSystemTags:
      - Key: Name
        Value:
          Fn::GetOptionSetting: {OptionName: EFSVolumeName, DefaultValue: "EFS_Dir"}

  MountTargetSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Security group for mount target
      SecurityGroupIngress:
      - FromPort: '2049'
        IpProtocol: tcp
        SourceSecurityGroupId:
          Fn::GetAtt: [AWSEBSecurityGroup, GroupId]
        ToPort: '2049'
      VpcId:
        Fn::GetOptionSetting: {OptionName: VPCId}
```

*Mounting-EFS-IP-addr.config*

```
option_settings:
  aws:elasticbeanstalk:application:environment:
    MOUNT_TARGET_IP: 'xxxxx'
    MOUNT_DIRECTORY: '/efs'

packages:
  yum:
    nfs-utils: []
    jq: []

commands:
  01_mount:
    command: "/tmp/mount-efs.sh"

files:
  "/tmp/mount-efs.sh":
      mode: "000755"
      content : |
        #!/bin/bash

        EFS_MOUNT_DIR=$(/opt/elasticbeanstalk/bin/get-config environment | jq -r '.MOUNT_DIRECTORY')
        EFS_MOUNT_TARGET_IP=$(/opt/elasticbeanstalk/bin/get-config environment | jq -r '.MOUNT_TARGET_IP')

        echo "Mounting EFS filesystem ${EFS_DNS_NAME} to directory ${EFS_MOUNT_DIR} ..."

        echo 'Stopping NFS ID Mapper...'
        service rpcidmapd status &> /dev/null
        if [ $? -ne 0 ] ; then
            echo 'rpc.idmapd is already stopped!'
        else
            service rpcidmapd stop
            if [ $? -ne 0 ] ; then
                echo 'ERROR: Failed to stop NFS ID Mapper!'
                exit 1
            fi
        fi

        echo 'Checking if EFS mount directory exists...'
        if [ ! -d ${EFS_MOUNT_DIR} ]; then
            echo "Creating directory ${EFS_MOUNT_DIR} ..."
            mkdir -p ${EFS_MOUNT_DIR}
            if [ $? -ne 0 ]; then
                echo 'ERROR: Directory creation failed!'
                exit 1
            fi
        else
            echo "Directory ${EFS_MOUNT_DIR} already exists!"
        fi

        mountpoint -q ${EFS_MOUNT_DIR}
        if [ $? -ne 0 ]; then
            echo "mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 ${EFS_MOUNT_TARGET_IP}:/ ${EFS_MOUNT_DIR}"
            mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 ${EFS_MOUNT_TARGET_IP}:/ ${EFS_MOUNT_DIR}
            if [ $? -ne 0 ] ; then
                echo 'ERROR: Mount command failed!'
                exit 1
            fi
            chmod 777 ${EFS_MOUNT_DIR}
            runuser -l  ec2-user -c "touch ${EFS_MOUNT_DIR}/it_works"
            if [[ $? -ne 0 ]]; then
                echo 'ERROR: Permission Error!'
                exit 1
            else
                runuser -l  ec2-user -c "rm -f ${EFS_MOUNT_DIR}/it_works"
            fi
        else
            echo "Directory ${EFS_MOUNT_DIR} is already a valid mountpoint!"
        fi

        echo 'EFS mount complete.'
```
