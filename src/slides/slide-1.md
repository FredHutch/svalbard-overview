class: center, middle

# Secrets Management

---

# Itinerary

 - What's a Secret
 - The Case for Managing Secrets
 - Key Capabilities Needed
 - Current Solutions
 - Available Solutions
 - Hashicorp Vault Overview
 - The Case for Hashicorp Vault
 - Options for Implementation

---

# What is a Secret

 - Information requiring authorization for access
 - Big Secrets:
   - Root password
   - CA root keys
 - Little Secrets
   - hostnames
   - configuration parameters

???

Password recovery questions: high school, favorite color, are these now secrets? How well are those secrets kept?

---

# A Diversion of Turtles

From a [presentation](http://danielsomerfield.github.io/turtles/) by D. Somerfield at OWASP.

All secret management solutions rely on secrets that can't be distributed by
itself.  It's turtles all the way down folks.

<img src="img/yertle.jpg" style="width: 75%; height: 75%"/>

???

 - Somerfield used the imagery of the notorious "turtles all the way down"
   comment from an apocryphal story illustrating the problems of infinite
   regression.
 - Variation on "quod ipse custodis"
 - No single solution exists

---

# Why Manage Secrets

> Three can keep a secret if two are dead -B.Franklin

Secrets are important:
  - things fail when secrets are missing/unavailable/forgotten
  - bad things happen when secrets are misused

We need secrets to do our jobs- we need safe storage and sensible access
  - access to secrets is a bottleneck in development and automation tasks

???

 - NB: We are "managing" secrets now... a post-it on the keyboard is one way of
   managing
 - Re-title as "improving our secrets management!"
 - automating access to secrets necessary for AWS auto-scaling, many CI/CD
   workflows, etc. etc.

---

# Why Manage Secrets

 - Increasing number of applications accessing sensitive data:
   - means secrets distributed over a wider landscape
   - means increased exposure to threats

 - Classic secret management (i.e. encrypted file on a share) will not scale
   - Application access to secrets complicated
   - Rotation and invalidation of secrets difficult and slow process

> 

---

# Key Capabilities Needed

> What do we need to manage secrets in an increasingly distributed and
> automated environment?

 - Safe storage at rest
 - Secure communication
 - Robust authentication
 - Flexible role-based authorization
 - Easy rotation of secrets

---

# Key Capabilities Needed

> What would put us above the bare necessities?

 - Temporary, ephemeral secrets
 - Auditing access

???

Ephemeral secrets are secrets that exist for a short period of time
 - task or time based
 - reduces risk, a compromised secret only works for a short while

An argument can easily be made that auditing is a core capability

---

# Current Solutions

 - Meatspace
   - an administrator with knowledge in her head
 - Simple file
   - text file with using file permissions to control access
   - sometimes encrypted
 - Chef Vault
   - a new capability, stores encrypted secret in databag

???

Solutions currently in use at Hutch, may have missed a few

---

# Available Solutions

Many exist:
  - Chef Vault 
  - Git-Crypt
  - Blackbox
  - Keywhix
  - [others](https://gist.github.com/maxvt/bb49a6c7243163b8120625fc8ae3f3cd)

???

 - all have plus/minus
 - keywhiz/blackbox more-or-less specific to author's environment
 - git-crypt and others encrypt at rest on github, AAA features dependent on
   github

---

# Hashicorp Vault Overview

Hashicorp Vault is something of a game-changer on the secrets management landscape.

 - stores in an encrypted backend, provides access via HTTP API
 - multiple authentication options
 - multiple authorization options
 - multiple storage options
 - multiple _secret generators_
 - provides AAA and auditing, ephemeral and leased secret management

This all comes at a cost: _complexity_

???

 - there is a command-line client, it uses the HTTP API
 - _secret generators_ and ephemeral ticket management is where Vault
   distinguishes itself

---

# Hashicorp Vault Overview

<img src="img/hash-vault-arch.png" style="width: 100%; height: 100%"/>

---

# Hashicorp Vault Overview

 - Accessing Secrets is /always/ done via HTTP API
    - command line vault client
    - `curl` post/get
    - Python/Ruby/Go/... libraries

Authentication by attribute (IP), token, etc.

???

 - write our own methods for retrieving secrets using API (app-specific)
 - use Chef to write configuration files (lacks immediacy)

---

# Hashicorp Vault Overview

## Consul Templates

> Consul Template queries a Consul instance and updates any number of specified
> templates on the filesystem. As an added bonus, Consul Template can execute
> arbitrary commands when a template update completes.

 - Consul daemon runs and updates Consul templates as information changes
 - Vault is a "first class" client for Consul and builds right into templates

```
production:{{with $secret := vault "secret/my-app/production" }}
  adapter: postgresql
  host: {{key "my-app/production/host"}}
  username: {{$secret.Data.username}}
  password: {{$secret.Data.password}}
{{end}}
```

???

- `secret/my-app/production` is a path to a secret
- typically use this with approle style authentication

---

# Hashicorp Vault Overview

## Leases

 - Secrets from Vault come with a lifetime- requires renewal based on a policy
 - Enforces check-ins (when configured)

## Ephemeral Secrets

Secrets that exist for a limited amount of time

 - Secrets dynamically generated on a per-token/application/user basis
 - Lease time allows for secrets/access to be retired
 - Provide secrets on a "need to use" basis

---

# Hashicorp Vault Overview

## Generated Secrets

Secret backends can be used to create secrets for applications

 - Give application "superuser" access to Vault and have Vault hand out
   credentials
 - Existing capabilities for Postgres, MySQL, AWS

---

# Hashicorp Vault Overview

## Generated Secrets

### Example: AWS IAM

> The AWS secret backend for Vault generates AWS access credentials dynamically
> based on IAM policies. This makes IAM much easier to use: credentials could
> be generated on the fly, and are automatically revoked when the Vault lease
> is expired.

Access to IAM role by reading a secret from the appropriate path:

```
$ vault read aws/creds/deploy
Key             Value
lease_id        aws/creds/deploy/7cb8df71-782f-3de1-79dd-251778e49f58
lease_duration  3600
access_key      AKIAIOMYUTSLGJOGLHTQ
secret_key      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
security_token  <nil>
```

---

# The Case for Hashicorp Vault

## Pros

 - comprehensive
 - solid, secure architecture
 - allows flexible "protection" of secrets
 - represents "next generation" capabilities

## Cons

 - many moving parts
 - can become a POF⁕
 - still need another turtle⁕

## ⁕ Footnotes

 - every secret management platform has these shortcomings

???

- flexible protection means we can be really paranoid or a bit looser
- more turtles required to:
  - give applications, users, or hosts authentication for the vault
  - perhaps have a different store for backing up long-term secrets such as
     root passwords, core IAM roles, postgres accounts, etc.

---
