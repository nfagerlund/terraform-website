---
layout: "cloud"
page_title: "GitHub.com (GitHub App) - VCS Providers - Terraform Cloud"
---

[private module registry]: TODO
[policy sets]: TODO

# Configuring GitHub.com Access (GitHub App)

If your VCS provider is GitHub.com, you can start connecting your repositories to Terraform Cloud workspaces

These instructions are for quickly using GitHub.com for Terraform Cloud workspaces, without needing to configure an OAuth app.

There are separate instructions for connecting to [GitHub.com via OAuth](./github.html), connecting to [GitHub Enterprise](./github-enterprise.html), and connecting to [other supported VCS providers.](./index.html)

-> **Note:** The GitHub App is designed for creating workspaces from the Terraform Cloud UI. It currently does not support the [private module registry][], [policy sets][], or creation of workspaces via the API or the `tfe` Terraform provider. If you plan to use these features immediately, configure [OAuth access for GitHub.com](./github.html) instead. If you plan to adopt features incrementally, start by using the GitHub App integration and configure OAuth later.

##

- Make sure you're an admin of the desired github account, whether it's personal or org.
- from create a new workspace page (or vcs settings page),
    - make sure the buttons are in the "add" state. (always if no vcs provider has been added. use the "add different provider" (?) link otherwise.)
    - click the github button, and select "GitHub.com" (!) from the menu.
- An app authorize window appears, w/ green button. Authorize the app.
- You'll end up transferred to second screen of the VCS setup, back in TFC.
- If that shows the org you wanted (in the dropdown), great! If not:
    - click the org-switcher dropdown in the workspace list, and choose "Add another organization" (!).
    - An "Install Terraform Cloud" window (on github.com) appears.
    - It has a list of the organizations you are a member of, plus your personal account.
    - Choose the account whose repositories you want to use in TFC.
    - A confirmation screen appears. You can choose which repositories to allow, or allow all repos. Confirm you want to install the app with the "Install" (!) button.
    - You'll be returned to the workspace list, this time with the desired account selected in the account switcher. that accounts repos will be shown in the list.
- you are done.



managing the app:

- go to your account's github settings.
    - This might be a personal account, or it might be an org account (in which case you probably need to be an admin.)
- navigate to "Applications" (!) in the sidebar.
- Go to the "Installed GitHub Apps" (!) tab. click the "Configure" (!) button by the "Terraform Cloud" app.
    - If you want to **change which repos** TFC can access, use the "Repository access" section of the settings.
    - If you want to **stop TFC from accessing the repos of this account,** use the "Uninstall" button.
- Go to the "Authorized GitHub Apps" (!) tab.
    - If you want to **stop the organizations you belong to from appearing in the workspace list account switcher,** use the "Revoke" button.
    - This is how you'd recover from being logged in as the wrong account and not having the right orgs available in the drop-down.