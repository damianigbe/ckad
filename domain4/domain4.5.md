CKAD: Define Resource Requirements
As a Kubernetes Application Developer (CKAD), understanding how to define resource requirements for your applications is essential. Kubernetes provides a powerful way to manage the allocation of computing resources such as CPU and memory to Pods running in your cluster. By setting resource requirements, you can ensure that your applications run efficiently without consuming excessive resources or starving other applications.
In this section, we'll discuss how to define resource requests and limits, and the best practices for using them effectively.

1. Resource Requests and Limits Overview
In Kubernetes, resources are the computing power available to Pods. The primary resources are:
CPU
Memory (RAM)
You can define requests and limits for each resource to control how much CPU and memory your Pods are allocated and how they behave under pressure.
Resource Requests: The amount of CPU or memory that a container requests. This is the amount Kubernetes will guarantee for the container when scheduling it on a node.
Resource Limits: The maximum amount of CPU or memory that a container can use. If the container exceeds this limit, Kubernetes will throttle the CPU usage or kill the container if it exceeds the memory limit.

2. Defining Resource Requests and Limits in YAML
Resource requests and limits are defined in the container specification within a Pod manifest (usually written in YAML). Here’s how you can define them:
a) Example YAML with Resource Requests and Limits
apiVersion: v1
kind: Pod
metadata:
  name: resource-example
spec:
  containers:
  - name: my-container
    image: my-image
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"

In this example:
requests:
The container requests 64 MiB of memory and 250 milliCPU (m) at the time of scheduling.
limits:
The container is allowed to use up to 128 MiB of memory and 500 milliCPU (m) if needed, but will be throttled or killed if it exceeds these limits.
b) CPU and Memory Units
Kubernetes supports the following units for specifying resource requests and limits:
CPU:
1 CPU = 1 physical CPU core.
m (milli) units are used to specify fractional CPU values. For example, 500m means half of a CPU core.
Examples: cpu: "250m", cpu: "1" (full core), cpu: "0.5" (half core).
Memory:
Memory can be specified in MiB or GiB (Mebibytes and Gibibytes) or MB and GB (Megabytes and Gigabytes).
Examples: memory: "64Mi", memory: "128Mi", memory: "2Gi".

3. Understanding How Requests and Limits Work
a) Resource Requests
Scheduling: Kubernetes uses the resource requests to decide where to place Pods. The request is a guarantee that the Pod will have that amount of CPU and memory available on the node. If the requested resources can't fit on a node, the Pod won’t be scheduled.


Efficient Utilization: The request ensures that the Pod gets a fair share of the node’s resources without monopolizing it.


b) Resource Limits
Container Behavior:
CPU Limits: If a container exceeds its CPU limit, Kubernetes will throttle its CPU usage, preventing it from using more than the allocated amount.
Memory Limits: If a container exceeds its memory limit, Kubernetes will terminate the container (OOM - Out Of Memory) and restart it. The container will be killed and potentially rescheduled depending on the restart policy.
c) No Resource Requests or Limits
If you don’t specify resource requests or limits:
The Pod will be scheduled on any available node, and it can consume unlimited CPU or memory resources.
This can lead to inefficient resource utilization and potential resource starvation for other applications in the cluster.

4. Why Are Resource Requests and Limits Important?
Defining proper resource requests and limits is crucial for the following reasons:
a) Resource Isolation
By setting limits, you ensure that your applications do not consume excessive resources and starve other workloads running on the same cluster. Requests help Kubernetes make smarter decisions on where to place Pods in the cluster.
b) Cluster Efficiency
Proper resource requests allow Kubernetes to schedule workloads efficiently and optimize resource utilization across the cluster. If every application has reasonable requests, Kubernetes can better balance the workloads across nodes.
c) Preventing Resource Exhaustion
Without limits, applications can exhaust the resources available on a node, causing instability. For example, if a container starts consuming all available memory on a node, it could cause other Pods to be evicted or even bring down the node.
d) Predictable Application Behavior
Resource limits allow you to control the maximum resource usage of your application, making it more predictable and manageable, especially when running multiple applications in a shared environment.

5. Best Practices for Defining Resource Requests and Limits
a) Start with Reasonable Defaults
If you’re unsure about the exact resource requirements, start with a small request and a reasonable limit, and adjust them based on your application’s behavior.
b) Monitor Resource Usage
Use Kubernetes monitoring tools (like Prometheus or Kubernetes Dashboard) to track the resource usage of your applications. This helps you identify bottlenecks and adjust resource requests and limits accordingly.
c) Set Memory Limits to Prevent OOMs
Always set memory limits to prevent your containers from consuming too much memory and crashing (OOM killed). This is especially critical for applications that may have varying memory usage patterns.
d) Use Vertical Pod Autoscaler (VPA) and Horizontal Pod Autoscaler (HPA)
While resource requests and limits are important, you can also make use of Kubernetes autoscaling features to automatically adjust resource requirements:
Horizontal Pod Autoscaler (HPA): Automatically scale the number of Pods based on CPU or memory usage.
Vertical Pod Autoscaler (VPA): Automatically adjust the resource requests and limits of your Pods based on usage patterns.
e) Avoid Overcommitting Resources
Be cautious of overcommitting resources, especially CPU. Kubernetes will not allow you to schedule Pods that require more resources than are available. Try to avoid overcommitting resources to ensure efficient cluster operation.
f) Use Requests to Guarantee Resources, Limits to Control Maximum Usage
Use requests to guarantee the resources that your container needs to function properly.
Use limits to cap the maximum resources to avoid overloading the system.

6. Example of a Deployment with Resource Requests and Limits
Here’s an example of how you would define a Deployment with resource requests and limits:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: resource-demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: resource-demo
  template:
    metadata:
      labels:
        app: resource-demo
    spec:
      containers:
      - name: my-container
        image: my-image
        resources:
          requests:
            memory: "128Mi"
            cpu: "500m"
          limits:
            memory: "256Mi"
            cpu: "1000m"

In this example:
Each container requests 128Mi of memory and 500m (half a CPU core) when scheduling the Pod.
The container can use up to 256Mi of memory and 1000m (1 CPU core), but it will be throttled or killed if it exceeds these limits.

7. Conclusion
Defining resource requests and limits in Kubernetes is a vital skill for a Kubernetes Application Developer (CKAD). Proper resource allocation helps ensure that your applications run efficiently, stay within predictable resource usage, and do not interfere with other workloads in the cluster.
To recap:
Requests: Guarantee the resources that the container will get when it is scheduled.
Limits: Cap the maximum resources a container can consume.
Always monitor and adjust resource settings based on your application's behavior and needs.
By following best practices for defining resource requirements, you can improve the performance, stability, and scalability of your applications in a Kubernetes cluster.
