---
layout: "cloud"
page_title: "Overview of Features - Terraform Cloud"
---

# Overview of Terraform Cloud Features

[cli]: /docs/cli-index.html
[workflow]: /guides/core-workflow.html
[state data]: TODO

Terraform uses a consistent core workflow for provisioning, modifying, and tearing down infrastructure:

- **Write** `.tf` configuration files that describe your desired infrastructure state.
- **Perform a plan** to preview the expected changes.
- **Perform an apply** to provision real infrastructure.

In its default state, [Terraform CLI][cli] uses a local workflow. Local Terraform is optimized for simplicity and a low barrier to entry, but it has some drawbacks when managing infrastructure as a team.

Terraform Cloud addresses all of those limitations with a remote, collaborative Terraform workflow. This page introduces the core features of Terraform Cloud, with a focus on how they change and extend the core Terraform workflow.

## Workspaces

In Terraform's local workflow, a _persistent working directory_ manages a collection of infrastructure. A working directory includes the configuration and inputs that manage its associated infrastructure, plus the state data that matches Terraform resources to real objects. Whenever Terraform runs inside a given working directory, it operates on the associated infrastructure.

In Terraform Cloud, a _workspace_ manages a collection of infrastructure. Like a working directory, a workspace contains everything Terraform needs to manage the associated infrastructure. But workspaces are designed to be used by a team of operators, so they store that data in a different form:

Component | Local Terraform | Terraform Cloud
--|--|--
Terraform configuration | On disk | In a linked version control repository\*
Variable values | On disk, as CLI arguments, or in environment | Stored directly in workspace; edit with UI or API
State | On disk or in remote backend; backups/history are operator's responsibility | In integrated backend; state history is saved automatically
Credentials and secrets | In shell environment or entered at prompts | Stored in workspace as sensitive variables
Terraform execution | On local machine; updates are operator's responsibility | In disposable virtual machine managed by Terraform Cloud; always uses workspace's configured Terraform version

\* New configuration versions can also be uploaded by other tools.

-> **Note:** Terraform Cloud and Terraform CLI both have features called "workspaces," but they're slightly different. CLI workspaces are alternate state files in the same working directory; they're a convenience feature for managing separate groups of very similar resources. Terraform Cloud workspaces act like completely separate working directories.

## Remote Plans and Applies

Terraform Cloud performs Terraform runs in its own remote infrastructure, using disposable virtual machines. Remote runs are always associated with a workspace, and use that workspace's variables and state. The progress of a remote run can be viewed in Terraform Cloud's UI; for runs

Remote runs provide major benefits to a team of Terraform users:

### Visibility and Insight

- Each workspace displays the progress of its current run in real time, so the whole team can understand what's happening.
- Workspaces preserve a record of their previous runs, including metadata about why and when each run was triggered.
- Terraform Cloud can send notifications for run events (configured per-workspace), with built-in support for Slack and a generic webhook format for integrating with other services.
- Enable cost estimation to preview the monthly cost of a configuration, as well as the change in cost versus the current state.

### Automation

- Terraform Cloud can trigger runs automatically in response to changes in version control; see [Version Control Integration][inpage-vcs] below for more details.
- Runs can also be triggered by an API. API-driven runs can use the current configuration from version control or can upload a new configuration.

### Security and Control

- Team members can perform Terraform runs without needing access to cloud credentials and other sensitive configuration. Sensitive variables in a workspace are available as normal variables during a Terraform run, but are never displayed to users.
- With the Team upgrade, give different permissions to different teams of users, making Terraform useful for your entire organization while still holding to the principle of least privilege.
- With the Governance upgrade, use Sentinel policies to limit what type of infrastructure Terraform provisions or require approval for disruptive changes.


## Version Control Integration

[inpage-vcs]: #version-control-integration

Version control is a best practice for infrastructure as code, so wherever possible, Terraform Cloud defers to your existing version control system (VCS) as a source of truth.

### Workspaces

Link a workspace to a VCS repository, and Terraform Cloud will pull configurations for that workspace directly from version control. VCS-linked workspaces also use webhooks to respond to changes in their repository:

- When new changes are merged to a workspace's branch, Terraform Cloud will queue a run in that workspace using the new configuration. By default, the resulting plan must be approved before it is applied; for fast-moving infrastructure in dev environments, you can configure the workspace to automatically apply.
- When pull requests are opened against a workspace's branch, Terraform Cloud will run a speculative plan and attach the result as a pull request check.

### Modules

Terraform Cloud's private module registry publishes Terraform modules from VCS repositories. Push a new tag to a shared module's repository, and the registry will publish a new version of it.

### Sentinel Policies

Terraform Cloud can pull sets of Sentinel policies from VCS repositories, and automatically keeps them up to date.











Like a local directory, a workspace includes a Terraform configuration and the state data that maps that configuration to real resources. But

- The Terraform configuration itself (in one or more `.tf` files).
- Values for the configuration's variables (in a `.tfvars` file).
- State data from the previous run (in a `.tfstate` file), which maps the resources in the configuration to the real infrastructure objects Terraform has created.


A workspace collects everything necessary to manage a collection of infrastructure, and makes it accessible to everyone who needs to work with that infrastructure.

- Cached plugins and modules (which improve performance but aren't strictly necessary).









Each workspace manages one collection of infrastructure. It usually has a link to the VCS repository that contains the Terraform configuration for that infrastructure, and it

collects everything Terraform needs to manage a given














CONCEPTS

- tf cloud is terraform, basically
- all terraform cli stuff still obtains

MAIN STUFF IT DOES

- workspaces
    - state, terraform runner, variables and data, run history, where infrastructure lives
- private module registry

HOW THAT CORRESPONDS TO THE TF CLI WORKFLOW

- still running terraform.
- you manage your .tfvars files and your environment variables centrally, and can mark some as sensitive. no more needing to pass things around in 1pass.
- state is centrally managed
    - state versions, too, for recovery or investigation
- runs are visible to whole team and can be passed off for approval
- extra run features (cost estimation, sentinel checks)
- "plan and see what happens" runs are codified as speculative plans, and run on pull requests on on demand
- workspaces are more strongly codified. they have more ~stuff~ in em, more independent.
- you can still do cli runs, but you have the option to do the actual action remotely. central point for accessing the network resources, central point for credentials and stuff, comfort of your own terminal. alternately: don't.


ACCESS CONTROL

- users/teams/orgs
- sentinel policies
- some access control delegated to your vcs.
- team tokens for automated stuff.

WORKFLOW

- vcs-backed workspaces, the merge-and-confirm workflow
- cli with remote runs
- cli without remote runs
- api for swapping in your own vcs or ci

API

- integrate whatever with whatever
- tfe provider


FEATURE SETS

- free
- team
- governance
