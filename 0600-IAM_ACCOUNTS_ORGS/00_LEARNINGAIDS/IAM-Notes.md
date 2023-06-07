# IAM identity policies
- identity policy: applies to identity, list of security statements
  - identity: user, group, or role
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
    - once thru auth proc, *Authenticated Identity*
  - Authorization: IAM knows which policies apply to actions
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