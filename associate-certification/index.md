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

See [companion repo](../learn-terraform-provisionning-cloudinit/).

We deploy on AWS ( see [authentication](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication) docs ) a micro instance of virtual machine on a virtual private cloud, pre-networked, inside a security group.. The point being that the machine comes "blank", so we use a [cloudinit](# https://cloudinit.readthedocs.io/en/latest/topics/examples.html) script to configure the ssh access, the user groups, get some dependencies ( here Go, and a example web app ) in order to populate the machine. We learn how to pass an arbitrary script to a machine deployed through TF, using a [template file provider](https://registry.terraform.io/providers/hashicorp/template/latest/docs).

#### [Provision Infrastructure with Packer](https://learn.hashicorp.com/tutorials/terraform/packer?in=terraform/provision)

See [companion repo](../learn-terraform-provisionning-packer/).

We deploy the same infra. The difference in the pre-configuration of the VM this time, is that we use Packer instead. Packer builds automated machine images, using a provided configuration script. This image is hosted on AWS, and we pass a reference to it into the tf main script to deploy it.

### [Provisioners are a Last Resort](https://www.terraform.io/docs/language/resources/provisioners/syntax.html#provisioners-are-a-last-resort)

Provisioners are a escape hatch from TF declarative model and its providers and modules system. There are many way to not use them that should be considered beforehand. If it's not possible to do otherwise you can add a `provisioner` block inside a `resource` block.

By default, provisioners run when the resource they are defined within is created. They are meant as a means to perform bootstrapping of a system. If `when = destroy` is specified, the provisioner will run when the resource it is defined within is destroyed.

Most provisioners require access to the remote resource via SSH or WinRM, and expect a nested connection block with details about how to connect.

The main provisioners are the [`file`](https://www.terraform.io/docs/language/resources/provisioners/file.html), [`local-exec`](https://www.terraform.io/docs/language/resources/provisioners/local-exec.html) and the [`remote-exec`](https://www.terraform.io/docs/language/resources/provisioners/remote-exec.html) provisioners.

### [Manage Resources in Terraform State Learn](https://learn.hashicorp.com/tutorials/terraform/state-cli)

See [companion repo](../learn-terraform-state/).

#### Create infrastructure and state

We deploy a micro compute instance with an Ubuntu AMI, and read after apply the `terraform.tfstate` file to see how TF tracks the resources we just created.

#### Examine the state file

We can see for example that the aws_instance type is a managed resource with the AMI from the data.aws_ami source. We can see dependencies between resources marked in their respective `dependencies` block.

#### Examine State with CLI

You can use `terraform show` to get a human-friendly output of the resources contained in your state, or run `terraform state list` to get the list of resource names and local identifiers in your state file.

#### Replace a resource with CLI

Instead of updating the state by hand, or destroy / recreate resources, one can use the `-replace` flag of the CLI. If the resource state has drifted executing `terraform plan -replace="aws_instance.example"` would recreate it per the stored configuration.

#### Move a resource to a different state file

The `terraform state mv` command moves resources from one state file to another. The move command will update the resource in state, but not in your configuration file. As such reapplying the configuration will destroy the moved resources.

#### Remove a resource from state

The `terraform state rm` subcommand removes specific resources from your state file. This does not remove the resource from your configuration or destroy the infrastructure itself.

#### Refresh modified infrastructure

The `terraform refresh` command updates the state file when physical resources change outside of the Terraform workflow (configuration drift). We can destroy a resource using the cloud provider cli ( aws cli here ) or using the GUI, and run the `tf refresh` command to see that it was destroyed. Re-running `tf plan` will plan to add the resource back.

### [Use Refresh-Only Mode to Sync Terraform State](https://learn.hashicorp.com/tutorials/terraform/refresh)

See [companion repo](../learn-terraform-refresh/).

The default region of the infra is `us-east-2`. After deploying it there we add a `terraform.tfvars` file, which sets the region to be `us-west-2` instead. When executing the `terraform plan -refresh-only` we can see that, not being able to locate the infrastructure in this new region, TF assume we have destroyed it, and wants to remove it from the state.

### [Lock and Upgrade Provider Versions](https://learn.hashicorp.com/tutorials/terraform/provider-versioning)
