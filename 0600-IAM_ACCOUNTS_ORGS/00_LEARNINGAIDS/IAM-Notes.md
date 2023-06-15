# IAM identity policies
- identity policy: applies to identity, list of security statements
  - identity: user, group (not a true identity), or role
- statement contains:
  - Sid
  - Effect
  - Action
  - Resource: use ARN
- match Action & Resource for it to take effect
- *priority of overlapping statements* - exam
  1. Explicit DENY
  2. Explicit ALLOW
  3. Default DENY (implicit)
    - except for account root user, *all identities start with no access*
- Granting access
  - *inline policy*: assign policy to accounts individually
    - not best practice; all independent JSONs
    - use for special circumstances
  - *managed policy*: create policy & attach to identities
    - use for baseline access
    - 2 types
      - *AWS managed policy*
      - *customer managed policy*

# IAM Users
- *IAM Users*: Identity used for *anything requiring long-term access*, e.g. humans, apps, service accounts
  - *Principal*: entity trying to access AWS account
  - Authentication: principal authenticates with IAM to access resources
    - "You are who you say you are"
    - once thru auth proc, *Authenticated Identity*
  - Authorization: IAM knows which policies apply to actions
    - "Can you access this resource?"
- ARNs: uniquely id resources within any aws account
  - naming
    - arn:partition:service:region:account-id:resource-id
    - partition: usually aws
    - s3 omits region & account-id because buckets are globally unique
  - example: S3
    - arn:aws:s3:::catgifs - bucket (not objects in bucket)
    - arn:aws:s3:::catgifs/* - objects in bucket (not bucket itself)
- *5000 IAM users per account* - exam
- IAM user can be a *member of 10 groups* - exam
- not suitable for internet-scale apps, large orgs / org merges
  - may use IAM roles or identity federation

# Demo: Simple Identity Permissions

# IAM Groups*: 
- *IAM Groups*: containers for users - for organizing users
  - no credentials - *Can't login to group*
  - user can be member of mult. groups
  - can att. managed & inline policies
  - no limit for # of users in a group
  - there is no all-users group (group that auto has all users in acc)
  - no nesting groups
    - group can have users & perm's
  - limit of *300 groups per account* (can ext. with support ticket)
- *resource policies* - introd. later
  - allows identities access to resource
    - only users & roles
    - not groups - **groups aren't a true identity**
      - *can't be ref'd as a principal in a policy*
  - ref to id by arn
- Group limitation: can contain users & att policies, that's it

# Demo: Group Permissions
- cleanup
  - detach policy from group
  - delete group
  - empty & delete s3 buckets
  - delete cf stack

# IAM Roles
- authen. & author. - single principal: use IAM user
- unknown # or multiple principals: use *IAM Role*
  - also good for >5000 principals (limit for # of users in acc)
- temporary: Role doesn't rep individual, reps level of access
  - identity assumes role temporarily, then stops
- can att 2 types of policies
  - *Trust Policy*: which id's can assume role
    - can ref id's in same/other aws acc's, aws services, anon users, other types of id's (fb, google)
    - gives identity temp security cred's
      - will expire & need to be renewed by reassuming role
      - uses *Secure Token Service (STS)*
        - sts:AssumeRole
  - *Permissions Policy*
- will use aws organizations later to manage multi accounts (allow 1 login to access multi accounts without logging in again)
- can give access to certain resource or whole account
  - simplifies multi-account management

## When to use IAM Roles
- common use case: aws services
  - e.g. lambda: function as a service
    - No perms as default. Can add access keys, or better, use iam role (lambda execution role)
      - lambda execution role
        - execution role has a trust policy to trust lambda svc
        - permissions policy for aws products
  - reasons to use role
    - access key: security risk, hard to rotate keys when expire
    - unknown # of entities using (could be 1 or 100 lambda functions)
- use case: emergency situation
  - e.g. role to promote readonly to modify
    - "break glass": when role is access, done on purpose (confirmation), and access logged (can see it was used)
- use case: adding aws to existing corp. env.
  - *use to reuse existing identities with aws.*
    - *Ext. accounts can't be use directly*
  - existing id's (e.g. MS Active Directory)
    - assign role to id, which gen's temp credentials for accessing aws resource
  - want to offer SSO for AWS, or >5k id's
  - *identity federation*: using a role to give perm's to ext. id. provider
- use case: ride sharing app that uses aws db
  - considerations
    1. *when interacting with aws resource, must use aws identity*
    2. 5k iam user limit per account
  - *Web Identity Federation*: sign in with Twitter, FB, Google, etc. Allow ID to assume IAM role
    - advantages
      1. security - no aws creds stored on app
      2. use existing accounts instead of making user make a new one
      3. scales
- use case: cross-account access
  - work in multi account env
  - example: your account access s3 bucket on partner org's account
    - use role in partner org's account, instead of partner org having to create accounts for 1000's of your org members

## Service-linked Roles
- *Service-linked role*: iam role linked to specific aws svc
  - predef'd by svc
  - can't delete until no longer req'd (vs. normal roles, can delete)
  - role separation: separate job roles
    - e.g. 1 group creates roles, 1 group uses them
    - *PassRole*: pass existing role in aws svc, but not create or edit role
    - to access svc, need iam:ListRoles and iam:PassRole
    - can be use to access svc, then pass a Role with higher access, to access things your id can't normally access
    - this is what CloudFormation uses in stack

# AWS Organizations
- used to manage many AWS accounts
  - each account has id's, separate billing
- *standard aws account*: aws account not within an org
- use std aws account to create org
- std aws account => *Management Account* (prev. Master)
- use mgmt acc to invite std aws acc's to join org
  - std account => *Member Account* when joining org
- AWS Org has 1 Management Account, and 0+ Member Accounts
- structure
  - *Organization Root*: contains aws accounts
    - not aws account root user (admin user of aws account)
    - can contain OUs
  - *Organizational Unit (OU)*: contains aws acc's, member/mgmt accounts, other OUs
- *consolidated billing*
  - billing removed from member accounts, instead uses mgmt acc
  - *Payer Account*: contains payment method for org
    - *Master = Management = Payer Account* in org
  - single monthly bill covers mgmnt account & all member acc's in org
- another benefit: certain svcs get cheaper when used more (volume discount) or pay in advance (reservation)
  - consolidate benefits from each acc
- *Service Control Policy (SCP)*: service. Restrict what acc's in org can do
