---
layout: post
title: Journée à l"école de Martine
categories: [Histoires]
comments: true
---

[
## Full Automation

### Overview
So far in this blog series I've covered the basics of user licensing in Office 365 with PowerShell by demonstrating how to add and modify license skus. This is very useful in scripting the service entitlements for new users, but not all users are static and in many cases you'll need to manage licenses as users move between positions and licensing needs change. This isn't as easy as it sounds (or should be), and has a couple of obstacles.

### Problem 1 - DisabledPlans
The biggest drawback to configuring user licenses via PowerShell lies in the design of the New-MsolLicenseOptions cmdlet. The problem is that the -DisabledPlans parameter is inherently the wrong approach to license automation. For example, let's say we've set up a script that licenses users for the EnterprisePack and you've added Sharepoint to the disabled plans. In it's original state, this would have enabled Exchange, Skype for Business, and Yammer. However, last year Microsoft added a new service plan to the license - Sway. This means that as soon as Sway became available as an assignable license in your tenant, Sway would have been assigned to your users because it hasn't been explicitly added to the list of disabled plans.

So what we need to solve this problem is a method of setting enabled plans rather than disabled ones. That way no services will be provisioned for users unless you have explicitly added it in your script.

### Problem 2 - AddLicenses
When you license a user who is currently unlicensed you use the -AddLicenses parameter of Set-MsolUserLicense to provision the license for the first time. However, if a user is already licensed and you need to modify the provisioned service plans, you need to omit the -AddLicenses parameter. If you don't you'll receive an error - "The license is invalid." This isn't a very descriptive error, and you'll get the same error if you assign an appropriate license but with invalid options. This can be a big roadblock to an automated solution since we won't know how to react to the error appropriately.
