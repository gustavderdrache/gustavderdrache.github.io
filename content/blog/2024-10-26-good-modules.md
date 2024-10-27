---
title: Good Terraform modules
description: Arguing for narrow-scoped, composable modules
---

It's no secret that I dislike [certain Terraform modules](/blog/modules). But rather than continuing to rant about various other modules, I thought it'd be instructive to think about good module design and how to create a useful ecosystem. That "useful" part is what I want to focus on&mdash;because I think a lot of Terraform modules expose way too many options, simply acting as multi-resource facades, rather than actually saving users time and work.

Imagine a few foundational modules that covered the kinds of scenarios an organization often encounters. For example, a basic VPC module that follows organizational practices and only exposes the knobs and levers a user needs to think about, such as the name and optionally a private IPv4 CIDR range. The module's authors have ensured that it meets requirements, such as creating flow logs, route tables, and maybe even network ACLs. For sensitive environments, another module could be created where PrivateLink endpoints are all created and network access is locked down.

Fundamentally, this is the shift I want to think about: modules should be a useful package to at least partially answer "what are you creating?" The answer is not "a VPC", it's "a public-facing marketing site" or "a log aggregation service."

When this shift happens, it opens up a few things: you can create, for example, a module to deploy an ECS clusted based on the usual standards. Say your organization prefers to deploy applications on Fargate, or has a specific AMI required for EC2-based capacity providers. The module can just handle that. There can be more modules if such things are required, but again, they should be compositional units: your proposal for a workload in your organization can turn into grabbing those modules off the shelf: this module uses a non-sensitive network, deploys to ECS Fargate, and is fronted by a public-facing load balancer. There's three modules right there, and each can require inputs that meet standards. If you require a WAF ACL for every load balancer, either hand out a standard definition or give users guidelines to follow, and pass their own WAF ACL into the load balancer module.

A good module aids users in following guidelines, and a good module portfolio turns this into rapid delivery. As modules are updated to pick up enhancements and bug fixes, every downstream consumer benefits. For example, when the majority of your load balancers are managed by a module, you can raise the security baseline by simply updating your module's TLS security policy. Don't make that kind of thing configurable: users shouldn't have to guess or consult standards. Don't make them think any more than they have to.
