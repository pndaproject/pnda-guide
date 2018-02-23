# Create PNDA cluster

![](../images/breadcrumbs-create.jpg)

## Introduction

With the target platform fully prepared, the PNDA software staged and the YAML configuration completed, the final step is to invoke the PNDA CLI to create the cluster.

## Create PNDA

#### Install CLI dependencies

On the client machine, install the pip packages required by the CLI. Navigate to the folder containing the pnda-cli repository, then run the following commands:

```
cd cli
sudo pip install -r requirements.txt
```

#### CLI invocation

**Important:** ensure you are certain what version of PNDA you want to deploy, and specify the correct branch or tag when invoking the CLI using the -b option. In most circumstances you'll want to make sure the branch or tag you specify is identical to the branch or tag you used to build the PNDA mirror, and identical to the version you checked out from the pnda-cli repository. All PNDA releases are designated with a tag such as ```release/4.0``` across all repositories.

An example CLI invocation -

```
pnda-cli.py -e pnda -s mykey -f production -m 'existing-machines/production.json' create -b release/4.0
```

Note the -m flag for existing-machines installation.

Note also that CLI parameter -s refer to the key configured in the previous phase. For example, if the keypair in AWS is 'pnda' then the local private key file should be named 'pnda.pem' and you should pass '-s pnda' to the CLI.

This phase of the installation typically takes around 45 minutes to an hour to complete.

# [Home](../OVERVIEW.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- |
