CKAD: Kustomize
Kustomize is a powerful tool built into Kubernetes for customizing Kubernetes resource YAML files. It enables you to manage configurations in a more flexible and reusable manner by allowing you to define a "base" configuration and customize it per environment without modifying the original resource files.
In this section, we will dive into how Kustomize works, how to use it for customizing Kubernetes resources, and why it is essential for a Kubernetes Application Developer (CKAD) to understand it.

1. What is Kustomize?
Kustomize is a tool that helps Kubernetes users manage their configurations more efficiently. Unlike Helm, which uses templates, Kustomize works by creating a customization layer on top of existing Kubernetes manifests. Kustomize allows you to reuse the same base configuration files across different environments (e.g., development, staging, production) with environment-specific overlays.
Key features of Kustomize:
No Templating: Kustomize does not require any templating logic (like Helm), making it simpler to understand.
Customization: It enables you to customize resource definitions (e.g., replicas, image names, labels, etc.) per environment using overlays.
Layered Configuration: You can create a "base" set of configurations and then apply customizations on top of it for specific environments.

2. Kustomize Workflow
The Kustomize workflow revolves around two main concepts:
Base: The core configuration files (e.g., deployments, services) that are common across all environments.
Overlay: Environment-specific customizations on top of the base configuration (e.g., changing the number of replicas or the image version for a specific environment).
The overall process looks like this:
Create a base configuration with reusable Kubernetes manifests.
Define environment-specific overlays that modify the base configuration.
Apply the kustomized resources using the kubectl command.

3. Kustomize Directory Structure
A typical Kustomize project might have the following directory structure:
/my-app
  /base
    deployment.yaml
    service.yaml
    kustomization.yaml
  /overlays
    /dev
      kustomization.yaml
      deployment.yaml  # Customizations for dev
    /prod
      kustomization.yaml
      deployment.yaml  # Customizations for prod

Base Directory: Contains the common resources that will be reused across all environments.
Overlays Directory: Contains the environment-specific customizations.
kustomization.yaml File: This file is used by Kustomize to know which resources and customizations to apply.

4. Using Kustomize: Basic Example
Let’s walk through a simple example of how to use Kustomize to customize Kubernetes manifests.
Step 1: Create Base Configuration
In your base/ directory, create a deployment.yaml file for your application:
# base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:1.0
          ports:
            - containerPort: 80

Next, create a kustomization.yaml file in the base directory to define the resources:
# base/kustomization.yaml
resources:
  - deployment.yaml

This kustomization.yaml tells Kustomize to manage the deployment.yaml file.
Step 2: Create Overlay for Development (dev)
In the overlays/dev/ directory, create a kustomization.yaml file:
# overlays/dev/kustomization.yaml
resources:
  - ../../base

patchesStrategicMerge:
  - deployment.yaml

Here, the resources field includes the base configuration (../../base), and we also provide a patch for the deployment.yaml file to make environment-specific changes.
Now, create the deployment.yaml patch for development:
# overlays/dev/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2  # Increase replicas for the dev environment
  template:
    spec:
      containers:
        - name: my-app
          image: my-app:dev  # Use the dev version of the image

Step 3: Create Overlay for Production (prod)
In the overlays/prod/ directory, create a kustomization.yaml file:
# overlays/prod/kustomization.yaml
resources:
  - ../../base

patchesStrategicMerge:
  - deployment.yaml

And the deployment.yaml patch for production:
# overlays/prod/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 5  # Increase replicas for the prod environment
  template:
    spec:
      containers:
        - name: my-app
          image: my-app:latest  # Use the latest production image


5. Apply Kustomized Resources
Once you’ve created the base and overlays, you can deploy your resources to a Kubernetes cluster.
For the Development Environment:
To apply the resources for the dev environment, navigate to the overlays/dev directory and run:
kubectl apply -k overlays/dev/

This will combine the base and the dev overlay and apply the configuration to your cluster. The resulting deployment will have 2 replicas and use the my-app:dev image.
For the Production Environment:
Similarly, for production:
kubectl apply -k overlays/prod/

This will apply the production-specific customizations, such as 5 replicas and using the my-app:latest image.

6. Advantages of Using Kustomize
No Templating Logic: Unlike Helm, Kustomize doesn’t require complex templating, which means it’s easier to use for simple customization.
Environment-Specific Configuration: You can easily apply environment-specific customizations (e.g., different numbers of replicas, different images) without modifying the base files.
Reusability: Kustomize encourages reusable configurations. You can define a base configuration that can be used in different environments, reducing duplication.
Declarative and Version-Control Friendly: Since Kustomize works with plain YAML files, it's easier to version control, and changes are clear and declarative.

7. Kustomize vs Helm
Kustomize:
Primarily used for customizing existing YAML files (no templating).
Works by layering and patching base configurations for different environments.
Ideal for simpler scenarios where you don't need the complexity of Helm charts.
Helm:
Uses templating to generate Kubernetes YAML files from templates.
Suitable for more complex, reusable packages with variables, values files, and dependencies.
Provides additional functionality like dependency management and versioning.

8. Conclusion
Kustomize is a powerful tool for Kubernetes developers, especially for CKADs who need to manage Kubernetes resources in a clean, environment-specific manner. By allowing you to customize resource definitions across multiple environments (like dev, staging, and prod) without modifying the original YAML files, Kustomize is a great option for managing Kubernetes resources in a maintainable way.
With Kustomize, you can:
Create a base configuration and layer environment-specific customizations on top.
Easily apply customizations without modifying the base files.
Use simple declarative YAML files, which makes it version control-friendly and easier to understand.
By mastering Kustomize, you can streamline your Kubernetes deployment process and manage configurations more effectively, making it an essential tool in your toolkit as a Kubernetes Application Developer (CKAD).

Application Observability and Maintenance15%
