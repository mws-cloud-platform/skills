# MWS Terraform Skill

Manage MWS Cloud Platform infrastructure with Terraform (provider `terraform-provider-mws`): configuration, import of existing resources, plan/apply/destroy.

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install) >= 1.11
- MWS provider network mirror in `~/.terraformrc` (see [MWS Terraform docs](https://mws.ru/docs/cloud-platform/terraform/general/whatis-terraform.html))
- [MWS CLI](https://mws.ru/docs/cloud-platform/mws-cli/general/mws-cli-install.html) — for listing resources and service account keys
