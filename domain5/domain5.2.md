CKAD: Provide and Troubleshoot Access to Applications via Services
In Kubernetes, Services are an abstraction that defines how to expose and access Pods in a network. Services provide a stable IP address or DNS name for accessing Pods, abstracting the complexity of dynamically changing Pod IP addresses as Pods are created, destroyed, or rescheduled across nodes.
In this section, we'll cover:
How to create and configure Services in Kubernetes.
How to troubleshoot Service access issues.
Common Service types and their use cases.
Key concepts and tools used in diagnosing Service-related problems.

1. Overview of Kubernetes Services
Kubernetes Services expose Pods to network traffic and are essential for providing reliable, stable access to applications. By default, Pods are ephemeral (i.e., they can be created and destroyed dynamically), so Services provide an abstraction that helps ensure communication with Pods, even if their IPs change.
Types of Services:
There are four main types of Kubernetes Services:
ClusterIP (default): Exposes the service on a cluster-internal IP. This makes the Service accessible only from within the cluster.
NodePort: Exposes the service on a static port on each node's IP. This allows access to the Service from outside the cluster.
LoadBalancer: Provisions a load balancer (if supported by the cloud provider) to expose the Service externally.
ExternalName: Maps the service to an external DNS name, allowing Kubernetes resources to access external services using a DNS alias.
Key Concepts:
ClusterIP is the most common type and is used when the service does not need to be accessed externally.
Endpoints are the set of Pods that are selected by a Service's label selector.

2. Creating a Kubernetes Service
Here’s an example of how to create a Service in Kubernetes.
Service Example: Exposing an Application Using ClusterIP
This YAML file creates a Service of type ClusterIP that exposes a Pod running an HTTP server.
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  clusterIP: None

Explanation:
selector: Selects the Pods labeled app=my-app to associate with the Service.
ports: The Service exposes port 80 and forwards traffic to Pods on port 8080.
clusterIP: None: Specifies that the Service does not have a specific ClusterIP, which makes it headless. (This is useful when using StatefulSets.)
Service Example: Exposing an Application Using NodePort
A NodePort service exposes your application outside the Kubernetes cluster. Below is an example of a NodePort service:
apiVersion: v1
kind: Service
metadata:
  name: my-app-nodeport
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30007
  type: NodePort

Explanation:
nodePort: 30007: The service will be accessible externally on port 30007 on every node in the cluster.
type: NodePort: This type exposes the service on a static port across all nodes in the cluster.

3. Troubleshooting Service Access
Sometimes, access to services in Kubernetes may not work as expected. There are several common reasons for Service-related issues. Here are steps to troubleshoot these problems:
Common Issues:
Service not accessible externally: The most common reason for this is that the service type is incorrectly set or the NodePort or LoadBalancer is not properly configured.
Pods not selected by the Service: The Service's label selector may not match the Pods you want to expose.
Service not routing traffic to Pods: Misconfigured targetPort or issues with the underlying Endpoints can result in traffic not reaching the Pods.
DNS issues: If the application cannot be accessed using the Service name, there may be issues with DNS resolution inside the cluster.
Troubleshooting Steps:
1. Check the Service Configuration
Verify that the Service is created and configured correctly.
kubectl get svc my-app-service

Check if the Service is of the correct type (ClusterIP, NodePort, LoadBalancer) and if the correct ports are exposed.
2. Check Service Endpoints
A Service relies on Endpoints to route traffic to Pods. If no Endpoints are associated with the Service, traffic won’t reach the Pods.
kubectl get endpoints my-app-service

If no Endpoints are listed, check if the Pod label selector is correct and whether Pods matching the selector are running.
3. Verify Pod Labels and Selectors
Ensure that the labels of the Pods match the Service’s label selector.
kubectl get pods --show-labels

For the Service to work correctly, the Pods should have labels that match the selector in the Service definition.
4. Check Pod Logs
If the Pods are selected correctly but the service still doesn’t respond, check the Pod logs to ensure the application inside the Pods is functioning correctly.
kubectl logs <pod-name>

Check if there are any issues with the application that might be preventing it from accepting traffic on the specified port.
5. Verify Network Connectivity
You can use the kubectl exec command to troubleshoot network connectivity to the Pods from other Pods.
kubectl exec -it <pod-name> -- curl <service-name>:<service-port>

This command tests whether a Pod can reach the Service and port. This is useful for diagnosing network issues between Pods.
6. Investigate NodePort and LoadBalancer Services
If using a NodePort or LoadBalancer service, check that the service is exposed properly.
NodePort: Ensure that the port is accessible on all the nodes in the cluster.
kubectl get svc my-app-nodeport

LoadBalancer: If using a LoadBalancer service, verify if the cloud provider has provisioned the external load balancer and that the IP is accessible.
kubectl describe svc my-app-lb

Check if the EXTERNAL-IP is listed and whether it’s reachable from outside the cluster.
7. DNS Resolution
If the application is not reachable by its Service name, ensure that the DNS service in Kubernetes is running correctly.
You can check if DNS resolution is working properly by querying the DNS from within a Pod:
kubectl exec -it <pod-name> -- nslookup my-app-service


4. Best Practices for Exposing Applications via Services
Use the Correct Service Type:


Use ClusterIP for internal applications.
Use NodePort or LoadBalancer for external access.
ExternalName is useful for redirecting traffic to external services via DNS.
Match Labels Correctly: Ensure that the label selectors on the Service match the labels on the Pods to ensure correct routing of traffic.


Health Checks and Probes: Always implement readiness and liveness probes in your Pods. These help ensure that Pods are ready to handle traffic before the Service routes requests to them.


Limit Port Exposure: Only expose the necessary ports for each application. This minimizes the attack surface and limits the potential for unauthorized access.


Use Namespace Isolation: If your application spans multiple namespaces, ensure that the appropriate namespace selectors are used in the Service definition.



5. Conclusion
Providing and troubleshooting access to applications in Kubernetes using Services is a fundamental part of managing Kubernetes environments. Services provide a stable interface to access Pods, abstracting away the complexities of pod IP address management. By understanding the types of services available and troubleshooting common issues, you can ensure that your applications are reliably exposed to the network and that access problems can be quickly diagnosed and fixed.
Key takeaways:
Services expose Pods and enable stable access to applications.
Use the correct Service type based on your use case (ClusterIP, NodePort, LoadBalancer, ExternalName).
Troubleshoot by verifying Service configurations, labels, endpoints, and Pod health.
Leverage DNS and network debugging tools to identify connectivity issues.
