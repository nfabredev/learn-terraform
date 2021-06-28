<!-- https://learn.hashicorp.com/tutorials/terraform/associate-study?in=terraform/certification -->

# Study Guide - Terraform Associate Certification

## [Learn about IaC](https://learn.hashicorp.com/tutorials/terraform/associate-study?in=terraform/certification)

### [Infrastructure as Code: What Is It? Why Is It Important?](https://www.hashicorp.com/resources/what-is-infrastructure-as-code)

The drivers of IaC are the cloud based infrastructure model and the elasticity of the demand. Because of those we need versionned, automated and reusable way of deploying infrastructure so we can lower the costs of the infra. It also avoids repetitive and not scalable GUI driven infra administration.

### [Infrastructure as Code in a Private or Public Cloud](https://www.hashicorp.com/blog/infrastructure-as-code-in-a-private-or-public-cloud)

Using code that is both machine and human readable.
Standardize workflow accross infra providers.
IaC makes changes idempotent, consistent, repeatable, and predictable

### [Manage infrastructure](https://learn.hashicorp.com/tutorials/terraform/associate-study?in=terraform/certification#manage-infrastructure)

Do the get started tutorial. See [`../azure`](../azure).

#### [Providers configuration](https://www.terraform.io/docs/language/providers/configuration.html)

##### [Providers requirements](https://www.terraform.io/docs/language/providers/requirements.html)

Provider requirements are declared in a `required_providers` block, nested inside the top-level `terraform` block. The key determines the provider's `local name`, and the value is an object with the following elements:

- source - the global source address for the provider you intend to use, such as hashicorp/aws.
- version - a version constraint specifying which subset of available provider versions the module is compatible with.

These help to tell which providers we are going to use.

##### Providers configuration

Provider configurations belong in the root module of a Terraform configuration. A provider configuration is created using a provider block:

The body of the block contains configuration arguments for the provider, defined by the provider itself.

### [Purpose of Terraform State](https://www.terraform.io/docs/language/state/purpose.html)

#### Mapping to the Real World

Terraform requires some sort of database to map Terraform config to the real world. Terraform expects that each remote object is bound to only one resource instance

#### Metadata

Alongside the mappings between resources and remote objects, Terraform must also track metadata such as resource dependencies.

When you delete a resource from a Terraform configuration, Terraform must know how to delete that resource. Terraform can see that a mapping exists for a resource not in your configuration and plan to destroy. However, since the configuration no longer exists, the order cannot be determined from the configuration alone.

#### Performance

For larger infrastructures, querying every resource is too slow. In these scenarios, the cached state is treated as the record of truth.

#### Syncing

When using Terraform in a team it is important for everyone to be working with the same state so that operations will be applied to the same remote objects.

With a fully-featured state backend, Terraform can use remote locking as a measure to avoid two or more different users accidentally running Terraform at the same time, and thus ensure that each Terraform run begins with the most recent updated state.

### [Terraform Settings](https://www.terraform.io/docs/language/settings/index.html)

The special terraform configuration block type is used to configure some behaviors of Terraform itself

#### [Configuring a Terraform Backend](https://www.terraform.io/docs/language/settings/backends/configuration.html)

#### Specifying a Required Terraform Version

The required_version setting accepts a version constraint string, which specifies which versions of Terraform can be used with your configuration. When you use child modules, each module can specify its own version requirements. The requirements of all modules in the tree must be satisfied.

#### [Specifying Provider Requirements](https://www.terraform.io/docs/language/providers/requirements.html)

The required_providers block specifies all of the providers required by the current module, mapping each local provider name to a source address and a version constraint.

#### Experimental Language Features

In releases where experimental features are available, you can enable them on a per-module basis by setting the experiments argument inside a terraform block:

#### [Passing Metadata to Providers](https://www.terraform.io/docs/internals/provider-meta.html)

In some situations it's beneficial for a provider to offer an interface through which modules can pass it information unrelated to the resources in the module, but scoped on a per-module basis.

### Provision Infrastructure Deployed with Terraform

#### [Provision Infrastructure with Cloud-Init](https://learn.hashicorp.com/tutorials/terraform/cloud-init?in=terraform/provision)

We deploy on AWS ( see [authentication](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication) docs ) a micro instance of virtual machine on a virtual private cloud, pre-networked, inside a security group.. The point being that the machine comes "blank", so we use a [cloudinit](# https://cloudinit.readthedocs.io/en/latest/topics/examples.html) script to configure the ssh access, the user groups, get some dependencies ( here Go, and a example web app ) in order to populate the machine. We learn how to pass an arbitrary script to a machine deployed through TF, using a [template file provider](https://registry.terraform.io/providers/hashicorp/template/latest/docs).

#### [Provision Infrastructure with Packer](https://learn.hashicorp.com/tutorials/terraform/packer?in=terraform/provision)

We deploy the same infra. The difference in the pre-configuration of the VM this time, is that we use Packer instead. Packer builds automated machine images, using a provided configuration script.

### [Provisioners are a Last Resort](https://www.terraform.io/docs/language/resources/provisioners/syntax.html#provisioners-are-a-last-resort)

tbc...
