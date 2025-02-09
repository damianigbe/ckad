CKAD: Use the Helm Package Manager to Deploy Existing Packages
Helm is the most widely used package manager for Kubernetes applications. It simplifies the process of deploying, configuring, and managing Kubernetes resources by enabling you to use Helm charts, which are pre-configured Kubernetes manifests bundled together for easy installation and management. As a Kubernetes Application Developer (CKAD), mastering Helm will help you streamline your deployment process and simplify the management of complex applications.
In this section, we'll cover how to use Helm to deploy existing Helm charts (pre-packaged applications) on a Kubernetes cluster.

1. What is Helm?
Helm is a tool that helps you manage Kubernetes applications through Helm charts. A Helm chart is a collection of files that describe a related set of Kubernetes resources, such as Deployments, Services, ConfigMaps, and more. Helm packages these resources together, allowing you to deploy and manage them as a single unit.
Key Concepts:
Helm Chart: A package of pre-configured Kubernetes resources.
Release: An instance of a Helm chart deployed to a Kubernetes cluster.
Repository: A location where Helm charts are stored and shared (e.g., Helm Hub, Artifact Hub).
Helm Client: The command-line tool used to interact with Helm charts and repositories.

2. Installing Helm
Before using Helm to deploy packages, you need to install it on your local machine. Here's how you can install Helm:
Install Helm (on macOS/Linux):
# On macOS using Homebrew
brew install helm

# On Linux (via script)
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

Install Helm (on Windows):
You can use Chocolatey or Windows Subsystem for Linux (WSL) to install Helm on Windows:
choco install kubernetes-helm


3. Helm Repositories
Helm charts are stored in repositories, which are collections of Helm charts. By default, Helm includes the Helm Hub repository, but you can add your own or use third-party repositories.
Add a Helm Repository:
To add a new repository (e.g., the official stable repository), use the following command:
helm repo add stable https://charts.helm.sh/stable

Once added, you can update your local Helm chart repository cache:
helm repo update


4. Deploying Existing Helm Charts
To deploy an existing Helm chart to your Kubernetes cluster, you can follow these steps. We'll use the nginx-ingress chart from the official Helm repository as an example.
Step-by-Step:
Search for Helm Charts: To search for a chart in a Helm repository, use the helm search command:

 helm search repo nginx-ingress
 This command will return a list of charts related to nginx-ingress available in your Helm repositories.


Install the Helm Chart:

 To deploy the chart, use the helm install command followed by the release name (an identifier for the deployed application) and the name of the chart.

 helm install my-ingress stable/nginx-ingress
 In this example:


my-ingress is the release name.
stable/nginx-ingress is the Helm chart you're installing from the stable repository.
Helm will automatically create the necessary Kubernetes resources (Deployments, Services, ConfigMaps, etc.) based on the chart and deploy them to your Kubernetes cluster.


Verify the Deployment:

 After installation, you can check the status of your release:

 helm list
 This will show you all the releases installed in your cluster.

 You can also check the individual Kubernetes resources created by the chart, such as Pods and Services:

 kubectl get pods
kubectl get services


Check the Resources Created by the Helm Chart:

 To view the Kubernetes resources created by Helm, use the following command:

 helm get all my-ingress
 This will show you detailed information about the release, including the manifests of the deployed resources.



5. Customizing the Helm Chart During Deployment
Helm allows you to customize the deployment of a Helm chart by overriding its default values using a values.yaml file or command-line flags.
Using --set Flag:
You can modify specific values directly through the --set flag:
helm install my-ingress stable/nginx-ingress --set controller.replicaCount=3

This will override the default replica count to 3 instead of the default value specified in the chart.
Using a Custom values.yaml File:
Alternatively, you can create a custom values.yaml file and provide it during installation:
controller:
  replicaCount: 3
  service:
    annotations:
      service.beta.kubernetes.io/aws-load-balancer-type: nlb

Then, install the chart with your custom values:
helm install my-ingress stable/nginx-ingress -f custom-values.yaml

This approach is useful when you need to provide more complex configuration changes.

6. Upgrading an Existing Release
If you want to upgrade an existing Helm release (e.g., to a new version of the chart or with new custom values), you can use the helm upgrade command:
helm upgrade my-ingress stable/nginx-ingress --set controller.replicaCount=5

This will update the my-ingress release with the new chart version and custom values.

7. Rolling Back a Release
If an upgrade or configuration change causes issues, you can roll back to a previous version of the release using the helm rollback command:
helm rollback my-ingress 1

In this example, 1 refers to the revision number of the release. You can view all revisions of a release with:
helm history my-ingress

This will show you the history of changes made to the release, including the revision numbers.

8. Uninstalling a Helm Release
To remove a Helm release, you can use the helm uninstall command:
helm uninstall my-ingress

This will delete all Kubernetes resources associated with the my-ingress release.

9. Benefits of Using Helm for Deployments
Reusability: Helm charts allow you to easily reuse application templates across multiple projects or clusters.
Simplified Management: Helm packages and manages complex Kubernetes applications, making it easier to handle updates, rollbacks, and configuration management.
Versioning: Helm supports versioned releases, making it easy to track and roll back application changes.
Customization: Helm allows you to easily customize application configurations without modifying the underlying YAML files manually.

10. Conclusion
Using Helm to deploy existing packages is an essential skill for a Kubernetes Application Developer (CKAD). It simplifies application deployment, management, and upgrades, especially for complex applications with multiple Kubernetes resources.
By mastering Helm, you can:
Easily deploy pre-configured Kubernetes applications (Helm charts).
Customize and manage your deployments using values files or command-line overrides.
Simplify updates and rollbacks using Helm's release management capabilities.
With Helm, you can focus on building and developing applications rather than worrying about the complexity of Kubernetes YAML files.
