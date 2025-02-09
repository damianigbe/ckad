CKAD: Use Built-in CLI Tools to Monitor Kubernetes Applications
As a Kubernetes Application Developer (CKAD), it's essential to be able to monitor the applications you deploy on Kubernetes. Monitoring helps you track the health, performance, and availability of applications, and identify any issues in real time. Kubernetes provides built-in CLI tools that can help you monitor applications and gain insight into the status of your resources.
In this section, we’ll focus on some of the most commonly used Kubernetes CLI tools and commands for monitoring your applications and how to use them effectively.

1. kubectl - The Kubernetes Command-Line Interface
The kubectl CLI is the primary tool for interacting with Kubernetes clusters. It can be used to inspect and monitor various components of the cluster and applications running in it.

2. Common kubectl Commands for Monitoring Kubernetes Applications
1. Checking Pod Status
You can use kubectl to get information about your Pods, such as their current state, events, and resource usage. This is useful for monitoring the health and status of the applications within your Pods.
View all Pods in a namespace:

 kubectl get pods


Use kubectl get pods -n <namespace> to get Pods from a specific namespace.
Use kubectl get pods -o wide to show additional details like the node the pod is running on.
View detailed information about a specific Pod:

 kubectl describe pod <pod-name>
 This command gives you detailed information about the Pod, including events, container status, resource usage, and more.


View the logs of a specific Pod:

 kubectl logs <pod-name>


Use -f to stream the logs in real time:
 kubectl logs -f <pod-name>


View logs for all containers in a Pod (if there are multiple containers):

 kubectl logs <pod-name> -c <container-name>



2. Monitoring Deployments
A Deployment is a higher-level object in Kubernetes used to manage the lifecycle of Pods. You can use kubectl to monitor the status of Deployments.
View all Deployments:

 kubectl get deployments


Get detailed information about a specific Deployment:

 kubectl describe deployment <deployment-name>


This provides insights into the Deployment's status, including the number of replicas, pods being used, and any ongoing issues.
Check the rollout status of a Deployment:

 kubectl rollout status deployment/<deployment-name>


This command helps you track the progress of a rolling update and verify that the deployment is working as expected.
Undo a Deployment rollout:

 If something goes wrong, you can rollback to the previous version:

 kubectl rollout undo deployment/<deployment-name>



3. Monitoring Services
A Service in Kubernetes exposes your application to the network. Monitoring Services helps ensure that your application is accessible and traffic is being routed correctly.
View all Services in a namespace:

 kubectl get services


Get detailed information about a specific Service:

 kubectl describe service <service-name>


This will show details about the service, such as the IP address, ports, and which Pods are being targeted.

4. Monitoring Namespaces
Namespaces are a way to partition resources in a Kubernetes cluster. Monitoring namespaces helps you get a clearer understanding of resource allocation and application isolation.
List all namespaces:

 kubectl get namespaces


Get resources (Pods, Services, Deployments) within a specific namespace:

 kubectl get all -n <namespace>



5. Monitoring Resource Usage
Kubernetes also provides resource metrics, such as CPU and memory usage, to help you monitor how efficiently your applications are running.
View resource usage for Pods:

 To use this command, you need to have the metrics-server installed in your cluster.

 kubectl top pod
 This command shows the current CPU and memory usage for all Pods in the default namespace. You can also specify a namespace:

 kubectl top pod -n <namespace>


View resource usage for Nodes:

 kubectl top node
 This command shows the CPU and memory usage for each node in the cluster.



6. Monitoring Events
Kubernetes logs events that provide insights into what's happening in the cluster, such as Pod failures, resource allocation issues, or deployment status changes.
View all events in a namespace:

 kubectl get events -n <namespace>
 This command shows all events in a specific namespace. Use it to track errors, warnings, or changes to your application or resources.


View events in real time:

 kubectl get events --watch
 This allows you to stream events as they occur in your cluster, which is helpful for troubleshooting live issues.



3. Monitoring with kubectl Plugins
Kubernetes supports the use of plugins that can extend the functionality of kubectl. Some plugins are designed specifically for monitoring and troubleshooting.
kube-ps1: Provides Kubernetes context information in your shell prompt.
kubectl-gotmpl: Allows you to use Go templating for more flexible queries and outputs.
kubectl-debug: Provides an easy way to debug running Pods by creating a temporary container.
You can install these plugins to enhance your Kubernetes monitoring experience.

4. Troubleshooting Application Failures with kubectl
When things go wrong, you can use kubectl to troubleshoot the issue.
Inspect the state of Pods:

 kubectl get pods -o wide
 This command shows which node the Pods are running on and helps you diagnose if there is a resource issue (e.g., resource limits, node failures).


Describe the Pod to get detailed information about the error:

 kubectl describe pod <pod-name>
 This provides detailed event logs and error messages for the container and helps you identify why the Pod might not be running properly.


Access a shell inside a running Pod (for debugging purposes):

 kubectl exec -it <pod-name> -- /bin/sh
 This allows you to interact with the application running inside the container and inspect logs, files, or processes.



5. Leveraging Labels and Selectors
Kubernetes allows you to use labels and selectors to filter resources in your cluster. Labels can be added to Pods, Services, and other resources to group them based on common attributes.
Get Pods with a specific label:

 kubectl get pods -l app=<label-name>


View resources by label in a specific namespace:

 kubectl get all -l app=<label-name> -n <namespace>


Using labels and selectors is helpful when you need to monitor or troubleshoot a subset of resources in a large cluster.

6. Conclusion
As a Kubernetes Application Developer (CKAD), being proficient with kubectl is essential for monitoring the health, performance, and availability of your applications in a Kubernetes cluster. By using the built-in CLI tools, you can quickly gain insight into the state of your Pods, Deployments, Services, and other resources, allowing you to troubleshoot and optimize your applications effectively.
Some key commands to remember:
kubectl get — View resources (Pods, Deployments, Services, etc.).
kubectl describe — Get detailed resource information and events.
kubectl logs — View application logs.
kubectl top — Monitor resource usage.
kubectl exec — Access running Pods for debugging.
Mastering these tools will empower you to ensure your applications are running smoothly in your Kubernetes environment, enabling you to detect and resolve issues quickly.
