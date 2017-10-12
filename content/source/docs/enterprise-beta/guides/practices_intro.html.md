# Introduction:



Recent advances in cloud computing have led to major changes in the field of infrastructure provisioning. Now that physical datacenters aren't always mandatory, advanced IT organizations are moving away from a practice of up-front decision making and large hardware purchases, and toward a practice of nimble iteration on cloud infrastructure. These organizations can now choose infrastructure resources using accurate and timely information about their real requirements, which saves money and enables faster development.



HashiCorp specializes in helping IT organizations adopt cloud technologies. Based on what we've seen work well, we believe the best approach to provisioning is **collaborative infrastructure as code,** using Terraform as the core workflow and Terraform Enterprise to govern the boundaries between your organization's different teams, roles, applications, and deployment tiers.

However, not every organization can jump directly to practicing collaborative infrastructure as code. It's built on top of many other IT best practices (like using version control and preventing manual changes), and you'll need to adopt these foundations before you can practice a full collaborative infrastructure as code workflow. This document describes our recommended practices and gives clear steps to adopt them.





* Part 1 of this guide describes how infrastructure is organized and governed in Terraform Enterprise's collaborative infrastructure as code workflow.
* Part 2 is a series of questions to help you evaluate the state of your own infrastructure provisioning practices. We define four stages of operational maturity around provisioning to help you orient yourself and understand which foundational practices you still need to adopt.
* Part 3 is a generalized guide for how to advance your provisioning practices through the four stages of operational maturity. Not every part applies to every organization, so keep your answers from part 2 in mind as you read it.
