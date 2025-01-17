# Google IAM Terraform Module

This Terraform module makes it easier to non-destructively manage multiple IAM roles for resources on Google Cloud Platform.

The module is a collection of submodules. We recommend using the specific resource submodules directly:
* [Folders IAM](modules/folders_iam)
* [KMS Crypto Keys IAM](modules/kms_crypto_keys_iam)
* [KMS_Key Rings IAM](modules/kms_key_rings_iam)
* [Organizations IAM](modules/organizations_iam)
* [Projects IAM](modules/projects_iam)
* [Pubsub Subscriptions IAM](modules/pubsub_subscriptions_iam)
* [Pubsub Topics IAM](modules/pubsub_topics_iam)
* [Service Accounts IAM](modules/service_accounts_iam)
* [Storage Buckets IAM](modules/storage_buckets_iam)
* [Subnets IAM](modules/subnets_iam)

## Compatibility

This module is meant for use with Terraform 0.12. If you haven't
[upgraded][terraform-0.12-upgrade] and need a Terraform 0.11.x-compatible
version of this module, the last released version intended for Terraform 0.11.x
is [1.1.1][v1.1.1].

## Upgrading

The following guides are available to assist with upgrades:

- [3.0 -> 4.0](./docs/upgrading_to_iam_4.0.md)
- [2.0 -> 3.0](./docs/upgrading_to_iam_3.0.md)

## Usage

Full examples are in the [examples](./examples/) folder, but basic usage is as follows for managing roles on two projects:

```hcl
module "projects_iam_bindings" {
  source  = "terraform-google-modules/iam/google//modules/projects_iam"
  version = "~> 4.0"

  projects = ["project-123456", "project-9876543"]

  bindings = {
    "roles/storage.admin" = [
      "group:test_sa_group@lnescidev.com",
      "user:someone@google.com",
    ]

    "roles/compute.networkAdmin" = [
      "group:test_sa_group@lnescidev.com",
      "user:someone@google.com",
    ]

    "roles/compute.imageUser" = [
      "user:someone@google.com",
    ]
  }
}
```

The module also offers an **authoritative** mode which will remove all roles not assigned through Terraform. This is an example of using the authoritative mode to manage access to a storage bucket:

```hcl
module "storage_buckets_iam_bindings" {
  source  = "terraform-google-modules/iam/google//modules/storage_buckets_iam"
  version = "~> 4.0"

  storage_buckets = ["my-storage-bucket"]

  mode = "authoritative"

  bindings = {
    "roles/storage.legacyBucketReader" = [
      "user:josemanuelt@google.com",
      "group:test_sa_group@lnescidev.com",
    ]

    "roles/storage.legacyBucketWriter" = [
      "user:josemanuelt@google.com",
      "group:test_sa_group@lnescidev.com",
    ]
  }
}
```

### Variables

Following variables are the most important to control module's behavior:

- Mode

    This variable controls the module's behavior, by default is set to "additive", possible options are:

      - additive: add members to role, old members are not deleted from this role.
      - authoritative: set the role's members, other roles' members are not deleted.

- Bindings

  Is a map of role (key) and list of members (value) with member type prefix, for example:

```hcl
        bindings = {
            "roles/<some_role>" = [
                "user:someone@somewhere.com",
                "group:somepeople@somewhereelse.com"
            ]
        }
```

- Project

  This variable must be defined in case of using one the following modules:

  - `pubsub_subscriptions_iam`
  - `pubsub_topics_iam`
  - `service_accounts_iam`
  - `subnets_iam`

- Subnets_region

  This variable must be defined in case of using module `subnets_iam`

#### Additive and Authoritative Modes

This module includes two modes: additive and authoritative.

In authoritative mode, the module takes full control over the IAM bindings listed in the module. This means that any members added to roles outside the module will be removed the next time Terraform runs. However, roles not listed in the module will be unaffected.

In additive mode, this module leaves existing bindings unaffected. Instead, any members listed in the module will be added to the existing set of IAM bindings. However, members listed in the module *are* fully controlled by the module. This means that if you add a binding via the module and later remove it, the module will correctly handle removing the role binding.

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| folders | Folders list to add the IAM policies/bindings | list(string) | `<list>` | no |
| folders\_bindings | Map of role (key) and list of members (value) to add the Folders IAM policies/bindings | map(list(string)) | n/a | yes |
| folders\_mode | Mode for adding the Folders IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| kms\_crypto\_keys | KMS Crypto Keys list to add the IAM policies/bindings | list(string) | `<list>` | no |
| kms\_crypto\_keys\_bindings | Map of role (key) and list of members (value) to add the KMS Crypto Keys IAM policies/bindings | map(list(string)) | n/a | yes |
| kms\_crypto\_keys\_mode | Mode for adding the KMS Crypto Keys IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| kms\_key\_rings | KMS Key Rings list to add the IAM policies/bindings | list(string) | `<list>` | no |
| kms\_key\_rings\_bindings | Map of role (key) and list of members (value) to add the KMS Key Rings IAM policies/bindings | map(list(string)) | n/a | yes |
| kms\_key\_rings\_mode | Mode for adding the KMS Key Rings IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| organizations | Organizations list to add the IAM policies/bindings | list(string) | `<list>` | no |
| organizations\_bindings | Map of role (key) and list of members (value) to add the Organizations IAM policies/bindings | map(list(string)) | n/a | yes |
| organizations\_mode | Mode for adding the Organizations IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| project | Project to add the IAM policies/bindings | string | `""` | no |
| projects | Projects list to add the IAM policies/bindings | list | `<list>` | no |
| projects\_bindings | Map of role (key) and list of members (value) to add the Projects IAM policies/bindings | map(list(string)) | n/a | yes |
| projects\_mode | Mode for adding the Projects IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| pubsub\_subscriptions | PubSub Subscriptions list to add the IAM policies/bindings | list(string) | `<list>` | no |
| pubsub\_subscriptions\_bindings | Map of role (key) and list of members (value) to add the PubSub Subscriptions IAM policies/bindings | map(list(string)) | n/a | yes |
| pubsub\_subscriptions\_mode | Mode for adding the PubSub Subscriptions IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| pubsub\_topics | PubSub Topics list to add the IAM policies/bindings | list(string) | `<list>` | no |
| pubsub\_topics\_bindings | Map of role (key) and list of members (value) to add the PubSub Topics IAM policies/bindings | map(list(string)) | n/a | yes |
| pubsub\_topics\_mode | Mode for adding the PubSub Topics IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| service\_accounts | Service Accounts list to add the IAM policies/bindings | list(string) | `<list>` | no |
| service\_accounts\_bindings | Map of role (key) and list of members (value) to add the Service Accounts IAM policies/bindings | map(list(string)) | n/a | yes |
| service\_accounts\_mode | Mode for adding the Service Accounts IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| storage\_buckets | Storage Buckets list to add the IAM policies/bindings | list(string) | `<list>` | no |
| storage\_buckets\_bindings | Map of role (key) and list of members (value) to add the Storage Buckets IAM policies/bindings | map(list(string)) | n/a | yes |
| storage\_buckets\_mode | Mode for adding the Storage Buckets IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| subnets | Subnets list to add the IAM policies/bindings | list(string) | `<list>` | no |
| subnets\_bindings | Map of role (key) and list of members (value) to add the Subnets IAM policies/bindings | map(list(string)) | n/a | yes |
| subnets\_mode | Mode for adding the Subnets IAM policies/bindings, additive and authoritative | string | `"additive"` | no |
| subnets\_region | Subnets region | string | n/a | yes |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Caveats

### Referencing values/attributes from other resources

This Terraform module performs operations over some variables before making any changes on the IAM bindings in GCP. Because of the limitations of `for_each` ([more info](https://www.terraform.io/docs/configuration/resources.html#using-expressions-in-for_each)), which is widely used in this module, there are certain limitations to what kind of dynamic values you can provide to the module:

1. Dynamic entities (for example `projects`) are only allowed for 1 entity.
2. If you pass 2 or more entities (for example `projects`), the configuration **MUST** be static, meaning that it can't use any of the other resources' fields to get the entity name from (this includes getting the randomly generated hashes through the `random_id` resource).
3. The role names themselves can never be dynamic.
4. Members may only be dynamic in `authoritative` mode.

## IAM Bindings

You can choose the following resource types to apply the IAM bindings:

- Projects (`projects` variable)
- Organizations(`organizations` variable)
- Folders (`folders` variable)
- Service Accounts (`service_accounts` variable)
- Subnetworks (`subnets` variable)
- Storage buckets (`storage_buckets` variable)
- Pubsub topics (`pubsub_topics` variable)
- Pubsub subscriptions (`pubsub_subscriptions` variable)
- Kms Key Rings (`kms_key_rings` variable)
- Kms Crypto Keys (`kms_crypto_keys` variable)

Set the specified variable on the module call to choose the resources to affect. Remember to set the `mode` [variable](#variables) and give enough [permissions](#permissions) to manage the selected resource as well.

## Requirements

### Terraform plugins

- [Terraform](https://www.terraform.io/downloads.html) 0.12
- [terraform-provider-google](https://github.com/terraform-providers/terraform-provider-google) 2.5
- [terraform-provider-google-beta](https://github.com/terraform-providers/terraform-provider-google-beta) 2.5

### Permissions

In order to execute this module you must have a Service Account with an appropriate role to manage IAM for the applicable resource. The appropriate role differs depending on which resource you are targeting, as follows:

- Organization:
  - Organization Administrator: Access to administer all resources belonging to the organization
    and does not include privileges for billing or organization role administration.
  - Custom: Add resourcemanager.organizations.getIamPolicy and
    resourcemanager.organizations.setIamPolicy permissions.
- Project:
  - Owner: Full access and all permissions for all resources of the project.
  - Projects IAM Admin: allows users to administer IAM policies on projects.
  - Custom: Add resourcemanager.projects.getIamPolicy and resourcemanager.projects.setIamPolicy permissions.
- Folder:
  - The Folder Admin: All available folder permissions.
  - Folder IAM Admin: Allows users to administer IAM policies on folders.
  - Custom: Add resourcemanager.folders.getIamPolicy and
    resourcemanager.folders.setIamPolicy permissions (must be added in the organization).
- Service Account:
  - Service Account Admin: Create and manage service accounts.
  - Custom: Add resourcemanager.organizations.getIamPolicy and
    resourcemanager.organizations.setIamPolicy permissions.
- Subnetwork:
  - Project compute admin: Full control of Compute Engine resources.
  - Project compute network admin: Full control of Compute Engine networking resources.
  - Project custom: Add compute.subnetworks.getIamPolicy	and
    compute.subnetworks.setIamPolicy permissions.
- Storage bucket:
  - Storage Admin: Full control of GCS resources.
  - Storage Legacy Bucket Owner: Read and write access to existing
    buckets with object listing/creation/deletion.
  - Custom: Add storage.buckets.getIamPolicy	and
  storage.buckets.setIamPolicy permissions.
- Pubsub topic:
  - Pub/Sub Admin: Create and manage service accounts.
  - Custom: Add pubsub.topics.getIamPolicy and pubsub.topics.setIamPolicy permissions.
- Pubsub subscription:
  - Pub/Sub Admin role: Create and manage service accounts.
  - Custom role: Add pubsub.subscriptions.getIamPolicy and
    pubsub.subscriptions.setIamPolicy permissions.
- Kms Key Ring:
  - Owner: Full access to all resources.
  - Cloud KMS Admin: Enables management of crypto resources.
  - Custom: Add cloudkms.keyRings.getIamPolicy and cloudkms.keyRings.getIamPolicy permissions.
- Kms Crypto Key:
  - Owner: Full access to all resources.
  - Cloud KMS Admin: Enables management of cryptoresources.
  - Custom: Add cloudkms.cryptoKeys.getIamPolicy	and cloudkms.cryptoKeys.setIamPolicy permissions.

## Install

### Terraform

Be sure you have the correct Terraform version (0.12), you can choose the binary here:
- https://releases.hashicorp.com/terraform/

### Terraform plugins

Be sure you have the compiled plugins on $HOME/.terraform.d/plugins/

- [terraform-provider-google](https://github.com/terraform-providers/terraform-provider-google) 1.20.0
- [terraform-provider-google-beta](https://github.com/terraform-providers/terraform-provider-google-beta) 1.20.0

See each plugin page for more information about how to compile and use them.

## Fast install (optional)

For a fast install, please configure the variables on init_centos.sh  or init_debian.sh script and then launch it.

The script will do:
- Environment variables setting
- Installation of base packages like wget, curl, unzip, gcloud, etc.
- Installation of go 1.9.0
- Installation of Terraform 0.10.x
- Download the terraform-provider-google plugin
- Compile the terraform-provider-google plugin
- Move the terraform-provider-google to the right location

[v1.1.1]: https://registry.terraform.io/modules/terraform-google-modules/iam/google/1.1.1
[terraform-0.12-upgrade]: https://www.terraform.io/upgrade-guides/0-12.html
