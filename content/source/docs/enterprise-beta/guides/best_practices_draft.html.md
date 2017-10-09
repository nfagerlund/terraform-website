---
page_title: "Temp title"
layout: "enterprise2"
sidebar_current: "none"
---

Table of contents:

[Goal](#h.qtb5yevsfaya)        [4](#h.qtb5yevsfaya)

[Background and Challenges](#h.z03bldqkkyv1)        [4](#h.z03bldqkkyv1)

[Personas, Responsibilities, and Desired User Experiences](#h.hlh88cy46ng5)        [5](#h.hlh88cy46ng5)

[Central IT](#h.cmt3wszii75z)        [5](#h.cmt3wszii75z)

[Organization Architect](#h.6n4l4dgywizj)        [6](#h.6n4l4dgywizj)

[Workspace Owner](#h.4cbgg0sgciaa)        [6](#h.4cbgg0sgciaa)

[Workspace Contributor](#h.1te8h4kh3bz9)        [6](#h.1te8h4kh3bz9)

[Defining Terraform Structure at the Organizational level](#h.dmgsgikjj0n2)        [6](#h.dmgsgikjj0n2)

[Terraform Workflow Across Workspaces](#h.sbcxzlsrspfa)        [7](#h.sbcxzlsrspfa)

[Variable and Policy Management](#h.i0ysxrpr74g6)        [8](#h.i0ysxrpr74g6)

[Organization](#h.av39mhs0p9h4)        [8](#h.av39mhs0p9h4)

[Organization Variable Set](#h.rk1ehopcltem)        [8](#h.rk1ehopcltem)

[Workspace](#h.qfbehkdltf2k)        [8](#h.qfbehkdltf2k)

[2.1 Questions for Understanding Current State](#h.u6pma8j3r1ow)        [10](#h.u6pma8j3r1ow)

[2.1.1 Understanding Current Infrastructure Configuration](#h.hemb1e4sg2er)        [10](#h.hemb1e4sg2er)

[2.1.2 Understanding the Change Workflow](#h.z4pk2f9jolsg)        [13](#h.z4pk2f9jolsg)

[2.1.3 Understanding the Current Operating Model:](#h.gw0lwf28wvdw)        [15](#h.gw0lwf28wvdw)

[2.1.4 Understanding the Current Security Model:](#h.u84ai8txtp5x)        [21](#h.u84ai8txtp5x)

[2.1.5 Assessing Your Maturity:](#h.u8veiqjqnmui)        [23](#h.u8veiqjqnmui)

[Introduction](#h.py4hrvo5butw)        [24](#h.py4hrvo5butw)

[3.1 Steps to Maturity](#h.53rcprdtor8k)        [24](#h.53rcprdtor8k)

[3.1.1 Manual Changes](#h.ixnhaen789dv)        [24](#h.ixnhaen789dv)

[3.1.2 Moving on from Manual Changes](#h.3pwqkdv1uele)        [25](#h.3pwqkdv1uele)

[2.Semi-automated](#h.t6vbg7vgsabk)        [26](#h.t6vbg7vgsabk)

[Infrastructure through UI/CLI and Infrastructure as Code](#h.22ankyo6ol4f)        [26](#h.22ankyo6ol4f)

[Infrastructure scripts or configuration management](#h.9v55u768h0jw)        [26](#h.9v55u768h0jw)

[3.Infrastructure as Code](#h.k233l4wnt7aw)        [29](#h.k233l4wnt7aw)

[4.Collaborative Infrastructure as Code](#h.nv68lj13c31j)        [31](#h.nv68lj13c31j)

[Progression from Terraform Open Source to Terraform Enterprise](#h.t3eylsaa4r2h)        [31](#h.t3eylsaa4r2h)

[Advanced TFE usage](#h.qqsls14tse9j)        [35](#h.qqsls14tse9j)

[Summary](#h.8cey8x7yfwz0)        [37](#h.8cey8x7yfwz0)

[Document Definitions:](#h.pzxig87uyjv0)        [38](#h.pzxig87uyjv0)

[Appendix A: Identifying automated and manual processes in your IT organization.](#h.dj1yhi2fvjyk)        [39](#h.dj1yhi2fvjyk)





Introduction:



Recent advances in cloud computing have led to major changes in the field of infrastructure provisioning. Now that physical datacenters aren’t always mandatory, advanced IT organizations are moving away from a practice of up-front decision making and large hardware purchases, and toward a practice of nimble iteration on cloud infrastructure. These organizations can now choose infrastructure resources using accurate and timely information about their real requirements, which saves money and enables faster development.



HashiCorp specializes in helping IT organizations adopt cloud technologies. Based on what we’ve seen work well, we believe the best approach to provisioning is collaborative infrastructure as code,using Terraform as the core workflow and Terraform Enterprise to govern the boundaries between your organization’s different teams, roles, applications, and deployment tiers.

However, not every organization can jump directly to practicing collaborative infrastructure as code. It’sbuilt on top of many other IT best practices (like using version control and preventing manual changes), and you’ll need to adopt these foundations before you can practice a full collaborative infrastructure as code workflow. This document describes our recommended practicesand gives clear steps to adopt them.





*   Part 1 of this guide describes how infrastructure is organized and governed in Terraform Enterprise’s collaborative infrastructure as code workflow.
*   Part 2 is a series of questions to help you evaluate the state of your own infrastructure provisioning practices. We define four stages of operational maturity around provisioning to help you orient yourself and understand which foundational practices you still need to adopt.
*   Part 3 is a generalized guide for how to advance your provisioning practices through the four stages of operational maturity. Not every part applies to every organization, so keep your answers from part 2 in mind as you read it.





Part 1: Best Practices for Provisioning with Terraform Enterprise



Terraform’s purpose is to provide one workflow to provision any infrastructure. In this section, we’ll show you our recommended practices for organizing Terraform usage across a large organization. This is the set of practices that we call “collaborative infrastructure as code.”



# Fundamental Challenges in Provisioning

There are two major challenges everyone faces when trying to improve their provisioning practices: technical complexity and organizational complexity.



1.  Technical complexity — Different infrastructure providers use different interfaces to provision new resources, and the inconsistency between these interfaces imposes extra costs on daily operations. These costs get worse as you add more infrastructure providers and more collaborators.



Terraform addresses this complexity by separating the provisioning workload. It uses a single core engineto read infrastructure as code configurations and determine the relationships between resources, then uses many [provider plugins](https://www.google.com/url?q=https://www.terraform.io/docs/providers/index.html&sa=D&ust=1507528980098000&usg=AFQjCNGCNZaBEgZ16thd_IsrXzvAZ5TtlQ) to create, modify, and destroy resources on the infrastructure providers. These provider plugins can talk to IaaS (e.g. AWS, GCP, Microsoft Azure, OpenStack), PaaS (e.g. Heroku), or SaaS services (e.g. GitHub, DNSimple, Cloudflare).



In other words, Terraform uses a model of workflow-level abstraction, rather than resource-level abstraction. It lets you use a single workflow for managing  infrastructure, but acknowledges the uniqueness of each provider instead of imposing generic concepts on non-equivalent resources.



2.  Organizational complexity — As infrastructure scales, it requires more teams to maintain it. For effective collaboration, it’s important to delegate ownership of infrastructure across these teams and empower them to work in parallel without conflict. Terraform and Terraform Enterprise can help delegate infrastructure in the same way components of a large application are delegated.



To delegate a large application, companies often split it into small, focused microservice components that are owned by specific teams. Each microservice provides an API, and as long as those APIs don’t change, microservice teams can make changes in parallel despite relying on each others’ functionality.



Similarly, infrastructure code can be split into smaller Terraform codebases, which have limited scope and are owned by specific teams. These independent Terraform codebases use [output variables](https://www.google.com/url?q=https://www.terraform.io/docs/configuration/outputs.html&sa=D&ust=1507528980099000&usg=AFQjCNF9S9gh2ckVt7QyR7PJIMF8brIrSw)to publish information and [remote state resources](https://www.google.com/url?q=https://www.terraform.io/docs/providers/terraform/d/remote_state.html&sa=D&ust=1507528980099000&usg=AFQjCNEUMth96lcEZ4A5umMs166j1jY7Wg)to access output data from other workspaces. Just like microservices communicate and connect via APIs, Terraform workspaces connect via remote state.



Once you have loosely-coupled Terraform codebases, you can delegate their development and maintenance to different teams. To do this effectively, you need to control access to these codebases. Version control systems can regulatewho can commit code, but since Terraform affects real infrastructure, you also need to regulate who can run the code.



This is how Terraform Enterprise (TFE) solves the organizational complexity of provisioning: by providing a centralized run environment for Terraform that supports and enforces your organization’s access control decisions across all workspaces. This helps you delegate infrastructure ownership to enable parallel development.

# Personas, Responsibilities, and Desired User Experiences

There are four main personas for managing infrastructure at scale. These roles have different responsibilities and needs, and Terraform Enterprise supports them with different tools and permissions.



## Central IT

This team is responsible for defining common infrastructure practices, enforcing policy across teams, and maintaining shared services.



Central IT users want a single dashboard to view the status and compliance of all infrastructure, so they can quickly fix misconfigurations or malfunctions. Since Terraform Enterprise is tightly integrated with Terraform’s run data and is designed around Terraform’s concepts of workspaces and runs, it offers a more integrated workflow experience than a general-purpose CI system.



## Organization Architect

This team defines how global infrastructure is divided and delegated to the teams within the business unit. This team also enables connectivity between workspaces by defining the APIs each workspace must expose, and sets organization-wide variables and policies.



Organization Architects want a single dashboard to view the status of all workspaces and the graph of connectivity between them.



## Workspace Owner

This individual owns aspecific set of workspaces, which build a given Terraform codebase across several environments. They are responsible for the health of those workspaces, managing the full change lifecycle through dev, UAT, staging, and production. They are the main approver of changes to production within their domain.



Workspace Owners want:

*   A single dashboard to view the status of all workspaces that use their infrastructure codebase.
*   A streamlined way to promote changes between environments.
*   An interface to set variables used by a codebase across environments.

##

## Workspace Contributor

Contributors submit changes to workspaces by making updates to the infrastructure as code configuration. They usually do not have approval to make changes to production, but can make changes in dev, UAT, and staging.



Workspace Contributors want a simple workflow to submit changes to a workspace and promote changes between workspaces. They can edit a subset of workspace variables and their own personal variables.



Workspace contributors are often already familiar with Terraform’s command line interface. TFE supports them by extending the Terraform CLI — for example, the `terraform push` command executes a run in TFE’s environment instead of locally, using TFE’s auditing and access control features.



# The Optimal Terraform Workspace Structure

## About Workspaces

Terraform Enterprise’s main unit of operation is a workspace. A workspace is a collection of everything Terraform needs to run: a codebase (usually from a VCS repo), values for that code’s variables, and state data to keep track of operations between runs.



In Terraform open source, a workspace is just a working directory. In TFE, they’re persistent shared resources; you can assign them their own access controls, monitor their run states, and more.



## One Workspace Per Environment Per Codebase

Workspaces are TFE’s primary tool for delegating control, which means their structure should matchyour organizational permissions structure. The best approach is to use one workspace for each environment of a given infrastructure component. Or in other words, codebases * environments = workspaces.



This is different from how some other tools view environments; notably, you shouldn’t use a single Terraform workspace to manage everything that makes up your production or staging environment. Instead, make smaller workspaces that are easy to delegate. This also means not every codebase has to use the exact same environments; if a UAT environment doesn’t make sense for your security infrastructure, you aren’t forced to use one.



Name your workspaces with both their component and their environment. For example, if you have a Terraform codebase for managing an internal billing app and another codebase for your networking infrastructure, you could name the workspaces as follows:

*   billing-app-dev
*   billing-app-stage
*   billing-app-prod
*   networking-dev
*   networking-stage
*   networking-prod

## Delegating Workspaces

Since each workspace is one environment of one infrastructure component, you can use per-workspace access controls to delegate ownership of components and regulate code promotion across environments. For example:



*   Teams that help manage a component can start Terraform runs and edit variables in dev or staging.
*   The owners or senior contributors of a component can start Terraform runs in production, after reviewing other contributors’ work.
*   Central IT and organization architects can administer permissions on all workspaces, to ensure everyone has what they need to work.
*   Teams that have no role managing a given component don’t have access to its workspaces.



To use TFE effectively, you must make sure the division of workspaces and permissions matches your organization’s division of responsibilities. If it’s difficult to separate your workspaces effectively, it might reveal an area of your infrastructure where responsibility is muddled and unclear. If so, this is a chance to disentangle the code and enforce better boundaries of ownership.



# Promoting Changes Between Related Workspaces (Coming Soon)

In a future version, TFE will let you create promotion pipelines across workspaces.



As we described above, each workspace is a specific environment of a given codebase. Today, you must handle code promotion manually, by switching the code used in a higher environment to match the code that successfully passed the prior environment.



But soon, you’ll be able to set up promotion relationships, so instead of checking out code directly from version control, a higher environment can accept code directly from a prior environment. This can help provide a guarantee that high environments are only run with known good code.

# Variable and Policy Management

Terraform Enterprise has multiple places to set variables, which override each other in hierarchical order. For all of these levels, TFE securely stores variables in Vault.



## Organization

Any workspace can use default variables from the organization level. For example, the organization might specify a default billing tag to use on all resources.



This is the lowest level of the hierarchy; all subsequent levels can override it.



## Workspace

Per-workspace variables. Most variables are stored at the workspace level; this is the best place for things like ami_ids, machine counts, SSL certificates, and more.



User

Variables attached to a specific user, which are used in all workspaces and which override workspace variables. For example, each user can be associated with their ARN.





2.0 Evaluating Your Current Provisioning Practices

Terraform Enterprise depends on several foundational IT practices. Before you can implement Terraform Enterprise’s collaborative infrastructure as code workflows, you need to understand which of those practices you’re already using, and which ones you still need to implement.



We’ve written the section below in the form of a quiz or interview, with multiple-choice answers that represent the range of operational maturity levels we’ve seen across many organizations. You should read it with a notepad handy, and take note of any questions where your organization can improve its use of automation and collaboration.



This quiz doesn’t have a passing or failing score, but it’s important to know your organization’s answers. Once you know which of your IT practices need the most attention, Section 3 will guide you from your current state to our recommended practices in the most direct way.



Each question has several answers, each of which aligns with a different level of operational maturity. Those levels are as follows:



1.  Manual

*   Infrastructure is provisioned through a UI or CLI.
*   Configuration changes do not leave a traceable history, and aren’t always visible.
*   Limited or no naming standards in place.

2.  Semi-automated

*   Infrastructure is provisioned through a combination of UI/CLI, infrastructure as code, and scripts or configuration management.
*   Traceability is limited, since different record-keeping methods are used across the organization.
*   Rollbacks are hard to achieve due to differing record-keeping methods.



3.  Infrastructure as code

*   Infrastructure is provisioned using Terraform OSS.
*   Provisioning and deployment processes are automated.
*   Infrastructure configuration is consistent, with all necessary details fully documented (nothing siloed in a sysadmin’s head).
*   Source files are stored in version control to record editing history, and, if necessary, roll back to older versions.
*   Some Terraform code is split out into modules, to promote consistent reuse of your organization’s more common architectural patterns.

4.  Collaborative infrastructure as code

*   Users across the organization can safely provision infrastructure with Terraform, without conflicts and with clear understanding of their access permissions.
*   Expert users within an organization can produce standardized infrastructure templates, and beginner users can consume those to follow infrastructure best practices for the organization.
*   Per-workspace access control helps committers and approvers on workspaces protect production environments.
*   Functional groups that don’t directly write Terraform code have visibility into infrastructure status and changes through Terraform Enterprise’s UI.



By the end of this section, you should have a clear understanding of which operational maturity stage you are in. Section 3 will explain the recommended steps to move from your current stage to the next one.

# 2.1 Questions for Understanding Current Practices



Answering these questions will help you understand your organization’s method for provisioning infrastructure, its change workflow, its operation model, and its security model.



Once you understand your current practices, you can identify the remaining steps for implementing Terraform Enterprise.



## 2.1.1 Your Current Configuration and Provisioning Practices

How does your organization configure and provision infrastructure today? Automated and consistent practices help make your infrastructure more knowable and reliable, and reduce the amount of time spent on troubleshooting.



The following questions will help you evaluate your current level of automation for configuration and provisioning.



Q1\. How do you currently manage your infrastructure?



1.  Through a UI or CLI. This might seem like the easiest option for one-off tasks, but for recurring operations it is a big consumer of valuable engineering time.It’s also difficult to track and manage changes.
2.  Through reusable command line scripts, or a combination of UI and infrastructure as code. This is faster and more reliable than pure ad-hoc management and makes recurring operations repeatable, but the lack of consistency and versioning makes it difficult to manage over time.
3.  Through an infrastructure as code tool (Terraform, CloudFormation). Infrastructure as code enables scalable, repeatable, and versioned infrastructure. It dramatically increases the productivity of each operator and can enforceconsistency across environments when used appropriately.
4.  Througha general-purpose automation framework (i.e. Jenkins + scripts / Jenkins + Terraform). This centralizes the management workflow, albeit with a tool that isn’t built specifically for provisioning tasks.



Q2\. What topology is in place for your service provider accounts?



1.  Flat structure, single account. All infrastructure is provisioned within the same account.
2.  Flat structure, multiple accounts. Infrastructure is provisioned using different infrastructure providers, with an account per environment.
3.  Tree hierarchy. This features a master billing account, an audit/security/logging account, and project/environment-specific infrastructure accounts.



Q3\. How do you manage the infrastructure for different environments?



1.  Manual. Everything is manual, with no configuration management in place.
2.  Siloed. Each application team has its own way of managing infrastructure — some manually, some using infrastructure as code or custom scripts.



3.  Infrastructure as code with different code bases per environment.Having different code bases for infrastructure as code configurations can lead to untracked changes from one environment to the other if there is no promotion within environments.
4.  Infrastructure as code with a single code base and differingenvironment variables. All resources, regardless of environment, are provisioned with the same code, ensuring that changes promote through your deployment tiers in a predictable way.



Q4\. How do teams collaborate and share infrastructure configuration and code?

1.  N/A. Infrastructure as code is not used.
2.  Locally. Infrastructure configuration is hosted locally and shared via email, documents or spreadsheets.
3.  Ticketing system. Code is shared through journal entries in change requests or problem/incident tickets.
4.  Centralized without version control.Code is stored on a shared filesystem and secured through security groups. Changes are not versioned. Rollbacks are only possible through restores from backups or snapshots.
5.  Configuration stored and collaborated in a version control system (VCS) (Git repositories, etc.). Teams collaborate on infrastructure configurations within a VCS workflow, and can review infrastructure changes before they enter production. This is the most mature approach, as it offers the best record-keeping and cross-department/cross-team visibility.



Q5\. Do you use reusable modules for writing infrastructure as code?

1.  Everything is manual. No infrastructure as code currently used.



2.  No modularity.Infrastructure as code is used, but primarily as one-off configurations. Users usually don’t share or re-use code.
3.  Teams use modules internally but do not share them across teams.



4.  Modules are shared organization-wide. Similar to shared software libraries, a module for a common infrastructure pattern can be updated once and the entire organization benefits.



## 2.1.2 Your Current Change Control Workflow



Change control is a formal process to coordinate and approve changes to a product or system. The goals of a change control process include:



*   Minimizing disruption to services.
*   Reducing rollbacks.
*   Reducing the overall cost of changes.
*   Preventing unnecessary changes.
*   Allowing users to make changes without impacting changes made by other users.

The following questions will help you assess the maturity of your change control workflow.



Q7\. How do you govern the access to control changes to infrastructure?

1.  Access is not restricted or audited. Everyone in the platform team has the flexibility to create, change, and destroy all infrastructure. This leads to a complex system that is unstable and hard to manage.



2.  Access is not restricted, only audited. This makes it easier to track changes after the fact, but doesn’t proactively protect your infrastructure’s stability.
3.  Access is restricted based on service provider account level. members of the team have admin access to different accounts based on the environment they are responsible for.
4.  Access is restricted based on user roles. all access is restricted based on user roles at infrastructure provider level.



Q8\. What is the process for changing existing infrastructure?

1.  Manual changes by remotely logging into machines. Repetitive manual tasks are inefficient and prone to human errors.



2.  Runtime configuration management (Puppet, Chef, etc.). Configuration management tools let you make fast, automated changes based on a readable and auditable codebase. However, since they don’t produce static artifacts, the outcome of a given configuration version isn’t always 100% repeatable, making rollbacks only partially reliable.



3.  Immutable infrastructure (images, containers). Immutable components can be replaced for every deployment (rather than being updated in-place), using static deployment artifacts. If you maintain sharp boundaries between ephemeral layers and state-storing layers, immutable infrastructure can be much easier to test, validate, and roll back.



Q9\. How do you deploy applications?

1.  Manually (SSH, WinRM, rsync, robocopy, etc.). Repetitive manual tasks are inefficient and prone to human errors.



2.  With scripts (Fabric, Capistrano, custom, etc.).



3.  With a configuration management tool (Chef, Puppet, Ansible, Salt, etc.), or by passing userdata scripts to CloudFormation Templates or Terraform configuration files.



4.  With a scheduler (Kubernetes, Nomad, Mesos, Swarm, ECS, etc.).





## 2.1.4 Your Current Security Model.



Q21\. How are infrastructure service provider credentials managed?



1.  By hardcoding them in the source code. This is highly insecure.
2.  By using infrastructure provider roles (like EC2 instance roles for AWS).Since the service provider knows the identity of the machines it’s providing, you can grant some machines permission to make API requests without giving them a copy of your actual credentials.
3.  By using a secrets management solution (like Vault, Keywhis, or PAR).We recommend this.
4.  By using short-lived tokens. This is one of the most secure methods, since the temporary credentials you distribute expire quickly and are very difficult to exploit. However, this can be more complex to use than a secrets management solution.



Q22\. How do you control users and objects hosted by your infrastructure provider (like logins, access and role control, etc.)?



1.  A common ‘admin’ or ‘superuser’ account shared by engineers.This increases the possibility of a breach into your infrastructure provider account.
2.  Individual named user accounts. This makes a loss of credentials less likely and easier to recover from, but it doesn’t scale very well as the team grows.
3.  LDAP and/or Active Directory integration.This is much more secure than shared accounts, but requires additional architectural considerations to ensure that the provider’s access into your corporate network is configured correctly.
4.  Single sign-on through OAuth or SAML. This provides token-based access into your infrastructure provider while not requiring your provider to have access to your corporate network.



Q23. How do you track the changes made by different users in your infrastructure provider’s environments?

1.  No logging in place. Auditing and troubleshooting can be very difficult without a record of who made which changes when.



2.  Manual changelog. Users manually write down their changes to infrastructure in a shared document. This method is prone to human error.
3.  By logging all API calls to an audit trail service or log management service (like CloudTrail, Loggly, or Splunk). We recommend this. This ensures that an audit trail is available during troubleshooting and/or security reviews.



Q24\. How is the access of former employees revoked?



1.  Immediately, manually.If you don’t use infrastructure as code, the easiest and quickest way is by removing access for that employee manually using the infrastructure provider’s console.
2.  Delayed, as part of the next release. if your release process is extremely coupled and most of your security changes have to pass through a CAB (Change Advisory Board) meeting in order to be executed in production, this could be delayed.
3.  Immediately, writing a hot-fix in the infrastructure as code. this is the most secure and recommended option. Before the employee leaves the building, access must be removed.



## 2.1.5 Assessing the Maturity of Your Practices



After reviewing all of these questions, look back at your notes and assess your organization’s overall stage of maturity: are your practices mostly manual, semi-automated, infrastructure as code, or collaborative infrastructure as code?



Keep your current state in mind as you read the next section.



3.0 Transitioning From Current State to Desired End State

# Introduction

This section describes the steps necessary to move an organization from manual provisioning processes to a collaborative infrastructure as code workflow.



In this section we will go through each stage of maturity and list the steps necessary for an organization to move on to the next stage of maturity.

## 3.<a id="id.saqnbb96kgf4"></a>1 Moving from Manual Changes to Semi-Automated

Building infrastructure manually (with CLI or GUI tools) results in infrastructure that is hard to audit, hard to reproduce, hard to scale, and hard to share knowledge about.



If your current provisioning practices are largely manual, your first goal is to begin using open source Terraform in a small, manageable subset of your infrastructure. Once you’ve gotten some small success using Terraform, you’ll have reached the semi-automated stage of provisioning maturity, and can begin to scale up and expand your Terraform usage.



Allow one individual (or a small group) in your engineering team to get familiar with Terraform by following these steps:



1.  [Install](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/install.html&sa=D&ust=1507528980128000&usg=AFQjCNF-c-01P-Io7ROIxM64R2M4FUkESg) [Terraform](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/install.html&sa=D&ust=1507528980128000&usg=AFQjCNF-c-01P-Io7ROIxM64R2M4FUkESg) [OSS](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/install.html&sa=D&ust=1507528980128000&usg=AFQjCNF-c-01P-Io7ROIxM64R2M4FUkESg).



2.  Write your first [Terraform Configuration file](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/build.html&sa=D&ust=1507528980129000&usg=AFQjCNGnI2vGFV_TVA-Vm14f2VEOk3lwkw).



3.  Follow the rest of the [Terraform getting started guide](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/change.html&sa=D&ust=1507528980129000&usg=AFQjCNE2ynQV7KCBbrNumw1a2Cbp5dlQzA). These pages will walk you through [changing](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/change.html&sa=D&ust=1507528980130000&usg=AFQjCNH-j0zVBEDZ5Ml6M6mtDvXPnAe3yw)and [destroying](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/destroy.html&sa=D&ust=1507528980130000&usg=AFQjCNGHIQwya9B1Rr9w3bB9w6hhlYETww)resources, working with [resource dependencies](https://www.google.com/url?q=https://www.terraform.io/intro/getting-started/dependencies.html&sa=D&ust=1507528980130000&usg=AFQjCNGnCVlo80lfmZaC_8Vx3Zrz7b92Hw), and more.



4.  Choose a small real-life project and implement it with Terraform. Look at your organization’s list of upcoming projects, and designate one to be a Terraform proof-of-concept. Alternately, you can choose some existing infrastructure to re-implement with Terraform.



The key is to choose a project with limited scope and clear boundaries, such as provisioning infrastructure for a new application on AWS. This helps keep your team from getting overwhelmed with features and possibilities. You can also look at some [example projects](https://www.google.com/url?q=https://github.com/hashicorp/terraform/tree/master/examples/&sa=D&ust=1507528980131000&usg=AFQjCNHRCdE4cSgMlbjDAOjHluKwsHghUw)to get a feel for your options. (The [AWS two-tier example](https://www.google.com/url?q=https://github.com/terraform-providers/terraform-provider-aws/tree/master/examples/two-tier&sa=D&ust=1507528980131000&usg=AFQjCNH_YawZPmaCjYCJqq5LtRbm6DLdxQ) is often a good start.)



Your goal here is to build a small but reliable core of expertise with Terraform, and demonstrate its benefits to others in the organization.





At this point, you’ve reached a semi-automated stage of provisioning practices — one or more people in the organization can write Terraform code to provision and modify resources, and a small but meaningful subset of your infrastructure is being managed as code. This is a good time to provide a small demo to the rest of team to show how easy it is to write and provision infrastructure with Terraform.



## 3.<a id="id.b4b12ab33u1v"></a><a id="id.i6wcevcv7sr"></a>2 Moving from Semi-Automated to Infrastructure as Code



We define semi-automated provisioning as a mix of at least two of the following practices:



*   Infrastructure as code with Terraform.
*   Manual CLI or GUI processes.
*   Scripts.



If that describes your current provisioning practices, your next goal is to expand your use of Terraform, reduce your use of manual processes and imperative scripts, and make sure you’ve adopted the foundational practices that make infrastructure as code more consistent and useful.



Note: If you aren’t already using infrastructure as code for some portion of your infrastructure, make sure you follow the steps in the previous section first.



5.  Choose and implement a version control system (VCS) if your organization doesn’t already use a VCS.



You might be able to get by with a minimalist Git/Mercurial/SVN server, but we recommend adopting a more robust collaborative VCS application that supports code reviews/approvals and has APIs for accessing data and administering repositories and accounts. Bitbucket, GitLab, and GitHub are popular tools in this space.



If you already have established VCS workflows, layouts, and access control practices, great! If not, this is a good time to make these decisions. (We consider [this advice](https://www.google.com/url?q=https://www.drupalwatchdog.com/volume-4/issue-2/version-control-workflow-strategies&sa=D&ust=1507528980134000&usg=AFQjCNHPo3G4NZ8FHa12leuWHPOlMpG5pA) to be a good starting point.) Make sure you have a plan for who is allowed to merge changes and under what circumstances — since this code will be managing your whole infrastructure, it’s important to maintain its integrity and quality.



Also, make sure to write down your organization’s expectations and socialize them widely among your teams.



6.  Create a repo that Terraform Enterprise will be able to access.Currently, Terraform Enterprise supports integrations with GitHub, GitLab and Atlassian Bitbucket (both Server and Cloud).



7.  Start moving infrastructure code into version control. New Terraform code should all be going into version control; if you have existing Terraform code that’s outside version control, start moving it in so that everyone in your organization knows where to look for things and can track the history and purpose of changes.



8.  Create your first module. [Terraform](https://www.google.com/url?q=https://www.terraform.io/docs/modules/usage.html&sa=D&ust=1507528980135000&usg=AFQjCNHmp5GkcoqVG5DqGmPWnNnPwu84Gg) [modules](https://www.google.com/url?q=https://www.terraform.io/docs/modules/usage.html&sa=D&ust=1507528980135000&usg=AFQjCNHmp5GkcoqVG5DqGmPWnNnPwu84Gg)are reusable configuration units. They let you manage pieces of infrastructure as a single package you can call and define multiple times in the main configuration for a workspace. Examples of a good Terraform module candidate would be an auto-scaling group on AWS that wraps a launch configuration, auto-scaling group, and EC2 Elastic Load Balancer (ELB).. If you are already using Terraform modules, make sure you’re following the best practices and keep an eye on places where your modules could improve.



The diagram below can help you decide when to write a module:

 ![Screen Shot 2017-06-19 at 10.22.11.png](images/image2.png)



9.  Spread Terraform skills to additional teams, and improve the skills of existing infrastructure teams. In addition to internal training and self-directed learning, you might want to consider:

*   Sign your teams up for [official Hashicorp Training](https://www.google.com/url?q=https://www.hashicorp.com/training/&sa=D&ust=1507528980136000&usg=AFQjCNHw-q_EOV7iwEhAGb0c6gjAVDGFBA) .

*   Make available resources such as [Terraform Up and Running: Writing Infrastructure as Code](https://www.google.com/url?q=https://www.amazon.co.uk/Terraform-Running-Writing-Infrastructure-Code-ebook/dp/B06XKHGJHP/ref%3Dsr_1_1?ie%3DUTF8%26qid%3D1496138592%26sr%3D8-1%26keywords%3Dterraform%2Bup%2Band%2Brunning&sa=D&ust=1507528980137000&usg=AFQjCNEM6dRWbz8768Thn319yUll3BLE0A)or [Getting Started with Terraform](https://www.google.com/url?q=https://www.amazon.co.uk/Getting-Started-Terraform-Kirill-Shirinkin/dp/1786465108/ref%3Dsr_1_1?ie%3DUTF8%26qid%3D1496138892%26sr%3D8-1%26keywords%3DGetting%2BStarted%2Bwith%2BTerraform&sa=D&ust=1507528980137000&usg=AFQjCNED3qgZ7AikRL5P9Txj6q6KWn26Pg). These are especially valuable when nobody in your organization has used Terraform before.



10.  Create standard build architectures to use as guidelines for writing Terraform code. Modules work best when they’re shared across an organization, and sharing is more effective if everyone has similar expectations around how to design infrastructure.



Your IT architects should design some standardized build architectures specific to your organizational needs, to encourage building with high availability, elasticity and disaster recovery in mind, and to support consistency across teams.

Here are a few examples of good build patterns, from several cloud providers:



*   AWS: [Well Architected Frameworks](https://www.google.com/url?q=https://d0.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf&sa=D&ust=1507528980138000&usg=AFQjCNH10XXThh7rWj2l2gZ0y1EoqRmWtg)and the [Architecture Center](https://www.google.com/url?q=https://aws.amazon.com/architecture/&sa=D&ust=1507528980139000&usg=AFQjCNGuPwmUcwBz4sW4FZPlVQ1Lq8rRlw).
*   Azure: [deploying Azure Reference Architectures](https://www.google.com/url?q=https://github.com/mspnp/reference-architectures&sa=D&ust=1507528980139000&usg=AFQjCNFqx12vhFJ8_FTpk2OqLjx2ee6bhA)and [Azure Architecture Center](https://www.google.com/url?q=https://docs.microsoft.com/en-us/azure/architecture/&sa=D&ust=1507528980139000&usg=AFQjCNHZNN7Iy6vVv7DMdX_AeRksLXLgMg).
*   GCP: [Building scalable and resilient web applications.](https://www.google.com/url?q=https://cloud.google.com/solutions/scalable-and-resilient-apps&sa=D&ust=1507528980140000&usg=AFQjCNGYk7his0h2_xhIltfnHPg3C3DdNw)
*   Oracle Public Cloud: [Best Practices for Using Oracle Cloud.](https://www.google.com/url?q=https://docs.oracle.com/cloud/latest/stcomputecs/STCSG/GUID-C37FDFF1-7C48-4DA8-B31F-D7D7B35674A8.htm%23STCSG-GUID-C37FDFF1-7C48-4DA8-B31F-D7D7B35674A8&sa=D&ust=1507528980140000&usg=AFQjCNE4JDUa-jcGumqUadDa4OUKmkSMsg)



11.  Integrate with configuration management tools for user data and applications. If your organization already has a configuration management tool, then it’s time to integrate it with Terraform — you can use [Terraform’s provisioners](https://www.google.com/url?q=https://www.terraform.io/docs/provisioners/index.html&sa=D&ust=1507528980141000&usg=AFQjCNF0FUeAni5yeANvYSbDnCj6xUPVuA) to pass control to configuration management after a resource is created.



If you don’t use configuration management, and you aren’t using strictly immutable infrastructure, you should consider adopting a config management tool. This might be a large task, but it supports the same goals that drove you to infrastructure as code, by making application configuration more controllable, understandable, and repeatable across teams.



If you’re just getting started, try thistutorial on how to [create a Chef cookbook](https://www.google.com/url?q=https://www.vagrantup.com/docs/provisioning/chef_solo.html&sa=D&ust=1507528980141000&usg=AFQjCNEvfANPmNlsnlulYGDHKPOVhnNQjg)and test it locally with Vagrant. We also recommend this article about how to decide what [configuration management tool](https://www.google.com/url?q=http://www.intigua.com/blog/puppet-vs.-chef-vs.-ansible-vs.-saltstack&sa=D&ust=1507528980142000&usg=AFQjCNHGZp_8XOeQ-GHBN4TmM1kXrSUDYw) is best suited for your organization.



12.  Integrate with [Vault](https://www.google.com/url?q=https://www.terraform.io/docs/providers/vault/index.html&sa=D&ust=1507528980142000&usg=AFQjCNEfmV_BAGhebIAkxeaYcOl3PWxzBw) or another secret management tool. Secrets like service provider credentials must stay secret, but they also must be easy to use when needed. The best way to address those needs is to use a dedicated secret management tool. We believe HashiCorp’s Vault is the best choice for most people, but Terraform can integrate with other secret management tools as well.



At this point, your organization has a VCS configured, is managing key infrastructure with Terraform, and has at least one reusable Terraform module. Compared to a semi-automated practice, your organization has much better visibility into infrastructure configuration, using a consistent language and workflow.

<a id="id.qqh6tt37zo31"></a>

## 3.3 Moving from Infrastructure as Code to Collaborative Infrastructure as Code



Using version-controlled Terraform configurations to manage key infrastructure eliminates a great deal of technical complexity and inconsistency. Now that you have the basics under control, you’re ready to focus on other problems.



Your next goals are to:



*   Adopt consistent workflows for Terraform usage across teams
*   Expand the benefits of Terraform beyond the core of engineers who directly edit Terraform code.
*   Manage infrastructure provisioning permissions for users and teams.



Terraform Enterprise (TFE) is the tool we’ve built to help you address these next-level problems. The following section describes how to start using it most effectively.



Note: If you aren’t already using mature Terraform code to manage a significant portion of your infrastructure, make sure you follow the steps in the previous section first.



13.  Install TFE. You have two options for installing Terraform Enterprise: SaaS or private install. If you have chosen the SaaS version then you can skip this step; otherwise visit the [installation guide](https://www.google.com/url?q=https://github.com/hashicorp/terraform-enterprise-modules/blob/master/INSTALLING.md&sa=D&ust=1507528980144000&usg=AFQjCNGP93ZKcArCFOxsZjW-j4ldz-ZYeQ)to get started. Terraform will display the URL to your TFE servers as an output.
14.  Get familiarwith Terraform runs in TFE. With Terraform OSS, you generally use external VCS tools to get code onto the filesystem, then execute runs from the command line or from a general purpose CI system.



TFE does things differently: a workspace is associated directly with a VCS repo, and you use TFE’s UI or API to start and monitor runs. To get familiar with this operating model:



*   Read the documentation on how to [perform](https://www.google.com/url?q=https://www.terraform.io/docs/enterprise/runs/starting.html&sa=D&ust=1507528980145000&usg=AFQjCNH32ggZ0nlPgH89iY0m21YSIDgA9A) and [configure](https://www.google.com/url?q=https://www.terraform.io/docs/enterprise/runs/variables-and-configuration.html&sa=D&ust=1507528980146000&usg=AFQjCNENtaZsj2BCrG05t-VnxcCfYw9XFA) Terraform runs in Terraform Enterprise.
*   Create a proof-of-concept workspace, associate it with a Terraform codebase in a VCS repo, set variables as needed, and use Terraform Enterprise to perform some Terraform runs with that codebase.

15.  Design your organization’s workspace structure<sup>[[a]](#cmnt1)</sup><sup>[[b]](#cmnt2)</sup>. <sup>[[c]](#cmnt3)</sup><sup>[[d]](#cmnt4)</sup>In TFE, each environmentof a given component should be a separate workspace — in other words, components * environments = workspaces. A workspace name should be something like “networking-dev,” so you can tell at a glance which component and environment it manages.



The definition of a “component” depends on your organization’s structure. A given workspace might manage an application, a service, or a group of related services; it might provision infrastructure used by a single engineering team, or it might provision shared, foundational infrastructure used by the entire business.



You should structure your workspaces to match the divisions of responsibility in your infrastructure. You will probably end up with a mixture: some components, like networking, are foundational infrastructure controlled by central IT staff; others are application-specific and should be controlled by the engineering teams that rely on them.



Also, keep in mind:

*   Some workspaces publish output data to be used by other workspaces.
*   The workspaces that make up a component’s environments (app1-dev, app1-stage, app1-prod) should be run in order, to ensure code is properly verified.



The first relationship, a relationship between workspaces fordifferent components but the same environment, createsa graph of dependencies between workspaces, and you should stay aware of it. The second relationship, a relationship between workspaces forthe same component but different environments, creates a pipeline between workspaces. TFE doesn’t currently have the ability to act on these dependencies, but features like cascading updates and promotion are coming soon, and you’ll be able to use them more easily if you already understand how your workspaces relate.



16.  Create workspaces in TFE, and map VCS repositories to them. Each workspace reads its Terraform code from your version control system. You’ll need to assign a repository and branch to each workspace.



We recommend using the same repository and branch for every environment of a given app or service — write your Terraform code such that you can differentiate the environments via variables, and set those variables appropriately per workspace. This might not be practical for your existing code yet, in which case you can use different branches per workspace and handle promotion through your merge strategy, but we believe a model of one canonical branch works best.



![](images/image1.png)





17.  Coming soon: specify custom workspace attributes. In an upcoming version of TFE, the organization owner will be able to create custom attributes, which can be given per-workspace values. Attributes will be fields like `Environment`, `Service`, `Business Unit`, `Region`, or `Datacenter`, and will help you filter and organize workspaces in a way that works better for large organizations. In today’s TFE, an `Environment` attribute is available on all workspaces, but custom attributes are not supported.



18.  Create or import users and teams. If your organization keeps user and team data in a directory server, use TFE’s SAML<sup>[[e]](#cmnt5)</sup> integration to import it. If you don’t, your colleagues must create their own TFE user accounts and ask to join your organization, and you can then add them to the appropriate teams.



TFE’s teams are lists of users that can be granted per-workspace permissions, which means your TFE teams need to match your understanding of who has which responsibilities for what infrastructure. That might not be an exact match for your org chart, so you might need to add some extra information to your employee directory in order to map TFE teams effectively. Keep in mind:



*   Some teams will need to administer many workspaces, and others only need permissions on one or two.
*   A team might not have the same permissions on every workspace they use; for example, application developers might have read/write access to their app’s dev and stage environments, but read-only access to prod.



Managing an accurate and complete map of how responsibilities are delegated is one of the most difficult parts of practicing collaborative infrastructure as code.

19.  Assignworkspace ownership and permissions to teams. Each workspace has three levels of permissions you can grant to any user or team: admin, read/write, and read-only. Admins effectively own the workspace, and can change the permissions of other users on it.



Most workspaces will give access to multiple teams with different permissions.





<a id="t.17eb7579ee7bb8bfdeee25b8776f2879dc76133e"></a><a id="t.0"></a>

<table class="c45">

<tbody>

<tr class="c33">

<td class="c21" colspan="1" rowspan="1">

Workspace

</td>

<td class="c21" colspan="1" rowspan="1">

Team Permissions

</td>

</tr>

<tr class="c33">

<td class="c21" colspan="1" rowspan="1">

app1-dev

</td>

<td class="c21" colspan="1" rowspan="1">

Team-eng-app1: Read/write

Team-owners-app1: Admin

Team-central-IT: Admin



</td>

</tr>

<tr class="c33">

<td class="c21" colspan="1" rowspan="1">

app1-prod

</td>

<td class="c21" colspan="1" rowspan="1">

Team-eng-app1: Read-only

Team-owners-app1: Read/write

Team-central-IT: Admin



</td>

</tr>

<tr class="c33">

<td class="c21" colspan="1" rowspan="1">

networking-dev

</td>

<td class="c21" colspan="1" rowspan="1">

Team-eng-networking: Read/write

Team-owners-networking: Admin

Team-central-IT: Admin

</td>

</tr>

<tr class="c33">

<td class="c21" colspan="1" rowspan="1">

networking-prod

</td>

<td class="c21" colspan="1" rowspan="1">

Team-eng-networking: Read-only

Team-owners-networking: Read/write

Team-central-IT: Admin

</td>

</tr>

</tbody>

</table>









20.  Restrictaccess to cloud provider UIs and APIs. Since Terraform Enterprise is now your organization’s primary interface for infrastructure provisioning, you should restrict access to any alternate interface that bypasses TFE. For almost all users, it should be impossible to manually modify infrastructure without using the organization’s agreed-upon Terraform workflow.



As long as no one can bypass Terraform, your code review processes and your TFE workspace permissions are the definitive record of who can modify which infrastructure. This makes everything about your infrastructure more knowable and controllable. Terraform Enterprise is one workflow to learn, one workflow to secure, and one workflow to audit for provisioning any infrastructure in your organization.



At this point, you have successfully adopted a collaborative infrastructure as code workflow with Terraform Enterprise. You can provision infrastructure across multiple providers using a single workflow, and you have a shared interface that helps manage your organization’s standards around access control and code promotion.

## 3.4 Advanced Terraform Enterprise usage



Now that you have a collaborative interface and workflow for provisioning, you have a solid framework for improving your practices even further.



The following suggestions don’t have to be done in order, and some of them might not make sense for every business. We present them as possibilities for when you find yourself asking what’s next.



21.  Move more processes and resources into TFE. Even after successfully implementing TFE, there’s a good chance you still have manual or semi-automated workflows and processes. We suggest holding a discovery meeting with all of the teams responsible for keeping infrastructure running, to identify future targets for automation. You can also use your notes from the questions in section 2 as a guide, or go through old change requests or incident tickets.



22.  Adopt Packer for image creation. HashiCorp’s Packer helps you build golden images in a maintainable and repeatable way, and can amplify Terraform’s usefulness.



23.  Adopt integration and unit testing for infrastructure.Some tools you might want to consider include [serverspec](https://www.google.com/url?q=https://github.com/mizzy/serverspec&sa=D&ust=1507528980157000&usg=AFQjCNFxF30e5d1TosHVZZE7ZNxIHYQ8fw), [Goss](https://www.google.com/url?q=https://github.com/aelsabbahy/goss&sa=D&ust=1507528980157000&usg=AFQjCNGhz0291weW3kTkQ1MdplYRTgGA7A), [InSpec](https://www.google.com/url?q=https://github.com/chef/inspec&sa=D&ust=1507528980157000&usg=AFQjCNHPcC9i7MgF7u0E8itDBET46-gbDg), and [rspec-terraform](https://www.google.com/url?q=https://github.com/bsnape/rspec-terraform/blob/master/README.md&sa=D&ust=1507528980157000&usg=AFQjCNFUj4wdAH0Ft3ogyfpc1Uf2Jx_Fsg).<sup>[[f]](#cmnt6)</sup>



24.  Set up audit logs for TFE. Terraform Enterprise private installs send logs to CloudWatch by default.



25.  Add infrastructure monitoring and performance metrics. This can help make environment promotion safer, and safeguard the performance of your applications. There are many tools available in this space, and we recommend monitoring both the infrastructure itself, and the user’s-eye-view performance of your applications.



26.  Use the TFE API.The [TFE API](https://www.google.com/url?q=https://www.terraform.io/docs/enterprise/api/index.html&sa=D&ust=1507528980159000&usg=AFQjCNGpBIuazfLz_E5sk0cw-C2Z5IW5Og) can be used to integrate withgeneral-purpose CI/CD tools to trigger Terraform runs in response to a variety of events.















[[a]](#cmnt_ref1)When organizing repos and workspaces there are a couple recommendations we have: (1) shouldn't use long-running branches,  (2) use a single branch but differentiate by variables, (3) or if appropriate use directories with different main.tf files for each env but sharing common modules.



Do you think those types of recommendations belongs in this document?





[[b]](#cmnt_ref2)My notes on this: https://docs.google.com/document/d/1NLUbS5m8QYIqBMp-nLkIWC0eKFHPY2MOg4-mUu-omLw/





[[c]](#cmnt_ref3)When organizing repos and workspaces there are a couple recommendations we have: (1) shouldn't use long-running branches,  (2) use a single branch but differentiate by variables, (3) or if appropriate use directories with different main.tf files for each env but sharing common modules.



Do you think those types of recommendations belongs in this document?





[[d]](#cmnt_ref4)My notes on this: https://docs.google.com/document/d/1NLUbS5m8QYIqBMp-nLkIWC0eKFHPY2MOg4-mUu-omLw/





[[e]](#cmnt_ref5)coming soon





[[f]](#cmnt_ref6)i think we should recommend sentinel here instead. maybe we remove for now then add later?

