## Helm: The Kubernetes Package Manager

### What is Helm?

Helm is often described as the "package manager for Kubernetes." Similar to how operating systems like Debian (with `apt`), Red Hat (with `yum`), or macOS (with `brew`) have package managers, Kubernetes has Helm.

**But what does that mean?**

At its core, Helm assists in defining, installing, and upgrading even the most complex Kubernetes applications. It uses a packaging format called **charts**, which are a collection of pre-configured Kubernetes resources.

### Helm Components:

1. **Chart**: A Helm package that contains definitions for creating related Kubernetes resources. Think of it as the 'recipe' for your Kubernetes application. It might include things like a `Deployment` for your main application server, a `Service` to expose it, and perhaps a `ConfigMap` for your app's configuration.

2. **Release**: Once you install a chart into a Kubernetes cluster, that installed instance is called a release. Each release has a unique name within the cluster and tracks the version of the chart used.

3. **Repository**: Like package managers such as `apt` or `yum` have repositories from which they pull software, Helm too pulls charts from a repository of chart collections.

### Key Features of Helm:

- **Manage Complexity**: Kubernetes manifests, especially in the context of microservices, can become quite detailed and numerous. Helm charts allow you to define, install, and upgrade even the most intricate applications.

- **Reproducibility**: Helm charts are versioned, and can be shared, ensuring that applications are the same from one environment to another. This is pivotal in CI/CD pipelines.

- **Rollbacks**: Helm has the capability to rollback to a previous release, which can be vital when facing production issues.

- **Templating**: Helm charts utilize templates to generate Kubernetes manifest files. This allows you to use the same chart to deploy multiple environments by just changing the values fed into the template.

- **Release Management**: With Helm, you can manage the lifecycle of your application deployment, from its installation to its upgrade and deletion.

### Why Use Helm?

While Kubernetes can be incredibly powerful, managing individual YAML files for each component of a complex application becomes unwieldy quickly. Helm streamlines this process, making it easier to deploy, manage, and update applications in a Kubernetes environment.

In addition to this, Helm's ecosystem provides many pre-created charts for common software needs, saving teams the effort of writing and maintaining extensive Kubernetes manifests.

Helm is a vital tool in the Kubernetes ecosystem, providing simplicity, power, and flexibility to manage and deploy applications. Its chart system abstracts the complexities of raw Kubernetes manifests, allowing developers and operators to streamline their deployment workflows.

## Installing Helm

[Helm](https://helm.sh/) is a package manager for Kubernetes that allows developers and operators to more easily package, configure, and deploy applications and services onto Kubernetes clusters.

In this tutorial, we'll go over how to download and install Helm v3.13.0 on a Debian system.

**Prerequisites**

- A Debian-based machine with internet access
- `sudo` privileges

## Install Helm CLI

```bash
curl -LO https://get.helm.sh/helm-<GO TO RELEASES PAGE TO FIND LATEST>
tar -C /tmp/ -zxvf helm-<TAsudo B>
rm helm-<TAB>
mv /tmp/linux-amd64/helm /usr/local/bin/helm
chmod +x /usr/local/bin/helm
# verify helm is installed
helm version
```

1. **Download Helm**:
   To download the Helm binary, use the following `wget` command:

   ```bash
   wget https://get.helm.sh/helm-v3.13.0-linux-amd64.tar.gz
   ```

   This command will fetch the Helm package for Linux AMD64 architecture.

   You may get the correct link for your architecture from the official Helm website. 

2. **Extract the Helm package**:
   Once downloaded, you'll have a tarball which needs to be extracted to get the Helm binary:

   ```bash
   tar -zxvf helm-v3.13.0-linux-amd64.tar.gz
   ```

   This will extract the files into a directory named `linux-amd64`.

3. **Move the Helm binary to a system-wide location**:
   To make the `helm` command available system-wide, move the binary to `/usr/local/bin/`:

   ```bash
   sudo mv linux-amd64/helm /usr/local/bin/helm
   ```

   Note: We use `sudo` here since regular users might not have permissions to write to `/usr/local/bin/`.

4. **Verify Installation**:
   Simply type `helm` in your terminal:

   ```bash
   helm
   ```

   This should display the Helm help and its various commands, confirming that Helm has been successfully installed.
