https://learn.cantrill.io/courses/1820301

identities = users

# Creating Accounts
- general account = management account
- root user created along with general account
- creating general & production accounts
- create as many accounts as needed. Best to create new accounts for new courses
- use gmail's dynamic alias for unique emails
- Creating account
  - email: *{{email}}+trainingsaa{{account}}@gmail.com*
  - account name: *{{initials}}-TRAINING-SAA-{{account}}*
  - check activate iam access in account settings
  - set up mfa
    - account => security credentials
    - device name: *TRAINING-SAA-{{account}}*
    - log out & log back in to test mfa
  - *During course - make sure in N.Virginia region (or global)*
  - budget
    - settings => billing dashboard => billing preferences
      - check receive pdf invoice by email, receive free tier usage alerts, receive billing alerts
    - budgets
      - use template
      - monthly cost or zero spend budget
        - if monthly cost, name: *Monthly10USDBudget*
        - budgeted amount: 10
        - email recipients: use email

# Identity and Access Management (IAM) basics
- *data always secure across all regions*
- each account has an instance of iam
- iam has all permissions as root
  - root almost synonymous with aws account - account created with root
- 3 types of *identities*
  - *User*: individual thing to access
  - *Group*: Group of users
  - *Role*: used to grant uncertain # of entities to service in account
- IAM policy: create doc that allow/deny only when attached to an identity
- *3 main jobs of IAM*:
  - ID provider
  - authentication of identities
  - authorization
- IAM is free but has limits, which can be important for real world usage
- global service / global resilience
- allows/denies identities on aws account, but no direct control for ext acc's
- identity federation: takes existing acc's & use them indirectly to access aws resources

# Adding an IAM admin
- should create an admin account to replace root for everyday usage
- IAM
  - create account alias: to make sign-in url more readable
    - alias: *{{initials}}-training-saa-{{account}}*
  - Users => add users
    - iam user name: *iamadmin*
    - check provide access to aws management console
    - "I want to create an IAM user"
      - will use Identity Center later
    - uncheck users must create a new password at next sign-in
    - att policies directly
      - select AdministratorAccess
    - add mfa
      - device name: *TRAINING-SAA-{{account}}-IAMADMIN*
      - log out & log back in to test mfa

# IAM access keys
- iam access keys: authentication on the command line
- long term credential = don't change auto or regularly
- iam user has 1 username & 1 pw
  - credential has public & private part
    - username = public, pw = private
  - can have 2 access keys
    - created, deleted, inactive, active
    - 2 parts
      - access key id
      - secret access key: longer then acc key id
    - access thru cli needs both parts
    - 2 access keys to allow for rotation
- roles don't use access keys
- root can have access key but not recommended
- create IAM access key
  - security credentials => create access key => CLI
  - desc tag: *LOCAL CLI IAMADMIN-{{account}}*
- install cli v2: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
  - `aws configure --profile iamadmin-{{account}}`: configure named configuration profile
    - enter acc key ID & secret acc key
    - default region: us-east-1
    - default output: skip
  - *aws s3 ls --profile iamadmin-{{account}}*
    - doing *aws s3 ls* will use default profile (get error if no default)

# Public vs Private services
- public/private from network perspective
- private AWS service: runs within VPC
  - connect via lan
- private: accessed w/ public endpoints (e.g. S3)
  - still need to be authorized to access
  - connect via isp
- 3 network zones
  - public internet
  - aws public (e.g. s3)
  - aws private (vpc) (e.g. ec2)
    - can also be assigned publicly accessible ip (projecting into aws public zone)

# AWS global infra
- *aws region*
  - not necessarily mapped to real geo region, creation of amazon
  - full deploy of aws infra (computer, storage, db products, ai, analytics, etc)
- edge location
  - smaller than regions
  - only content distrib. services & some types of edge computing
- properties
  - geographic separation: isolated fault domain
  - geopolitical ": diff. governance (governed by region)
    - data will stay in region unless explicitly moved
  - location control for perf.
- *availability zone*
  - region contains 1+ AZs
  - isolated infra for: compute, storage, networking, power
  - can put resources over mult. AZs for resiliency
  - VPC: spans multiple AZs
  - https://aws.amazon.com/about-aws/global-infrastructure/regions_az/
- *resilience level*
  - *globally resilient*: data replic. among mult. regions (e.g. iam, route 53)
  - *regionally resilient*: data replic among mult AZs
  - *AZ resilient*: resiliency to hw failures

# Virtual Private Cloud (VPCs)
- create private network that other private services will run from
- within 1 account & 1 region
  - *regionally resilient*
- 2 types
  - *default vpc*
    - *only 1 per region*
    - auto created & configured
  - *custom vpc's*
    - need to configure e2e, 100% private by default
- how vpc works in region
  - *VPC CIDR*: start & end IPs
    - incoming conn's (if allowed) go to cidr
    - *default vpc has 1 cidr (custom has multiple)*
- default vpc
  - not very flexible - best practice to use custom
  - 1 per region, can be deleted & recreated
      - some services assume default vpc exists!
  - CIDR always same: `172.31.0.0/16`
    - 1 subnet per AZ
      - `/20` subnet for AZ
        - the higher the slash number, the smaller the subnet
        - up to 16 AZs per region
  - auto created: Internet Gateway (IGW), Security Group (SG), NACL
  - subnets are assigned public IPv4 addr's - *specific to default!*

# Elastic Compute Cloud (EC2)
- IAAS - unit of consumption is instances
- for compute req's
- private aws svc by default
- uses vpc. Set to launch into subnet
  - AZ resilient
- config size & capabilities
- on-demand billing by the second
- storage: local or Elastic Block Store (EBS)
- main states:
  - running
    - charges: cpu, memory, disk, networking
  - stopped
    - charges: disk
      - storage still allocated, so will see EBS bills
  - terminated: non-reversible (deleted)
    - charges: none

## Amazon Machine Image (AMI)
- create EC2 instance or be created from EC2 instance
- like server image
- permissions
  - public (e.g. Windows, Linux)
  - private
    - implicit: owner implicitly has permissions
    - explicit: specify AWS accounts
- root volume: boots OS
- block device mapping: maps other volumes (e.g. data volume) to device ID

## Connecting to EC2
- windows: RDP
  - `port 3389`
- Linux: SSH
  - `port 22`
- SSH key pair
  - private: only gen'd once, keep safe
  - instance keeps public key, matches private key

## Demo: EC2
- ec2 => key pairs => create key pair
  - name: A4L
  - file format
    - mac/linux/newer windows versions: choose pem
    - putty/older windows: choose ppk
- instance => create instance
  - Name: MyFirstEC2Instance
  - choose A4L key pair
  - SG name: MyFirstInstanceSG
    - SG like mini-firewall
- instance takes some time to create. Want to see Instance state: Running, Status check: 2/2 checks passed
- Connect - EC2 Instance Connect
  - use web console
- Connect - SSH client
  - set file permissions on pem file for windows: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html#connection-prereqs-private-key
  > Open File Explorer and right-click on the .pem file. Select Properties > Security tab and choose Advanced. Choose Disable inheritance. Remove access to all users except for the current user. 
- Cleanup
  - Terminate EC2 instance
  - Delete SG

# S3 Basics
- global storage platform
- regionally resilient
  - some ability to replic. data b/t regions
- public svc
- unlimited data scale, multi-user
- 2 main obj's
  - *Object*: data that S3 stores (e.g. pictures, large scale datasets)
    - like a file
    - components
      - *key*: like filename. Key + Bucket = uniquely access object
        - *note: in aws, no access initially (including for public services) except for account root user*
      - *value*: content being stored
        - range: **0 bytes - 5 TB**
      - others: version id, metadata, access control, subresources
  - *Bucket*: containers for obj's
    - created for aws region
    - generally where options, permissions set
    - data in bucket has primary home region, doesn't leave unless configured
    - blast radius = region
      - *blast radius*: effect of disaster is contained within
    - *bucket names must be globally unique* - most other aws things are unique in region or account
      - *3-63 chars, lowercase, no underscores*
      - *start w/ lowercase or number*
      - *can't be ip formatted (like 1.1.1.1)*
      - *limit of # buckets for account: 100 soft limit, 1000 hard limit (thru support requests)*
        - i.e., can't use for unlimited # of users, use prefixes instead
    - can hold unlimited # of objects
    - flat structure
      - however, when ls it looks like folders
      - prefix: e.g. /old/Koala1.jpg. /old/ is part of object name
      - s3 doesn't know about file types
- default storage solution
- S3 is object store: not file or block store
  - *object store*: access whole obj at a time
  - *file store*: access files on file system
  - *block store*: virtual hdd's
    - mount point for VMs - can't mount S3 as K:\ or /images
    - generally limited to 1 thing accessing at a time
- good for large scale data storage, distribution, upload
- good for offloading data, e.g. images for blog or videos
  - use instead of storing on EC2 instance (expensive)
- default for input/output for many aws svc's

## Demo: S3
- creating S3 Bucket - Global namespace, so don't need need to pick region
- Block Public Access settings for this bucket
  - S3 buckets are private to owner by default
  - safety feature - uncheck will allow granting public access (doesn't make public)
    - will see "Objects can be public" under Access when unchecked
- *Amazon Resource Name (ARN)*: all resources in aws have unique ID
  - format: arn:{{partition}}:{{svc_name}}:::
    - partition: most aws resources in most regions, will say "aws"
    - svc_name: e.g. s3
    - ::: - optional vals (region / account number) - s3 is globally unique so not needed
- Create Folder - not really folder, just a file emulating folder
- Object URL: when copy and open, will get AccessDenied error, b/c no auth.
  - Open button will authenticate
- Cleanup
  - Empty the bucket
  - Delete

# CloudFormation (CFN) Basics
- IaC (Infrastructure as Code)
  - use templates to predictably create infra
- JSON/YAML
- parts (YAML example)
  - Resources: >= 1. Only mandatory part
  - Description: free text
    - *if also using AWSTemplateFormatVersion, this must come directly after*
  - AWSTemplateFormatVersion: used to ext the std over time. If omitted, value is assumed
  - Metadata: set how the UI presents template
  - Parameters: prompt user for more info
  - Mappings: create lookup table
  - Conditions
  - Outputs: output after template applied
- How it's used
  - Resources: contains *Logical Resources*, e.g. Instance
    - Logical Resource contains
      - Type, e.g. AWS::EC2::Instance
      - Properties
  - create Stack
    - contains all Logical Resources. Instantiation of template.
    - for every logical resource, creates corresp. Physical Resource
    - CFN keeps logical & physical resources in sync

## Demo: CFN
- CFN function
  - !Ref: Ref's another part of template
  - !GetAtt: more capable !Ref
- [CloudFormation Resource Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- Create Stack
  - upload template: example ec2instance.yaml
    - uploads to auto-created S3 bucket (prefix CF)
  - name: cfndemo1
  - check "The following resource(s) require capabilities: [AWS::IAM::Role]"
- EC2 *Session Manager*: alt to key pair & ssh
  - requires permissions (instance role)
  - created with SessionManagerRole in template
- Delete Stack
  - deletes logical resources, then corresp. physical res's

# CloudWatch (CW)
- collects & manages operational data
  - operational data: genreated by env. detailing performance, logging data
- 3 products in one
  - Metrics (or CloudWatch itself): aws products, on-premises env's, other cloud platforms
    - some metrics run natively (on aws products)
    - some need CloudWatch Agent
      - outside aws, on-prem
      - things not exposed to aws, e.g. processes running on EC2
  - CloudWatch Logs
    - need CloudWatch Agent for custom logs, on-prem, things not exposed to aws
  - CloudWatch Events: event hub
    - AWS Services & Schedules
    - gen's events to respond to aws svc (e.g. EC2 inst. stops)
    - gen's events on timer
- *namespace*: container for monitoring data
  - aws services reserve convention: AWS/service
- *metric*: time ordered set of data points
  - data point
  - dimension: key/value pairs within datapoint for diff things/perspectives within same metric
    - Data for a metric (e.g. CPUUtilization) from many instances
    - EC2: InstanceId and InstanceType
- *alarm*: linked to metric. Takes action on criteria
  - states: OK, ALARM, INSUFFICIENT_DATA (starts here while gathering data)

## Demo: CW Simple Monitoring
- create EC2 instance
  - security group name: cloudwatchSG
  - Detailed CloudWatch monitoring: turn on. Will have charges
- create Alarm
  - metric: EC2 => per-instance metrics => find instanceID of EC2 instance
  - conditions: Static => >= 15%
  - SNS: remove (useful for prod)
  - name: cloudwatchtestHIGHCPU
- connect to EC2 instance
  - install stress: `sudo yum install stress -y`
    - `stress -c 1 -t 3600`
      - enter # of virtual CPUs - t2.micro has 1
    - after a few mins, cloudwatchtestHIGHCPU alarm will go from OK to ALARM
- cleanup
  - delete alarm
  - terminate instance
    - wait until in terminated state
  - delete cloudwatchSG security group

# Shared Responsibility Model
![SharedResponsibilityModel-1.png](SharedResponsibilityModel-1.png)
![SharedResponsibilityModel-2.png](SharedResponsibilityModel-2.png)
- keep this in mind during learning. Which parts you are responsible for, and which parts AWS

# HA vs FT vs DR
- *High Availability (HA)*: maximize uptime
  - 99.9: Three 9's ≈ 9 hours/year downtime (8.77)
  - 9.999: Five 9's ≈ 5 mins/year downtime (5.26)
- *Fault Tolerance (FT)*: continue operating despite failures
  - much more complex & expensive
  - HA = spare equipment + automated repair
  - FT: Need HA + levels of redundancy (tolerate failures)
  - example: 4X4 w/ extra wheel. If wheel breaks, can pull over & fix
- misunderstandings
  - need HA but choose FT: wasting money
  - need FT but choose HA: potentially putting life at risk
  - example: plane. If engine breaks, can't pull over and fix. Have redundant systems
- *Disaster Recovery (DR)*: enable recovery following disaster. Use when HA & FT fail.
  - 2 parts: pre-planning & DR Process
  - have backup data in 2nd location
  - have backup of processes in 2nd location
  - designed to keep crucial/irreplacable parts of system safe
  - example: pilot/passenger ejection systems
  - exam: tests how quickly service can recover

# Route 53
- managed DNS svc. Like DNS as a svc
- responsibilities
  1. register domains
  2. host zone files
- *global svc*, single database
- *globally resilient*
- needs to scale. needs to be FT
- PIR: .org domain registry
- register domain steps
  - Route 53 checks with registry if domain name is available
  - creates *zone file*: db containing dns info
  - allocate name servers for zone
  - get name server records from registry, add into zone file
    - *name server records*: allows admin access
- zone files
  - 4 *name servers* for zone
  - Route 53 term for zone files: *hosted zone*
  - can be AWS public or private (VPCs) zone
  - stores dns records (Route 53 term: *recordsets*)

# Demo: Route 53
- registered animals4life4u.org

# DNS Record Types
- Record Types
  1. *Name Server (NS)*
  2. *A (ipv4) / AAAA (ipv6) Name*
    - map host to ip addr
  3. *CNAME (Canonical Name)*
    - usage: point multiple services such as ftp, mail, etc to same server
    - if ip addr changes, only need to change A name
    - exam: CNAME can't pt at ip addr, only other names
  4. *MX*: important for mail
    ![diagram](DNS-RecordTypes-5.png)
    - 2 parts
      - priority: lower is higher priority
      - value
        - host (no dot on the right): same zone
        - fully qualified domain name (ends with dot): same or outside zone
  5. *TXT*: add arbitrary text. Usages: prove domain ownership, fight spam
- TTL: # of seconds, can be set on DNS records
  ![DNS-RecordTypes-7.png](DNS-RecordTypes-7.png)
  - low values mean more queries to NS, but more control
  - high values mean fewer queries but less control if you need to change DNS records
  - DNS resolver could ignore TTL value!
  - lower TTL in advance of changing DNS records
