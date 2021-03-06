---
title: "Starting Terraform Runs in Atlas"
---

# Starting Terraform Runs in Atlas

There are a variety of ways to queue a Terraform run in Atlas. In addition to
`terraform push`, you can connect your [environment](/help/glossary#environment)
to GitHub and have Atlas queue Terraform runs based on new commits. Atlas can
also intelligently queue new runs when linked artifacts are uploaded or changed.
Remember from the [previous section about Terraform runs](/help/terraform/runs)
that it is safe to trigger many plans without consequence since Terraform plans
do not change infrastructure.


## Terraform Push

Terraform `push` is a [Terraform command](https://terraform.io/docs/commands/push.html)
that packages and uploads a set of Terraform configuration and directory to Atlas. This then creates a run
in Atlas, which performs `terraform plan` and `terraform apply` against the uploaded
configuration.

The directory is included in order to run any associated provisioners,
that might use local files. For example, a remote-exec provisioner
that executes a shell script.

By default, everything in your directory is uploaded as part of the push.

However, it's not always the case that the entire directory should be uploaded. Often,
temporary or cache directories and files like `.git`, `.tmp` will be included by default. This
can cause Atlas to fail at certain sizes and should be avoided. You can
specify [exclusions](https://terraform.io/docs/commands/push.html) to avoid this situation.

Terraform also allows for a [VCS option](https://terraform.io/docs/commands/push.html#_vcs_true)
that will detect your VCS (if there is one) and only upload the files that are tracked by the VCS. This is
useful for automatically excluding ignored files. In a VCS like git, this
basically does a `git ls-files`.


## GitHub Import

Optionally, GitHub can be used to import Terraform configuration,
automatically queuing runs when changes are merged into the default branch
or plans when a PR is made.

When used within an organization, this can be extremely valuable for keeping
differences in environments and last mile changes from occurring before an
upload to Atlas.

This works by first connecting your [environment](/help/glossary#environment) to the target
GitHub repository and then pushing changes. Currently, an environment
must already exist to be connected to Github. You can create the environment
with `terraform push`, detailed above, and then link it to GitHub.

By default, all commits to the default branch of your repository and all commits
to an open Pull Request will trigger a Terraform plan. You can disable a plan by
adding the text `[atlas skip]` or `[ci skip]` to your commit message.


## Artifact Uploads

Upon successful completion of a Terraform run, Atlas parses the remote state and
detects any [Atlas artifacts](/help/terraform/artifacts/artifact-provider) that
were referenced. When new versions of those referenced artifacts are uploaded
to Atlas, you have the option to automatically queue a new Terraform run.

For example, consider the following Terraform configuration which references an
Atlas artifact named "worker":

    resource "aws_instance" "worker" {
      ami = "${atlas_artifact.worker.metadata_full.region-us-east-1}"
      instance_type = "m1.small"
    }

When a new version of the Atlas artifact "worker" is uploaded either manually
or as the output of a [Packer build](/help/packer/builds/starting.html), Atlas
can automatically trigger a Terraform plan with this new artifact version.
You can enable this feature on a per-environment basis from the
[environment](/help/glossary#environment) settings page in Atlas.

Combined with
[Terraform auto apply](/help/terraform/runs/automatic-applies), you can
continuously deliver infrastructure using Terraform and Atlas.
