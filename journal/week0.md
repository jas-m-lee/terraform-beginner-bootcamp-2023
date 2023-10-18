- [Semantic Versioning](#semantic-versioning)
- [Install the Terraform CLI](#install-the-terraform-cli)
  * [Considerations for the Terraform CLI changes](#considerations-for-the-terraform-cli-changes)
  * [Considerations for a Linux Distribution](#considerations-for-a-linux-distribution)
  * [Refactoring into Bash Scripts](#refactoring-into-bash-scripts)
    + [Shebang](#shebang)
    + [Execution Considerations](#execution-considerations)
    + [Linux Permissions Considerations](#linux-permissions-considerations)
- [Gitpod Lifecycle Before, Init, Command](#gitpod-lifecycle-before--init--command)
- [Working with Env Vars](#working-with-env-vars)
  * [env command](#env-command)
  * [Setting and Unsetting Env Vars](#setting-and-unsetting-env-vars)
  * [Printing Env Vars](#printing-env-vars)
  * [Scoping of Env Vars](#scoping-of-env-vars)
  * [Persisting Env Vars in Gitpod](#persisting-env-vars-in-gitpod)
- [AWS CLI Installation](#aws-cli-installation)
- [Terraform Basics](#terraform-basics)
  * [Terraform Registry](#terraform-registry)
  * [Terraform Console](#terraform-console)
    + [Terraform init](#terraform-init)
    + [Terraform plan](#terraform-plan)
    + [Terraform apply](#terraform-apply)
    + [Terraform Lock Files](#terraform-lock-files)
    + [Terraform State Files](#terraform-state-files)
    + [Terraform Directory](#terraform-directory)
    + [Terraform Destroy](#terraform-destroy)
    + [Terraform Directory](#terraform-directory-1)
- [Creating an Alias](#creating-an-alias)
- [Issues with Terraform Cloud Login and Gitpod Workspace](#issues-with-terraform-cloud-login-and-gitpod-workspace)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

## Semantic Versioning 

For tagging, this project will utilzie semantic versioning.
[semver.org](https://semver.org/)

The general format:
**MAJOR.MINOR.PATCH**, eg. `1.0.1`

- **MAJOR** version when you make incompatible API changes
- **MINOR** version when you add functionality in a backward compatible manner
- **PATCH** version when you make backward compatible bug fixes

## Install the Terraform CLI

### Considerations for the Terraform CLI changes
The Terraform CLI installation instructions have changed due to gpg keyring changes. So, we referenced the lastest intall CLI instructions via Terraform Documentation and change the scripting for install.

[Install Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

### Considerations for a Linux Distribution

This project is build against Ubuntu. Please consider checking you Linux Distribution and change according to your distribution needs.

[How to Check OS Version in Linux](https://linuxize.com/post/how-to-check-linux-version/)

Example of Checking the OS Version
```
 cat /etc/os-release
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

While fixing the Terraform CLI GNU implementation of PGP (gpg) deprecation issues we noticed that bash scripts steps were a considerable amount more code. So, we decided to create a bash script to install the Terraform CLI.

Find the bash script at the following location: [./bin/install_terraform_cli](./bin/install_terraform_cli)

- This will keep the Gitpod Task File ([.gitpod.ym](.gitpod.yml)) tidy.
- This will allow us an easier way to debug and manually execute the Terraform CLI install command.
- This will allow better portability for other projects that need to install Terraform CLI.

#### Shebang

A Shebang (pronounced Sha-bang) tells the bash script what progam will interpret the script. e.g. `#!/bin/bash`

Generative AI recommended this format for bash for the reasons listed below:

`#!/usr/bin/env bash`

- Allows portability for different OS distributions.
- It will search the user's PATH for the bash script.

[Using Shebang](https://www.baeldung.com/linux/shebang)

#### Execution Considerations

When executing the bash script we can use the `./` shorthand notation to execute the bash script.

e.g. `./bin/install_terraform_cli`

If we are using a script in .gitpod.yml we need to point the script to a program to interpert it.

e.g. `source ./bin/install_terraform_cli`

[Using shebang](https://www.baeldung.com/linux/bash-shebang-lines
)

#### Linux Permissions Considerations 

In order to make our bash scripts executable we need to change the Linux permission for the fix to be executable at the user mode.

```sh
chmod u+x ./bin/install_terraform_cli
```

An alternative is to change permissions numerically:

```sh
chmod 744 ./bin/install_terraform_cli
```

[Using chmod](https://linuxize.com/post/chmod-command-in-linux/
)

## Gitpod Lifecycle Before, Init, Command

We need to be careful when using Init because it will not rerun if we restart an existing workspace.

[Gitpod Lifecycle](https://www.gitpod.io/docs/configure/workspaces/tasks)

## Working with Env Vars

### env command

We can list out all Environment Variables (Env Vars) using the `env` command

We can filter specific env vars using grep e.g. `env | grep AWS_`

### Setting and Unsetting Env Vars

In the terminal we can set using `export HELLO=world`

In the terminal we unset using `unset HELLO`

We can set an env var temporarily when just running a command.

```sh
HELLO='world' ./bin/print_message
```

Within a bash script we can set env without writing export.
 
```sh
#!/usr/bin/env bash
HELLO='world'
echo HELLO
```

### Printing Env Vars

We can print an env var using echo eg. `echo $HELLO`

### Scoping of Env Vars

When you open up new bash terminals in VSCode it will not be aware of env vars that you have set in another window.

If you want the env vars to persist across all future bash terminals that are open you need to set env vars in you bash profile. e.g. `.bash_profile`

### Persisting Env Vars in Gitpod

We can persist env vars into gitpod by storing them in Gitpod Secrets Storage.

```
gp env HELLO='world'
```

All future workspaces launched will set the env vars for all bash terminals opened in those workspaces.

You can also set env vars in the `.gitpod.yml` but this can only ocntain no-sensitive env vars.

## AWS CLI Installation

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

Terraform sources their providers and modules from the Terraform registry.

**Terraform registry** is located at [registry.terraform.io](https://registry.terraform.io/)

**Providers**

- An interface to APIs that will allow you to create resources in Terraform.

**Modules**

- A way to refactor large amounts of Terraform code to be modular, portable, and sharable.

[Random Terraform Provider](https://registry.terraform.io/providers/hashicorp/random/)

### Terraform Console

To see a list of Terraform commands type the `terraform` command.

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

#### Terraform Lock Files

`.terraform.lock.hcl` contains the locked versioning for the providers or modules that are used with this project.

Should be committed to the Version Control System (VSC) e.g. Github.

#### Terraform State Files

`.terraform.tfstate` contains information about the current state of the infrastructure.

Should **not** be committed to VCS.

This file contains sensitive data. If you lose this file, you lose knowing the state of your infrastructure.

`.terraform.tfstate.backup` is the previous state file state.

#### Terraform Directory

`.terraform` directory contains binaries of Terraform providers.

#### Terraform Destroy

When you are ready to destroy resources, use the following command:

`terraform destroy`

Auto approve flag can be used to skip the approve prompt. e.g. `terraform apply --auto-approve`.

#### Terraform Directory

`.terraform` directory contains binaries of terraform providers.

## Creating an Alias

To create an alias for the `terraform` commmand we used the following format:

```sh
alias alias_name="command_to_run"
```

To make the alias permanent, a bash script is necessary.

```sh
#!/usr/bin/env bash

# Define the alias
ALIAS="tf"
COMMAND="terraform"

# Check if the alias already exists in .bash_profile
if grep -q "alias $ALIAS=\"$COMMAND\"" "$HOME/.bash_profile"; then
  echo "Alias '$ALIAS' already exists in .bash_profile."
else
  # If the alias doesn't exist, add it to .bash_profile
  echo "Adding alias '$ALIAS' to .bash_profile..."
  echo "alias $ALIAS=\"$COMMAND\"" >> "$HOME/.bash_profile"
  echo "Alias added."
fi

# Apply the changes immediately
source ~/.bash_profile
```
[Create a Bash Alias](https://linuxize.com/post/how-to-create-bash-aliases/)

## Issues with Terraform Cloud Login and Gitpod Workspace

When attempting to run `terraform login` it will launch bash a wiswig view to generate a token. However it does not work expected in Gitpod VsCode in the browser.

To fix this issue, manually generate a token in Terraform Cloud through the link below:

```
https://app.terraform.io/app/settings/tokens?source=terraform-login
```

Then create open the file manually here:

```sh
touch /home/gitpod/.terraform.d/credentials.tfrc.json
open /home/gitpod/.terraform.d/credentials.tfrc.json
```

Provide the following code (replace your token in the file):

```json
{
  "credentials": {
    "app.terraform.io": {
      "token": "YOUR-TERRAFORM-CLOUD-TOKEN"
    }
  }
}
``````

We have automated this workaround with the following bash script [bin/generate_tfrc_credentials](bin/generate_tfrc_credentials)
