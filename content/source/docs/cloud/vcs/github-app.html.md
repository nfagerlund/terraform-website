---
layout: "cloud"
page_title: "GitHub.com (Simple) - VCS Providers - Terraform Cloud"
---

[private module registry]: TODO
[policy sets]: TODO

# Configuring GitHub.com Access (Simple)

This page describes Terraform Cloud's simplified integration with GitHub.com. We recommend this method for most new Terraform Cloud users.

There are separate instructions for connecting to [GitHub.com via OAuth](./github.html), connecting to [GitHub Enterprise](./github-enterprise.html), and connecting to [other supported VCS providers.](./index.html)

## Notes and Limitations

Simplified GitHub access is implemented with a GitHub App, rather than an OAuth application.

This integration currently has some limitations compared to the OAuth-based integration:

- It does not support the [private module registry][] or [policy sets][].
- It cannot be used to create workspaces via the API or the `tfe` Terraform provider.

However, the GitHub App can be used alongside an OAuth connection. Most new users should start with the GitHub App, then connect via OAuth later if necessary.

##