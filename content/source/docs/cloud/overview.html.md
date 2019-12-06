---
layout: "cloud"
page_title: "Overview of Features - Terraform Cloud"
---

# Overview of Terraform Cloud Features

[cli]: /docs/cli-index.html
[workflow]: /guides/core-workflow.html
[state data]: TODO
[supported VCS providers]: todo
[remote backend]: todo
[CLI-driven runs]: todo
[speculative plans]: todo
[remote_state]: /docs/providers/terraform/d/remote_state.html
[outputs]: todo
[modules]: todo
[cost estimation]: todo

At its core, Terraform Cloud is an execution platform that runs Terraform. Unlike a general-purpose execution platform, it has deep integrations with Terraform's workflows and data formats, and it uses those integrations to provide new capabilities, insights, and conveniences.

This page is a brief overview of Terraform Cloud's features and how they fit together.


- Core Terraform workflow affordances
    -x remote execution (or not!)
    -x workspaces (bundling all info for an inf set.)
    -x CLI integration w/ remote backend and remote operations (you don't have to give up much by switching to cloud)
    -x VCS integration (it's fundamental)
    -x private module registry
    -x cross-workspace remote state access
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


## Core Terraform Workflow Support

In its default state, [Terraform CLI][cli] uses a local workflow that is optimized for simplicity and easy adoption.

However, when working with a team of other Terraform users, it's important to use a remote, collaborative workflow so that you can share responsibilities, share awareness, and eliminate single points of failure.

Terraform Cloud offers a team-oriented remote Terraform workflow, designed to be comfortable for existing Terraform users and easily learnable for new users.

### Remote Terraform Execution

Terraform Cloud performs Terraform runs in its own cloud infrastructure, on disposable virtual machines. Documentation and UI text often refer to this as "remote operations."

Remote operations remove problems like inconsistent developer environments and local network congestion from the critical path for provisioning infrastructure, and enable advanced features that consume information Terraform generates during a run.

#### Support for Local Execution

Remote execution is a best practice for using Terraform as a team, but it can be disabled on specific workspaces for workloads with unusual requirements. With remote operations disabled, Terraform Cloud workspaces can still host remote state; configure the [remote backend][], and Terraform CLI will perform local runs using the configured workspace's remote state.

### Workspaces for Organizing Infrastructure

Terraform's local workflow manages each collection of infrastructure with a _persistent working directory._ Each directory contains the configuration, state data, and variables Terraform needs, and Terraform uses that content whenever it runs inside that directory. By using separate directories, it's easy to organize infrastructure resources into meaningful groups.

In Terraform Cloud, _workspaces_ fill this role. Each workspace contains everything necessary to manage a given collection of infrastructure, and Terraform uses that content whenever it executes in the context of that workspace.

Since workspaces are designed for teams and persistent directories are designed for individuals, they store their information differently:

Component | Local Terraform | Terraform Cloud
--|--|--
Terraform configuration | On disk | In linked version control repository, or uploaded via API/CLI
Variable values | On disk, as CLI arguments, or in shell environment | In workspace
State | On disk or in remote backend | In workspace; accessible with remote backend
Credentials and secrets | In shell environment or entered at prompts | In workspace, stored as sensitive variables

In particular, storing variables in the workspace offers major benefits. Sensitive variables allow your whole team to _use_ production cloud credentials without ever needing to _view_ them.

-> **Note:** Terraform Cloud and Terraform CLI both have features called "workspaces," but they're slightly different. CLI workspaces are alternate state files in the same working directory; they're a convenience feature for managing separate groups of very similar resources. Terraform Cloud workspaces function like completely separate working directories.

### Version Control Integration

Infrastructure as code belongs in version control, so Terraform Cloud is designed to work directly with your version control system (VCS) provider. (More info: [supported VCS providers][].)

Each workspace can be linked to a VCS repository that contains its Terraform configuration, optionally specifying a branch and subdirectory. Terraform Cloud automatically retrieves configuration content from the repository, and will also watch the repository for changes:

- When you merge new commits, any affected workspaces will automatically run Terraform plans using the new code.
- When a pull request is opened, Terraform Cloud will run speculative plans as pull request checks, so you can easily preview the infrastructure changes that will result from the proposed code changes.

VCS integration is powerful, but optional; if you use an unsupported VCS or want to preserve an existing code validation and deployment pipeline, you can use the API or Terraform CLI to upload new configuration versions. You'll still get the benefit of remote execution and Terraform Cloud's other features.

### Command Line Integration

When developing Terraform configurations, it's often helpful to run `terraform plan` to interactively preview changes and check your work.

Terraform Cloud enhances that workflow by letting you run remote plans from your local command line. Configure the [remote backend][] in your local configuration directory, and the `terraform plan` command will start a remote run in the configured Terraform Cloud workspace. The output of the run streams directly to your terminal, and you can also share a link to the remote run with your teammates.

Remote [CLI-driven runs][] use the current working directory's Terraform configuration and the remote workspace's variables. This usually offers the best of both worlds: you can preview local changes before submitting a PR, and you can iterate on production infrastructure without needing direct access to production cloud credentials or other sensitive information.

The remote backend also supports state manipulation commands like `terraform import` or `terraform taint`.

-> **Note:** The `terraform plan` command runs [speculative plans][], which don't modify real infrastructure. You can also use `terraform apply` to perform full remote runs, but only with workspaces that are _not_ connected to a VCS repository. This helps ensure that your VCS remains the source of record for all real infrastructure changes.

### Cross-Workspace Data Sharing

No infrastructure is an island, and groups of infrastructure resources that are mostly separate from each other often need to share a small amount of information.

Terraform Cloud makes it trivial to share information between workspaces with root-level [outputs][]. Any workspace that uses remote operations is automatically authenticated to use [`terraform_remote_state` data sources][remote_state].

### Private Module Registry

Even small organizations can get major efficiency benefits by codifying commonly used infrastructure patterns into reusable [modules][].

Terraform CLI has a built-in ability to fetch modules from VCS repos, but Terraform Cloud improves this with a per-organization private module registry. Users throughout your organization can browse a directory of internal modules, and can specify flexible version constraints for the modules they use in their configurations. Easy versioning lets downstream teams use modules with confidence, and frees upstream teams to iterate faster.

The private registry uses your VCS as the source of truth, relying on Git tags to manage module versions. Just specify which repositories contain modules, and the registry handles the rest.

## Insight

Beyond the core Terraform workflow, Terraform Cloud offers powerful tools for insight into your infrastructure provisioning process.

### Notifications

Terraform Cloud can send notifications about Terraform runs to other systems, including Slack and any other service that accepts webhooks. Notifications can be configured per-workspace.

### Cost Estimation

Before applying a run, Terraform Cloud can display an estimate of the total cost and change in cost for the affected infrastructure.

- More info: [Cost Estimation][]

### Code Review

For workspaces connected to a VCS repository, Terraform Cloud can run speculative plans on pull requests, adding a link to the results in the pull request interface.

### State and Run History

Each Terraform Cloud workspace retains historical state versions and a history of all Terraform runs.

For workspaces connected to a VCS repository, the run history includes information about the commit that triggered the run, so you can correlate infrastructure changes and code changes long after the fact.

### API

Nearly all of Terraform Cloud's features are available in its JSON-based API, making it easy to integrate any of its data with any other service.


## Governance and Control

For larger organizations, Terraform Cloud offers access control and policy features to


###