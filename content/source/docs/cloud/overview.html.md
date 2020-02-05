---
layout: "cloud"
page_title: "Overview of Features - Terraform Cloud"
---

# Overview of Terraform Cloud Features

[cli]: /docs/cli-index.html
[workflow]: /guides/core-workflow.html
[state data]: TODO
[supported VCS providers]: todo
[remote backend]: /docs/backends/types/remote.html
[CLI-driven runs]: todo
[speculative plans]: todo
[remote_state]: /docs/providers/terraform/d/remote_state.html
[outputs]: todo
[modules]: todo
[cost estimation]: todo

Fundamentally, Terraform Cloud is an execution platform that runs Terraform. Unlike a general-purpose execution platform, it has deep integrations with Terraform's workflows and data formats, and it uses those integrations to make Terraform more convenient and more powerful.

This page is a brief overview of Terraform Cloud's features and how they fit together.


## Core Terraform Workflow Support

Terraform Cloud runs [Terraform CLI][cli] to provision infrastructure.

In its default state, Terraform CLI uses a local workflow, performing operations on the workstation where it is invoked and storing state in a local directory.

But working with a team requires a remote workflow — at minimum, state must be stored in a shared backend, and ideally Terraform should execute in a shared environment instead of on a random workstation. This makes it possible to share responsibilities, share awareness, and avoid single points of failure.

Terraform Cloud offers a team-oriented remote Terraform workflow, designed to be comfortable for existing Terraform users and easily learnable for new users. The foundations of this workflow are remote Terraform execution, a workspace-based organizational model, version control integration, command-line integration, cross-workspace data sharing, and a private Terraform module registry.

### Remote Terraform Execution

Terraform Cloud runs Terraform on disposable virtual machines in its own cloud infrastructure. (Remote Terraform execution is sometimes referred to as "remote operations.")

The main benefits of remote execution are consistency and visibility for critical provisioning operations. Remote execution also enables powerful features like Sentinel policy enforcement, cost estimation, notifications, version control integration, and more.

#### Support for Local Execution

[execution_mode]: ./workspaces/settings.html#execution-mode

Remote execution can be disabled on specific workspaces with the ["Execution Mode" setting][execution_mode]. The workspace will still host remote state, and Terraform CLI can use that state for local runs via the [remote backend][].

### Workspaces for Organizing Infrastructure

Terraform's local workflow manages a collection of infrastructure with a _persistent working directory,_ which contains configuration, state data, and variables. Terraform uses a working directory's content whenever it runs inside that directory; by using separate directories, it's easy to organize infrastructure resources into meaningful groups.

Terraform Cloud fills this role with _workspaces_ instead of directories. Each workspace contains everything necessary to manage a given collection of infrastructure, and Terraform uses that content whenever it executes in the context of that workspace.

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

- When new commits are merged, affected workspaces automatically run Terraform plans with the new code.
- When pull requests are opened, affected workspaces run speculative plans with the proposed code changes and post the results as a pull request check; reviewers can see at a glance whether the plan was successful, and can click through to view the proposed changes in detail.

VCS integration is powerful, but optional; if you use an unsupported VCS or want to preserve an existing validation and deployment pipeline, you can use the API or Terraform CLI to upload new configuration versions. You'll still get the benefits of remote execution and Terraform Cloud's other features.

### Command Line Integration

Remote execution offers major benefits to a team, but local execution offers major benefits to individual developers; for example, most Terraform users run `terraform plan` to interactively check their work while editing configurations.

Terraform Cloud offers the best of both worlds, allowing you to run remote plans from your local command line. Configure the [remote backend][], and the `terraform plan` command will start a remote run in the configured Terraform Cloud workspace. The output of the run streams directly to your terminal, and you can also share a link to the remote run with your teammates.

Remote [CLI-driven runs][] use the current working directory's Terraform configuration and the remote workspace's variables, so you don't need to obtain production cloud credentials just to preview a configuration change.

The remote backend also supports state manipulation commands like `terraform import` or `terraform taint`.

-> **Note:** The `terraform plan` command runs [speculative plans][], which preview changes without modifying real infrastructure. You can also use `terraform apply` to perform full remote runs, but only with workspaces that are _not_ connected to a VCS repository. This helps ensure that your VCS remains the source of record for all real infrastructure changes.

### Cross-Workspace Data Sharing

No infrastructure is an island, and groups of infrastructure resources that are mostly separate from each other often need to share a small amount of information.

Terraform Cloud makes it trivial to share information between workspaces with root-level [outputs][]. Any workspace that uses remote operations can use [`terraform_remote_state` data sources][remote_state] to access other workspaces' outputs, without any additional configuration or authentication.

### Private Module Registry

Even small organizations can get major efficiency benefits by codifying commonly used infrastructure patterns into reusable [modules][].

Terraform CLI can already fetch modules from arbitrary VCS sources, but Terraform Cloud improves this with a private module registry. Users throughout your organization can browse a directory of internal modules, and can specify flexible version constraints for the modules they use in their configurations. Easy versioning lets downstream teams use modules with confidence, and frees upstream teams to iterate faster.

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