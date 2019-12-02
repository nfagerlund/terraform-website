---
layout: "cloud"
page_title: "Overview of Features - Terraform Cloud"
---

# Overview of Terraform Cloud Features

[cli]: /docs/cli-index.html
[workflow]: /guides/core-workflow.html
[state data]: TODO


At its core, Terraform Cloud is an execution platform that runs Terraform. Unlike a general-purpose execution platform, it has deep integrations with Terraform's workflows and data formats, and it uses those integrations to provide a large feature set of conveniences, insights, and new capabilities.

This page is a brief overview of Terraform Cloud's features and how they fit together.


- Core Terraform workflow affordances
    - remote execution (or not!)
    - workspaces (bundling all info for an inf set.)
    - CLI integration w/ remote backend and remote operations (you don't have to give up much by switching to cloud)
    - VCS integration (it's fundamental)
    - private module registry
    - cross-workspace remote state access
    - ...basically, this is all the stuff that you could already sort of tape together out of spare parts! except the remote backend stuff, that's secret sauce on our part.
- Governance and control
    - teams and permissions
        - plan/apply division
    - Sentinel policies
    - some access control is delegated to your VCS's merge permissions.
- Insight
    - state version history
    - notifications
    - pull request checks
    - API
    - Cost estimation


## Core Terraform Workflow Affordances

In its default state, [Terraform CLI][cli] uses a local workflow that is optimized for simplicity and easy adoption.

However, when working with a team of other Terraform users, it's important to use a remote, collaborative workflow so that you can share responsibilities, eliminate single points of failure, and ensure everyone understands what's happening.

Terraform Cloud offers a team-oriented remote Terraform workflow, designed to be comfortable for existing Terraform users and easily learnable for new users.

### Remote Terraform Execution

Terraform Cloud performs Terraform runs in its own cloud infrastructure, on disposable virtual machines.

This removes problems like inconsistent developer environments and local network congestion from the critical path for provisioning infrastructure, and lets us build advanced features that rely on the information Terraform generates during a run.

Remote execution can be disabled on a per-workspace basis. However, remote execution is a best practice for using Terraform as a team.

### Workspaces for Organizing Infrastructure

Terraform's local workflow manages each collection of infrastructure with a _persistent working directory._ The directory contains the configuration, state data, and variables Terraform needs, and Terraform uses that content whenever it runs inside that directory. By using separate directories, it's easy to separate unrelated (or loosely related) collections of infrastructure.

In Terraform Cloud, a _workspace_ contains everything you need to manage a collection of infrastructure, and Terraform uses that content whenever it executes in the context of that workspace.

Workspaces serve the exact same function as a persistent working directory, but they do so in a way that fits a remote workflow with many contributors.

Component | Local Terraform | Terraform Cloud
--|--|--
Terraform configuration | On disk | In linked version control repository\*
Variable values | On disk, as CLI arguments, or in shell environment | In workspace
State | On disk or in remote backend | In workspace; accessible with remote backend
Credentials and secrets | In shell environment or entered at prompts | In workspace, stored as sensitive variables

\* New configuration versions can also be uploaded by other tools.

-> **Note:** Terraform Cloud and Terraform CLI both have features called "workspaces," but they're slightly different. CLI workspaces are alternate state files in the same working directory; they're a convenience feature for managing separate groups of very similar resources. Terraform Cloud workspaces function like completely separate working directories.

### Version Control Integration

Infrastructure as code belongs in version control, so Terraform Cloud is designed to work directly with your version control system (VCS) provider.

Link a workspace to a VCS repository, and Terraform Cloud can automatically retrieve the Terraform configuration from that repository. Merge changes to that repository, and Terraform Cloud will notice, queueing a new Terraform run to update your infrastructure.



###


