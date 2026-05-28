---
title: Deploy Langfuse on AWS with Terraform
description: Step-by-step guide to run Langfuse on AWS via Terraform.
label: "Version: v3"
sidebarTitle: "AWS (Terraform)"
---

# Deploy Langfuse on AWS with Terraform

This guide will walk you through the steps to deploy Langfuse on AWS using the official Terraform module ([langfuse/langfuse-terraform-aws](https://github.com/langfuse/langfuse-terraform-aws)).
You will need access to an AWS account and the Terraform CLI installed on your local machine.

By default, the Terraform module will provision the necessary infrastructure for the Langfuse application containers and data stores ([architecture overview](/self-hosting#architecture)).
You can optionally configure the module to use existing AWS resources. See the Readme for more details.

Alternatively, you can deploy Langfuse on Kubernetes using the [Helm chart](/self-hosting/deployment/kubernetes-helm).

  If you are interested in contributing to our Terraform deployment guides or
  modules, please create an issue on the [GitHub
  Repository](https://github.com/langfuse/langfuse-terraform-aws).

## Readme

Source: [langfuse/langfuse-terraform-aws](https://github.com/langfuse/langfuse-terraform-aws)

## Support

If you experience any issues when self-hosting Langfuse, please:

1. Check out [Troubleshooting & FAQ](/self-hosting/troubleshooting-and-faq) page.
2. Use [Ask AI](/ask-ai) to get instant answers to your questions.
3. Ask the maintainers on [GitHub Discussions](/gh-support).
4. Create a bug report or feature request on [GitHub](/issues).

