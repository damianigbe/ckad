CKAD: Understand Requests, Limits, and Quotas
In Kubernetes, managing resources effectively is critical for ensuring the stability, performance, and efficiency of applications running in the cluster. As a Kubernetes Application Developer (CKAD), it is essential to understand resource requests, limits, and quotas, as these directly influence how workloads (Pods and containers) are scheduled, executed, and constrained within the cluster.
This section will cover the core concepts related to resource management and demonstrate how you can define and configure resource requests, limits, and quotas for containers and Pods to ensure that your applications are properly allocated resources.

1. Resource Requests and Limits
In Kubernetes, resources like CPU and memory (RAM) are allocated to containers within a Pod. Each container in a Pod can specify requests and limits for these resources.
a) Resource Requests
A request is the amount of a particular resource that the container is guaranteed to receive when it is scheduled onto a node. When a container is scheduled, Kubernetes ensures that enough resources are available on the node to meet the requested amount.
CPU Requests: The amount of CPU requested by the container. Kubernetes will schedule the container onto a node only if that node has enough available CPU.
Memory Requests: The amount of memory the container requests. Similarly, the container will only be scheduled onto a node if there is sufficient memory available.
Example:
apiVersion: v1
kind: Pod
metadata:
  name: resource-request-example
spec:
  containers:
  - name: my-container
    image: my-image
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"

In this example, the container requests 64 MB of memory and 250 milli-CPU (0.25 CPU core).
b) Resource Limits
A limit is the maximum amount of a resource that a container can use. If a container tries to exceed its specified limit, Kubernetes may throttle the container’s CPU usage or kill the container if it exceeds its memory limit (and it will be restarted, depending on the restart policy).
CPU Limits: If a container tries to exceed the CPU limit, Kubernetes will throttle the container’s CPU usage to prevent it from consuming too much.
Memory Limits: If a container exceeds its memory limit, Kubernetes will terminate the container and restart it (OOMKill), as memory consumption is a critical resource for the node.
Example:
apiVersion: v1
kind: Pod
metadata:
  name: resource-limit-example
spec:
  containers:
  - name: my-container
    image: my-image
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"

In this example, the container can use up to 128 MB of memory and 500 milli-CPU (0.5 CPU core), but it is not allowed to consume more than that.
c) Requests vs. Limits:
Requests ensure that the container has the required resources to run.
Limits ensure that the container cannot use more resources than the specified maximum, preventing one container from starving other containers on the same node.
Important: If you don't specify limits, containers can use as much resource as is available on the node, which could lead to resource contention or instability.

2. Resource Quotas
Resource Quotas are a way to limit the amount of resources that can be consumed by a specific namespace within a Kubernetes cluster. This allows administrators to enforce policies that prevent one team or application from consuming all the available resources in the cluster.
A ResourceQuota object can define limits on resources like CPU, memory, number of Pods, number of Persistent Volume Claims (PVCs), and more.
a) Common ResourceQuota Constraints
CPU: Limits the total CPU usage in the namespace.
Memory: Limits the total memory usage in the namespace.
Pods: Limits the number of Pods that can be created within the namespace.
PersistentVolumeClaims: Limits the number of Persistent Volume Claims that can be created.
b) Example of a ResourceQuota
apiVersion: v1
kind: ResourceQuota
metadata:
  name: example-quota
  namespace: my-namespace
spec:
  hard:
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "8"
    limits.memory: "16Gi"
    pods: "10"
    persistentvolumeclaims: "5"

In this example:
The namespace my-namespace can request up to 4 CPUs and 8 GiB of memory in total.
The namespace is allowed to create up to 10 Pods and 5 PersistentVolumeClaims.
The maximum limits for CPU and memory are set at 8 CPUs and 16 GiB of memory.
c) ResourceQuota and Namespace Limits
When a ResourceQuota is defined for a namespace, Kubernetes will enforce the limits on the total resource usage for that namespace. This means if you exceed the quota, Kubernetes will prevent new resources from being created until resources are freed up or the quota is adjusted.

3. Best Practices for Resource Requests, Limits, and Quotas
As a Kubernetes Application Developer, understanding how to configure requests, limits, and quotas is essential for the stability and efficiency of your applications. Here are some best practices:
a) Always Define Requests and Limits
Always specify both requests and limits for containers. If only one is defined (e.g., only a request but no limit), it can lead to unexpected behaviors, such as excessive resource consumption or throttling.
Requests should reflect the minimum resources necessary for your application to run, while limits should reflect the maximum acceptable resources your application should consume.
b) Monitor Resource Usage
Continuously monitor your containers and Pods for resource usage to ensure that you are not over- or under-allocating resources.
Use Kubernetes tools like kubectl top to view resource usage in your cluster.
Example:
kubectl top pod

c) Optimize Resource Requests
Setting resource requests too high can lead to overprovisioning, meaning your cluster may run out of resources. On the other hand, setting requests too low may lead to performance issues.
Use monitoring tools like Prometheus to track resource usage and adjust requests and limits accordingly.
d) Use Resource Quotas to Control Resource Usage
Enforce ResourceQuotas to ensure that no single team or application uses more than its fair share of resources.
For multi-tenant clusters, use ResourceQuotas to avoid resource contention between teams or applications.

4. How to View and Manage Resources
a) View Resources in the Cluster
You can check the resource requests and limits of containers and Pods using kubectl describe:
kubectl describe pod <pod-name>

This command will provide detailed information about the resource requests and limits for each container in the Pod.
b) View Resource Quotas in a Namespace
You can view the existing ResourceQuota objects in a specific namespace using the following command:
kubectl get resourcequota -n <namespace>

This will display the resource quotas in the specified namespace, along with the usage and available resources.

5. Troubleshooting Resource Issues
When resource issues arise, such as Pods being OOMKilled (Out of Memory) or Pods being throttled due to CPU limits, you can troubleshoot these problems with the following steps:
a) Check Resource Usage
Use kubectl top pod to monitor how much CPU and memory your Pods are using.
kubectl top pod

b) Check Pod Events
If Pods are being killed due to resource constraints, you can inspect their events to get more details:
kubectl describe pod <pod-name>

Look for events such as "OOMKilled" or "Memory Pressure".
c) Adjust Resource Requests and Limits
If your application is resource-intensive, consider increasing the resource requests and limits. Conversely, if your application is using fewer resources than expected, you can scale back the requests and limits.

6. Conclusion
Understanding resource requests, limits, and quotas is crucial for maintaining a healthy, efficient, and stable Kubernetes environment. As a Kubernetes Application Developer (CKAD), your role is to ensure that resources are allocated properly to your workloads, avoiding over- or under-provisioning.
To summarize:
Requests: The minimum amount of resources that a container is guaranteed to receive.
Limits: The maximum amount of resources a container can use.
Resource Quotas: Limits on resources within a namespace to prevent one team or workload from consuming excessive resources.
By carefully managing these resources, you help ensure that Kubernetes clusters remain efficient, responsive, and predictable, while also preventing potential issues related to resource contention.
