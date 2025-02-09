CKAD: Demonstrate Basic Understanding of NetworkPolicies
NetworkPolicies in Kubernetes are used to control the communication between Pods. They define the rules and policies that control how groups of Pods are allowed to communicate with each other and with other network endpoints outside the Kubernetes cluster. By defining NetworkPolicies, you can restrict traffic to and from Pods based on various factors, such as namespace, IP address, and labels.
In this section, we’ll cover the basics of NetworkPolicies:
What NetworkPolicies are
How they work in Kubernetes
How to define and apply NetworkPolicies
Common use cases and best practices for NetworkPolicies

1. What are NetworkPolicies?
A NetworkPolicy in Kubernetes is an API object that defines a set of rules governing how Pods communicate with each other and with external services. By default, Pods are open to communication from any other Pod within the same namespace, and they can initiate connections to external services. However, NetworkPolicies allow you to restrict this communication to only the Pods and external services you specify.
NetworkPolicies are enforced by network plugins that support the NetworkPolicy API, such as Calico, Cilium, or Weave.

2. How Do NetworkPolicies Work?
NetworkPolicies work by specifying ingress (incoming traffic) and egress (outgoing traffic) rules for Pods. These rules can specify:
Selectors for Pods (e.g., by labels)
Ports that are open or closed for communication
Namespaces that are allowed or disallowed
IP Blocks that control access to or from specific IP ranges
By default, if a NetworkPolicy is not applied, Pods are open to any communication. Once a NetworkPolicy is applied, it acts as a firewall, allowing only traffic that matches the rules defined in the policy and rejecting all other traffic.

3. Key Concepts
Ingress and Egress:
Ingress refers to inbound traffic to a Pod.
Egress refers to outbound traffic from a Pod.
You can define rules for both ingress and egress to control the flow of traffic into and out of a Pod.
Pod Selectors:
Pod selectors are used to identify which Pods the policy applies to. This can be based on labels assigned to Pods.
Policy Types:
You can define the ingress and/or egress rules in a NetworkPolicy:
Ingress: Defines what inbound traffic is allowed.
Egress: Defines what outbound traffic is allowed.
Namespace Selectors:
You can use namespace selectors to control communication between Pods in different namespaces.

4. Basic Example of a NetworkPolicy
Let’s look at a simple example of a NetworkPolicy that restricts inbound traffic to a Pod:
NetworkPolicy Example: Allow HTTP Inbound Traffic
In this example, we will create a NetworkPolicy to allow only HTTP traffic (port 80) from other Pods in the same namespace.
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-http-ingress
spec:
  podSelector:
    matchLabels:
      app: my-app
  ingress:
  - ports:
    - protocol: TCP
      port: 80
  policyTypes:
  - Ingress

Explanation:
podSelector: This policy applies to Pods with the label app=my-app.
ingress: Specifies that only traffic on port 80 (HTTP) is allowed.
policyTypes: The policy is applied to ingress traffic (incoming traffic).
By applying this NetworkPolicy, Pods labeled app=my-app will only be able to accept traffic on port 80 (HTTP). Any other traffic is blocked.

5. Egress Example: Allowing Outbound Traffic
Next, let’s look at a NetworkPolicy that allows a Pod to send outbound traffic only to a specific IP range.
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-egress-to-db
spec:
  podSelector:
    matchLabels:
      app: my-app
  egress:
  - to:
    - ipBlock:
        cidr: 192.168.0.0/16
  policyTypes:
  - Egress

Explanation:
podSelector: This policy applies to Pods with the label app=my-app.
egress: Specifies that traffic from the Pod is allowed only to the IP range 192.168.0.0/16.
policyTypes: The policy is applied to egress traffic (outgoing traffic).
This policy ensures that Pods labeled app=my-app can only initiate connections to IPs within the range 192.168.0.0/16. Any other outbound traffic will be blocked.

6. Combining Ingress and Egress Policies
You can combine both ingress and egress rules in a single NetworkPolicy to control both inbound and outbound traffic for a set of Pods.
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-http-and-db-traffic
spec:
  podSelector:
    matchLabels:
      app: my-app
  ingress:
  - ports:
    - protocol: TCP
      port: 80
  egress:
  - to:
    - ipBlock:
        cidr: 192.168.0.0/16
  policyTypes:
  - Ingress
  - Egress

This policy:
Allows HTTP inbound traffic on port 80 (ingress).
Allows outbound traffic to the 192.168.0.0/16 IP range (egress).

7. Default Deny Policy
A default deny policy can be created by not defining any ingress or egress rules in the policy. This implicitly denies all traffic by default. You can create a NetworkPolicy that denies all traffic to Pods by not specifying any rules.
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-traffic
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress

Explanation:
The empty podSelector: {} applies the policy to all Pods in the namespace.
No ingress or egress rules are defined, meaning all traffic (both inbound and outbound) is blocked.
This policy effectively blocks all traffic to and from the Pods unless other NetworkPolicies allow specific traffic.

8. Best Practices for NetworkPolicies
Start with a Default Deny: Begin by creating a default deny policy to block all traffic. Then, incrementally allow only the traffic that is required.
Apply NetworkPolicies Early: Apply policies early in the deployment cycle to avoid security holes.
Label Pods Consistently: Use consistent labels on Pods to easily manage and target them with NetworkPolicies.
Keep NetworkPolicies Simple: Start with simple, clear rules. Complex policies can be harder to debug and maintain.
Use Namespace Selectors: For cross-namespace communication, use namespaceSelector to control which namespaces can communicate with each other.

9. Conclusion
NetworkPolicies are a critical tool in securing Kubernetes clusters by controlling traffic flow between Pods and other network endpoints. They enable you to implement the least privilege model by restricting unnecessary communication and reducing potential attack surfaces. By defining ingress and egress rules, you can manage communication between Pods, external services, and the network.
Key takeaways:
NetworkPolicies control traffic flow between Pods.
PodSelectors and NamespaceSelectors define which Pods are affected.
You can control both ingress (incoming) and egress (outgoing) traffic.
Use NetworkPolicies to minimize the attack surface and ensure secure communication.
