[[k3d]]

---


When using k3d (Kubernetes in Docker), the correct `KUBECONFIG` configuration is important to ensure that your `kubectl` commands interact with the correct k3d cluster. k3d provides utilities to easily manage the kubeconfig.

Here's how you can configure `KUBECONFIG` for k3d:

### 1. Create or Use a k3d Cluster

First, ensure you have a k3d cluster running. If you don't have one, you can create it using:

```sh

k3d cluster create mycluster

```

### 2. Get the Kubeconfig for the Cluster

You need to get or merge the kubeconfig for your k3d cluster. k3d has a command for this:

#### Merge Kubeconfig

To merge the k3d cluster's kubeconfig with your default kubeconfig (`~/.kube/config`):

```sh

k3d kubeconfig merge mycluster --kubeconfig-merge-default

```

#### Get and Save Kubeconfig

To save the k3d cluster's kubeconfig to a specific file:

```sh

k3d kubeconfig get mycluster > $HOME/.kube/config

```

### 3. Set or Export the KUBECONFIG Environment Variable

Ensure that the `KUBECONFIG` environment variable points to the correct kubeconfig file. This step ensures that `kubectl` uses the right configuration to connect to your k3d cluster.

#### Temporarily Set KUBECONFIG

You can temporarily set the `KUBECONFIG` variable for the current terminal session:

```sh

export KUBECONFIG=$HOME/.kube/config

```

#### Permanently Set KUBECONFIG

To make this change permanent, add the export command to your shell's configuration file (`~/.bashrc`, `~/.bash_profile`, or `~/.zshrc`):

For `bash`:

```sh

echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bashrc

source ~/.bashrc

```

For `zsh`:

```sh

echo 'export KUBECONFIG=$HOME/.zshrc' >> ~/.zshrc

source ~/.zshrc

```

### 4. Verify the Configuration

Verify that the `KUBECONFIG` environment variable is set correctly:

```sh

echo $KUBECONFIG

```

Ensure it points to the correct kubeconfig file. You can then check if `kubectl` is able to connect to the k3d cluster:

```sh

kubectl cluster-info

kubectl get nodes

```

### Example Workflow

Here is an example workflow from creating a cluster to verifying the setup:

1. **Create a k3d Cluster:**

```sh

k3d cluster create mycluster

```

2. **Merge Kubeconfig:**

```sh

k3d kubeconfig merge mycluster --kubeconfig-merge-default

```

3. **Set KUBECONFIG Environment Variable:**

```sh

export KUBECONFIG=$HOME/.kube/config

```

4. **Add to Shell Config for Persistence:**

For `bash`:

```sh

echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bashrc

source ~/.bashrc

```

5. **Verify Connection:**

```sh

kubectl cluster-info

kubectl get nodes

```

By following these steps, you can configure `KUBECONFIG` for k3d and ensure that `kubectl` communicates with the correct Kubernetes cluster.