---
page_title: "TODO"
layout: "enterprise2"
sidebar_current: "recommended"
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




























[[a]](#cmnt_ref1)When organizing repos and workspaces there are a couple recommendations we have: (1) shouldn't use long-running branches,  (2) use a single branch but differentiate by variables, (3) or if appropriate use directories with different main.tf files for each env but sharing common modules.



Do you think those types of recommendations belongs in this document?





[[b]](#cmnt_ref2)My notes on this: https://docs.google.com/document/d/1NLUbS5m8QYIqBMp-nLkIWC0eKFHPY2MOg4-mUu-omLw/





[[c]](#cmnt_ref3)When organizing repos and workspaces there are a couple recommendations we have: (1) shouldn't use long-running branches,  (2) use a single branch but differentiate by variables, (3) or if appropriate use directories with different main.tf files for each env but sharing common modules.



Do you think those types of recommendations belongs in this document?





[[d]](#cmnt_ref4)My notes on this: https://docs.google.com/document/d/1NLUbS5m8QYIqBMp-nLkIWC0eKFHPY2MOg4-mUu-omLw/





[[e]](#cmnt_ref5)coming soon





[[f]](#cmnt_ref6)i think we should recommend sentinel here instead. maybe we remove for now then add later?

