# DNS 101
- like a big lookup db, maps domain names to IP addresses
- concerns
  - risk: hacking, disasters
    - mitigation: many servers. But huge data set, each server needs its own copy
  - scale: huge data volume
    - mitigation: delegate parts of data set to other orgs
- Key terms
  - *DNS Zone*: db containing *DNS records*, e.g. *.netflix.com
  - *Zone File*: file containing zone
  - *Name Server (NS)*: 1+ Zones, 1+ Zone Files
  - *Authoritative*: real/genuine records for domain. Single src of truth for zone
  - *Non-Authoritative*: i.e. cached, e.g. by local router, internet provider
- Hierarchical structure
  - DNS Root zone
    - knows name servers for TLD
    - every DNS client knows & trusts
    - 13 IP addresses (each reps many servers)
      - indep. orgs manage hw
        - Internet Corporation for Assigned Names and Numbers (ICANN) - operates 1
        - others: NASA, University of Maryland, Verisign
      - Root zone - db file - managed by Internet Assigned Names Authority (IANA)
    - stores not much info, but critical
      - high level info on TLDs
        - TLD types: country code or generic
        - pts to name servers for TLD registries
        - authoritative records for TLD
  - TLD
    - knows name servers for domains
    - stores high level info on domains in TLD
      - only netflix.com, not detailed info like www.netflix.com
  - Domain
    - contains records for domains
    - has zone, name servers, zone file
      - authoritative for domain
    - records for domain: e.g. www.netflix.com


# Walking the (DNS) tree
- What do we want from DNS? DNS's job is to locate authoritative zone which hosts DNS records you need
  - tree structure
- example: user's PC wants to visit domain, e.g. www.netflix.com
  1. check local cache & Hosts file
  2. query *DNS resolver*: type of DNS server, runs on home router or internet provider
  3. DNS resolver checks local cache (non-authoritative)
  4. Contact Root zone
      - NS records for TLD
  5. Root zone returns TLD NS
  6. Resolver queries TLD NS for domain
  7. TLD registry returns domain NS
  8. Resolver queries NS for domain (authoritative)
  9. returns DNS record
      - resolver caches result
      - Note: this is CNAME record - need to lookup again!
  10. return query result to client
- Takeaways
  - no single NS has all answers
  - every query gives next step

# Registering a domain
- Domain Registrar and DNS hosting provider can be same or different companies
- steps
  1. pay for domain from registrar 
  2/3. create DNS zone, get NS's if hosting provider is same company, need to do separately if different
  4. register with TLD
  5. add to .com TLD zone (root zone)
  6. domain is live