---
layout: "cloud"
page_title: "GitHub.com (Simple) - VCS Providers - Terraform Cloud"
---

[private module registry]: TODO
[policy sets]: TODO

# Configuring GitHub.com Access (Simple)

This page describes Terraform Cloud's simplified integration with GitHub.com. This method requires very little configuration, and we recommend it for most new Terraform Cloud users.

There are separate instructions for connecting to [GitHub.com via OAuth](./github.html), connecting to [GitHub Enterprise](./github-enterprise.html), and connecting to [other supported VCS providers.](./index.html)

-> **Note:** This integration is designed for creating workspaces from the Terraform Cloud UI. It currently does not support the [private module registry][], [policy sets][], or creation of workspaces via the API or the `tfe` Terraform provider. If you plan to use these features immediately, configure [OAuth access for GitHub.com](./github.html) instead. If you plan to adopt features incrementally, feel free to use the simplified integration; you can configure OAuth later.

##