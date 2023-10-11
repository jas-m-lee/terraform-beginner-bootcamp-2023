# Terraform Beginner Bootcamp 2023

## Semantic Versioning :bulb:

For tagging, this project will utilzie semantic versioning.
[semver.org](https://semver.org/)

The general format:
**MAJOR.MINOR.PATCH**, eg. `1.0.1`

- **MAJOR** version when you make incompatible API changes
- **MINOR** version when you add functionality in a backward compatible manner
- **PATCH** version when you make backward compatible bug fixes

## Install the Terraform CLI

### Considerations with the Terraform CLI changes
The Terraform CLI installation instructions have changed due to gpg keyring changes. So, we referenced the lastest intall CLI instructions via Terraform Documentation and change the scripting for install.

[Install Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

### Considerations for Linux Distribution

This project is build against Ubuntu. Please consider checking you Linux Distribution and change according to your distribution needs.

[How to Check OS Version in Linux](https://linuxize.com/post/how-to-check-linux-version/)

Example of Checking the OS Version
```
gitpod /workspace/terraform-beginner-bootcamp-2023 (main) $ cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

### Refactoring into Bash Scripts

While fixing the Terraform CLI gpg depracation issues we notice that bash scripts steps were a considerable amount more code. So, we decided to create a bash script to install the Terraform CLI.

Find the bash script at the following location: [./bin/install_terraform_cli](./bin/install_terraform_cli)

- This will keep the Gitpod Task File ([.gitpod.ym](.gitpod.yml)) tidy.
- This allow us an easier to debug and execute manually Terraform CLI install
- This will allow better protability for other projects that need to install Terraform CLI.

#### Shebang

A Shebang (pronounced Sha-bang) tells the bash script what progam will interpret the script.

Generative AI recommended this format for bash:

`#!/usr/bin/env bash`

- portability for different OS distributions
- will search the user's PATH for the bash script

#### Execution Considerations

When executing the bash script we can use the `./` shorthand notation to execute the bash script.

eg. `./bin/install_terraform_cli`

If we are using a script in .gitpod.yml we need to point the script to a program to interpert it.

eg. `source ./bin/install_terraform_cli`

[Using shebang](https://www.baeldung.com/linux/bash-shebang-lines
)

#### Linux Permissions Considerations 

In order to make our bash scripts executable we need to change Linux permission for the fix to be executable at the user mode.

```sh
chmod u+x ./bin/install_terraform_cli
```

An alternative is to change permissions numerically:
```sh
chmod 744 ./bin/install_terraform_cli
```

[Using chmod](https://linuxize.com/post/chmod-command-in-linux/
)

### Gitpod Lifecycle (Before, Init, Command)

We need to be careful when using the Init because it will not rerun if we restart an existing workspace.

[Gitpod Lifecycle](https://www.gitpod.io/docs/configure/workspaces/tasks)

### Working with Env Vars

#### env command

We can list out all Environment Variables (Env Vars) using the `env` command
We can filter specific env ars using grep eg. `env | grep AWS_`

#### SEtting and Unsetting Env Vars

In the terminal we can set using `export HELLO=world`
In the terminal we unset using `unset HELLO`
We can set an env var temporarily when just running a command.

```sh
HELLO='world' ./bin/print_message
```

Within a bash script we can set env without writing export eg.

```sh
#!/usr/bin/env bash
HELLO='world'
echo HELLO
```

#### Printing Env Vars

We can print an env var using echo eg. `echo $HELLO`

#### Scoping of Env Vars

When you open up new bash terminals in VSCode itwill not be awre of env vars that you have set in another window.

If you want the Env Vars to persist across all future bash terminals that are open you need to set env vars in you bash profile. eg. `.bash_profile`

#### Persisting Env Vars in Gitpod

We can persist env vars into gitpod by storing them in Gitpod Secrets Storage.

```
gp env HELLO='world'
```
All future workspaces launched will set the env vars for all bash terminals opened in those workspaces.

You can also set env vars in the `.gitpod.yml` but this can only ocntain no-sensitive env vars

## Installation for AWS CLI

AWS CLI is installed for this project via the bash script [`./bin/install_aws_cli`](./bin/install_aws_cli)

[Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

[Set the Env Vars](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)

To check if the AWS credentials are configured correctly, use the following AWS CLI command:

```sh
aws sts get-caller-identity
```

Confirmation of a successful configuration will output a similar JSON payload as seen below.

```sh
{
  "UserId": "ADLVMEJXEMJVEAJWLNEQ",
  "Account": "210987654321",
  "Arn": "arn:aws:iam::365123456789:user/terraform-beginner-bootcamper"
}
```

AWS CLI credentials are generated from an IAM User to allow use of the AWS CLI.

[Create an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

[Get Access Keys](https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication-user.html#cli-authentication-user-get)

## Terraform Basics

### Terraform Registry

Terraform sources their providers and modules from the Terraform registry

**Terraform registry** is located at [registry.terraform.io](https://registry.terraform.io/)

**Providers**

An interface to APIs that will allow you to create resources in Terraform.

**Modules**

A way to refactor large amounts of Terraform code to be modular, portable, and sharable.

[Random Terraform Provider](https://registry.terraform.io/providers/hashicorp/random/)

### Terraform Console

To see a list of Terraform commands type `terraform`

#### Terraform init

At the start of a new Terraform project we run `terraform init` to download binarires for the Terraform providers that are used in this project.

#### Terraform plan

`terraform plan`

Generates out a changeset about the state of the infrastructure and what will be changed.

We can output this changeset i.e. “plan” to be passed to an apply but often you can just ignore outputting.

#### Terraform apply

`terraform apply`

Runs a plan and passes the changeset to be executed by Terraform. This command should prompt us approve.

To automatically approve an apply we can provide the auto-approve flag e.g. `terraform --auto-approve`

### Terraform Lock Files

`.terraform.lock.hcl` contains the locked versioning for the providers or modules that are used with this project.

Should be committed to the Version Control System (VSC) e.g. Github

### Terraform State Files

`.terraform.tfstate` contains information about the current state of the infrastructure.

Should **not** be committed to VCS.

This file contains sensitive data. If you lose this file, you lose knowing the state of your infrastructure.

`.terraform.tfstate.backup` is the previous state file state.

### Terraform Directory

`.terraform` directory contains binaries of Terraform providers.
