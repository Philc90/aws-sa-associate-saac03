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
  - During course - make sure in N.Virginia region (or global)
  - budget
    - settings => billing dashboard => billing preferences
      - check receive pdf invoice by email, receive free tier usage alerts, receive billing alerts
    - budgets
      - use template
      - monthly cost or zero spend budget
        - if monthly cost, name: *Monthly10USDBudget*
        - budgeted amount: 10
        - email recipients: use email

# IAM (Identity and Access Management) basics
- *data always secure across all regions*
- each account has an instance of iam
- iam has all permissions as root
  - root almost synonymous with aws account - account created with root
- 3 types of identities
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
    - *default vpc has 1 cidr (custom has mult)*
    - always same: **172.31.0.0/16**
    - 1 subnet per AZ