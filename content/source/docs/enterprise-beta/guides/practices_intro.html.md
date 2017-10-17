---
page_title: "TODO"
layout: "enterprise2"
sidebar_current: "recommended"
---

# Recommended Provisioning Practices

## Introduction

Recent advances in cloud computing have led to major changes in the field of infrastructure provisioning. Now that physical datacenters aren't always mandatory, advanced IT organizations are moving away from a practice of up-front decision making and large hardware purchases, and toward a practice of nimble iteration on cloud infrastructure. These organizations can now choose infrastructure resources using accurate and timely information about their real requirements, which saves money and enables faster development.

HashiCorp specializes in helping IT organizations adopt cloud technologies. Based on what we've seen work well, we believe the best approach to provisioning is **collaborative infrastructure as code,** using Terraform as the core workflow and Terraform Enterprise to manage the boundaries between your organization's different teams, roles, applications, and deployment tiers.

The collaborative infrastructure as code workflow is built on many other IT best practices (like using version control and preventing manual changes), and you must adopt these foundations before you can fully adopt our recommended workflow. Achieving state-of-the-art provisioning practices is a journey, with several distinct stops along the way.

This guide describes our recommended provisioning practices and explains how to adopt them. It covers the steps to start using our tools, with special attention to the foundational practices they rely on.

* Part 1 of this guide is a holistic overview of Terraform Enterprise's collaborative infrastructure as code workflow. It describes how infrastructure is organized and governed, and how people interact with it.
* Part 2 is a series of questions to help you evaluate the state of your own infrastructure provisioning practices. We define four stages of operational maturity around provisioning to help you orient yourself and understand which foundational practices you still need to adopt.
* Part 3 is a guide for how to advance your provisioning practices through the four stages of operational maturity. Many organizations are already partway through this process, so use what you learned in part 2 to determine where you are in this journey.

TODO links
