class: center, middle

# Secrets Management

---

 - What's a Secret
 - The Case for Managing Secrets
 - Key Capabilities Needed
 - Current Solutions
 - Available Solutions
 - Vault Overview
 - The Case for Vault
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



