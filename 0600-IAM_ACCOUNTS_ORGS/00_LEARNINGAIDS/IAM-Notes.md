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

