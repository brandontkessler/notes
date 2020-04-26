3 ways to use aws:
1. Management console (GUI)
2. Command line interface (CLI)
3. Software development kits (SDKs)

#### EC2
Elastic compute cloud
* EC2 instances
* Pay as you go

**Demo**
* Log into console
* Choose region
* Click services -> EC2 -> launch instance
	* Select and AMI - amazon Linux
* Choose an instance type
	* Low end has a free tier eligible
* Configure instance details
	* Build 1 instance
	* Default network, subnet and public IP
* Add storage
* Add tags
	* Name - EC2-Demo
* Configure security group
	* Creates firewalls rules
	* One for SSH
	* One for HTTP
	* Change security group name to: “ssh-http”
* Review instance launch
	* Click launch
* Create new key pair
	* Name it EC2-Demo
	* Download private key and save locally
* Launch instance

Default user is “ec2-user”
`ec2-user@ip address`

Ssh in

#### Elastic block store (EBS)
Storage system using pay as you go volumes

In volumes under elastic block store

They attach to ec2 instances

**steps**
* Create volume
* Change availability zone to the zone where ec2 instance is located
* Volume type - general purpose ssd will only charge for size in gbs
* Create volume
* After created and back in main menu, when volume is highlighted, click actions -> attach volume
* Add the instance and device and hit attach

Ssh into the ec2 instance

`$ lsblk` - shows the new volume that was created

`$ sudo mke2fs /dev/xvdb`- given a volume name of xvdb this creates a file system in the xvdb volume

This can then be mounted on the Linux machine

`sudo mount /dev/xvdb /mnt` - mounts the xvdb volume and creates a mnt folder in the root

This can be unmounted with the unmount command

It can be detached and attached to other instances in the same availability zone

#### Simple storage system (s3)
* Cloud storage

Each object stored in a bucket needs a key which generally represents the file path

S3 is stored in 3 locations so if two experience mass data loss it’s still secure

Only billed for what you use

**demo**
* Go to S3 section
* Click create bucket
* Bucket name must be dns compliant: “amazing-bucket-1”
* Region should be close to wherever ec2 instance was set up
* Hit create
* Click on it
* Terminal cli: `aws s3 cp demo.txt s3://amazing-bucket-1/hello.txt`
* Terminal cli: `aws s3 sync some-folder s3://amazing-bucket-1/files`

#### Global infrastructure
**Regions**
* 2 or more availability zones
* Can deploy in multiple regions
* Ex. If business requires to store in a specific region but customers typically access assets from another you can deploy business assets in one region and customer assets in another.
* Or the same in multiple regions to minimize latency for all customers regardless of location

**availability zones**
* Physically and logically independent facilities to store data. Many are located within the same region.
* When one goes down, others can handle requests

**edge locations**
* Amazon cloud front - used to deliver faster to customers

#### Amazon virtual private cloud (VPC)
**intro**
* Pay as you go, on demand compute
* A private, virtual network
* Complete control of network configuration
* Ec2 can be deployed into the VPC

**features**
* Builds upon global infrastructure (regions and availability zones)
* Internet gateways
* Subnet

**example**
* Select a region
* Create VPC - name : Test-VPC
* Ip - 10.0.0.0/16
* Subnet A1 - 10.0.0.0/24 - to live in availability zone A
* Subnet B1 - 10.0.1.0/24 - also in availability zone A
* Added internet gateway called Test-IGW
* subnet A1 will become public in which non local traffic will be routed through the internet gateway
* B1 will be private subnet

#### Security groups
* Act like firewalls
* Control of access

**example**
A multi tiered security group could have one for a web tier, one for an application tier, and one for a database tier. The web tier accepts web traffic only. The application tier accepts traffic only from the web tier. And the database tier only accepts traffic from the application tier.

**demo**
* Click EC2
* Click security groups in navigation pane
* Click create security group
* By default all inbound traffic is denied and all outbound traffic is accepted
* Name: web-server-sg
* Description: allow web traffic
* For inbound select add rule
* Type: http, protocol: tcp, port: 80, source: 0.0.0.0/0 - this ip allows all internet traffic
* Add another rule
* Type: https
* Click create
