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

### Shebang

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