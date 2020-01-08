---
layout: "cloud"
page_title: "VCS Connections - Workspaces - Terraform Cloud"
---

# VCS Connections

[remote operations]: ../run/index.html
[remote backend]: /docs/backends/types/remote.html
[execution mode]: ./settings.html#execution-mode

Any Terraform Cloud workspace can be connected to a version control system (VCS) repository that contains its Terraform configuration. You can manage that connection in the "Version Control" page of each workspace's settings.

## About Configuration Versions

Each workspace is associated with a particular Terraform configuration. In order to perform [remote Terraform runs][remote operations] for a given workspace, Terraform Cloud needs access to its configuration. Since the configuration can change over time, Terraform Cloud also needs to get updated configuration versions when the code changes.

There are two ways to manage configuration versions for a workspace:

- **With a connected VCS repository.** Terraform Cloud can automatically fetch content from supported VCS providers, and uses webhooks to get notified of code changes. This is the most convenient way to use Terraform Cloud. See [The UI- and VCS-driven Run Workflow](../run/ui.html) for more information.

    -> **Note:** When a workspace is connected to a VCS repository, directly uploaded configuration versions can only be used for speculative plans.
- **With direct uploads.** With [the `remote` backend][remote backend] configured, Terraform CLI's `terraform plan` and `terraform apply` commands will perform remote runs by uploading a configuration from a local working directory. Alternately, you can use Terraform Cloud's API to upload configurations as `.tar.gz` files. The CLI and API aren't as convenient as a VCS connection (since Terraform Cloud can't automatically react to code changes), but they can integrate Terraform Cloud with an existing CI pipeline or an unsupported VCS provider. See [The CLI-driven Run Workflow](../run/cli.html) and [The API-driven Run Workflow](../run/api.html) for more information.

If a workspace's [execution mode is set to local][execution mode], it doesn't require configuration versions at all. Although it can still be connected to a VCS repository, the connection is not used unless the execution mode is changed to remote.

## Configuring VCS Connections

-> **API:** See the [Update a Workspace endpoint](../api/workspaces.html#update-a-workspace) (`PATCH /organizations/:organization_name/workspaces/:name`).

Workspaces are usually assigned a VCS connection [when they are created](./creating.html).

To change the VCS settings for an existing workspace, choose "Version Control" from the workspace's "Settings" menu. The version control page includes the following settings:

- Which VCS repository to use.
- Which directories in the repository should trigger Terraform runs when they change.
- Which branch of the repository to use.
- Whether to download the repository's submodules.

For most of the settings on this page, you must save any changes with the "Update VCS settings" button at the bottom of the page.

### VCS Connection and Repository

The first item in the version control settings lets you select a new VCS repository or disconnect from the current repository.

-> **Note:** Depending on the current status, this control can appear as a "Connect to version control" button or a "Change VCS connection" link.

Connecting or disconnecting a VCS repository occurs on a separate page, split into three screens:

1. On the first screen, choose your VCS provider (or choose "No VCS connection" to disconnect the workspace from version control).

    -> **Note:** If you haven't configured a VCS provider for your organization yet, choosing one here will prompt you to add that provider to your organization. See [Connecting VCS Providers](../vcs/index.html) for more information. Only organization owners can configure VCS providers.

2. On the second screen, choose a repository from a filterable list. This screen is skipped if you chose "No VCS connection".

    Some providers limit the size of this list. If the repository you want doesn't appear in the list, you can still connect a workspace to it; scroll to the bottom of the list, and enter the repository's ID in the text field.

    -> **Note:** For some VCS providers, the repository list includes a drop-down menu for changing which VCS account's repositories are shown. Other providers combine all available accounts into a single list.

3. On the third screen, confirm or cancel your choice. Confirmed changes will be saved immediately, and you will be returned to the "Version Control" settings page.

See also:

- [Creating Workspaces](./creating.html)
- [Connecting VCS Providers to Terraform Cloud](../vcs/index.html)

-> **API:** If you need to change VCS connections for many workspaces at once, consider automating the changes with the [Update a Workspace endpoint](../api/workspaces.html#update-a-workspace). This is most common when moving a VCS server, or when a vendor deprecates an older API version.

### Automatic Run Triggering

-> **Note:** This setting only affects workspaces that specify a Terraform working directory. [The working directory setting](./settings.html#terraform-working-directory) can be found on the "General" settings page.

For workspaces that **don't** specify a Terraform working directory, Terraform Cloud assumes that the entire repository is relevant to the workspace. Any change will trigger a run.

For workspaces that **do** specify a Terraform working directory, Terraform Cloud assumes that only _some_ content in the repository is relevant to the workspace. Only changes that affect the relevant content will trigger a run. By default, only the working directory is considered relevant.

You can adjust this behavior in two ways:

- **Add more trigger directories.** Terraform Cloud will queue runs for changes in any of the specified trigger directories (including the working directory).

    For example, if you use a top-level `modules` directory to share Terraform code across multiple configurations, changes to the shared modules are relevant to every workspace that uses that repo. You can add `modules` as a trigger directory for each workspace to make sure they notice any changes to shared code.
- **Mark the entire repository as relevant.** If you set the "Automatic Run Triggering" setting to "Always Trigger Runs," Terraform Cloud will assume that anything in the repository might affect the workspace's configuration, and will queue runs for any change.

    This can be useful for repos that don't have multiple configurations but require a working directory for some other reason. It's usually not what you want for true monorepos, since it queues unnecessary runs and slows down your ability to provision infrastructure.

-> **Note:** Trigger directories also apply to [speculative plans](./index.html#speculative-plans) on pull requests — Terraform Cloud won't queue plans for changes that aren't marked as relevant.

-> **Error Handling:** Terraform Cloud retrieves the changed files for each push or pull request using your VCS provider's API. If for some reason the list of changed files cannot be retrieved, or if it is too large to process, the default behaviour is to trigger runs on all attached workspaces. Should this happen, you may see several runs with state "Planned", due to the push resulting in no changes to infrastructure.

### VCS Branch

Which branch of the repository to use. If left blank, Terraform Cloud will use the repository's default branch.

### Include submodules on clone

Whether to recursively clone all of the repository's Git submodules when fetching a configuration.

-> **Note:** The [SSH key for cloning Git submodules](../vcs/index.html#ssh-keys) is set in the VCS provider settings for the organization, and is not related to the workspace's SSH key for Terraform modules.
