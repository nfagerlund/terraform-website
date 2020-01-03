---
layout: "cloud"
page_title: "Terraform State - Workspaces - Terraform Cloud"
---

# Terraform State in Terraform Cloud

-> **API:** See the [State Versions API](../api/state-versions.html).

Each workspace has its own separate state data. In order to read and write state for the correct workspace, Terraform Cloud overrides any configured [backend](/docs/backends/index.html) when running Terraform.

You can view current and historical state data for a workspace from its "States" tab. Each state in the list indicates which run and which VCS commit (if applicable) it was associated with. You can click a state in the list for more details, including a diff against the previous state and a link to the raw state file.

## Cross-Workspace State Access

In your Terraform configurations, you can use a [`terraform_remote_state` data source](/docs/providers/terraform/d/remote_state.html) to access [outputs](/docs/configuration/outputs.html) from your other workspaces.

~> **Important:** A given workspace can only access state data from within the same organization. If you plan to use multiple Terraform Cloud organizations, make sure to keep related groups of workspaces together in the same organization.

To configure a data source for a Terraform Cloud workspace, set the `backend` argument to `remote` and specify the organization and workspace in the `config` argument.

``` hcl
data "terraform_remote_state" "vpc" {
  backend = "remote"
  config = {
    organization = "example_corp"
    workspaces = {
      name = "vpc-prod"
    }
  }
}

resource "aws_instance" "redis_server" {
  # Terraform 0.12 syntax: use the "outputs.<OUTPUT NAME>" attribute
  subnet_id = data.terraform_remote_state.vpc.outputs.subnet_id

  # Terraform 0.11 syntax: use the "<OUTPUT NAME>" attribute
  subnet_id = "${data.terraform_remote_state.vpc.subnet_id}"
}
```

