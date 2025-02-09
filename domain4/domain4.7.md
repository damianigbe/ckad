CKAD: Understand Service Accounts
In Kubernetes, Service Accounts are used to provide an identity for processes that run in Pods. When applications in Pods need to interact with the Kubernetes API or other resources within the cluster, they use a ServiceAccount to authenticate and authorize themselves. Each ServiceAccount is associated with a set of API access credentials, typically tokens, that the system uses to authenticate requests made by Pods.
Understanding Service Accounts is crucial for building secure Kubernetes applications, as they define how Pods interact with the Kubernetes API and other resources in the cluster.
In this section, we will cover the following topics:
What Service Accounts are and why they are important.
How to create and configure Service Accounts.
How to associate Service Accounts with Pods.
Best practices for using Service Accounts securely.

1. What is a Kubernetes Service Account?
A ServiceAccount is a Kubernetes resource that provides an identity for a Pod and is used to authenticate and authorize API requests on behalf of the Pod. It’s essentially a "service user" that can be assigned specific permissions (via RBAC policies) to interact with other resources within the Kubernetes cluster.
Key Points:
Service Accounts are tied to namespaces. Each namespace can have its own set of Service Accounts.
A ServiceAccount is associated with an automatically generated ServiceAccount Token (JWT) that is used for authentication.
Service Accounts can be configured to interact with the Kubernetes API, make calls to other services, or access secrets.

2. Why are Service Accounts Important?
Service Accounts provide a way to manage the security and identity of applications running in your Kubernetes clusters. They help in:
Access Control: Service Accounts are used with Role-Based Access Control (RBAC) to define what a Pod can and cannot do in the Kubernetes environment.
Isolation and Least Privilege: By using distinct Service Accounts for different types of workloads, you can minimize the risk of exposing sensitive data or giving unnecessary permissions.
Automation: Service Accounts are key for applications that need to automatically interact with the Kubernetes API or other resources in the cluster, such as creating Pods, accessing secrets, etc.

3. Default Service Account
Every Kubernetes namespace automatically has a default ServiceAccount. When you create a Pod and don't explicitly specify a ServiceAccount, Kubernetes automatically assigns the default ServiceAccount of the namespace to the Pod.
Example of Using Default Service Account
If you create a Pod without specifying a ServiceAccount, it will use the default one:
apiVersion: v1
kind: Pod
metadata:
  name: default-service-account-pod
spec:
  containers:
  - name: my-container
    image: my-image

This Pod will automatically use the default ServiceAccount from the same namespace.

4. Creating and Using Service Accounts
a) Creating a Service Account
You can create a Service Account using a simple YAML manifest or the kubectl command. Here's an example of a ServiceAccount YAML manifest:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account

To create the ServiceAccount:
kubectl apply -f service-account.yaml

Alternatively, you can create a ServiceAccount directly via kubectl:
kubectl create serviceaccount my-service-account


b) Associating a Service Account with a Pod
Once a ServiceAccount is created, you can associate it with a Pod by specifying it in the Pod’s serviceAccountName field:
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-service-account
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: my-image

This Pod will use the my-service-account ServiceAccount for authentication and authorization when interacting with the Kubernetes API.
If you don’t explicitly set the serviceAccountName, the Pod will use the default ServiceAccount in the namespace.

5. Service Account Tokens
When a Pod uses a ServiceAccount, Kubernetes automatically creates a ServiceAccount Token. This token is used by the Pod to authenticate with the Kubernetes API.
Kubernetes automatically mounts the token as a file inside the Pod, typically at /var/run/secrets/kubernetes.io/serviceaccount/token. The token is in JWT (JSON Web Token) format and can be used for authenticating API requests.
Example: Accessing the ServiceAccount Token Inside a Pod
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-token
spec:
  serviceAccountName: my-service-account
  containers:
  - name: my-container
    image: my-image
    command: ["cat", "/var/run/secrets/kubernetes.io/serviceaccount/token"]

This Pod will print the ServiceAccount token when it is run.

6. Role-Based Access Control (RBAC) with Service Accounts
Service Accounts are often used in combination with Role-Based Access Control (RBAC) to define what a ServiceAccount (and, by extension, the Pod using that ServiceAccount) can do in the cluster.
a) Create a Role
Here’s an example of creating a Role that allows access to Pods within a namespace:
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

This Role gives the ServiceAccount permission to get and list Pods in the default namespace.
b) Create a RoleBinding
A RoleBinding associates the Role with a ServiceAccount:
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: my-service-account
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

This binding allows the my-service-account ServiceAccount to read Pods in the default namespace.

7. Service Account Best Practices
Use the Least Privilege Principle: Grant only the minimum permissions required for the ServiceAccount to perform its tasks. This minimizes the risk of exposure.
Use Separate Service Accounts for Different Workloads: Avoid using the default ServiceAccount for every Pod. Create distinct ServiceAccounts for different types of workloads or applications to reduce the blast radius of compromised credentials.
Avoid Sharing Service Accounts Across Pods: When multiple Pods need different levels of access, create separate ServiceAccounts with the appropriate permissions for each.
Audit Service Account Usage: Regularly audit the permissions and roles granted to ServiceAccounts, and ensure they are not over-privileged.
Secure the Service Account Tokens: Ensure tokens are used securely, and access to the file paths where tokens are stored (e.g., /var/run/secrets/kubernetes.io/serviceaccount/) is restricted.
Rotate Service Account Tokens: Kubernetes automatically handles the expiration of tokens, but for critical workloads, consider integrating with external tools for more fine-grained control and rotation.

8. Deleting a Service Account
To delete a ServiceAccount:
kubectl delete serviceaccount my-service-account

Make sure to check if any Pods are using the ServiceAccount before deletion, as removing it may affect those Pods' ability to interact with the Kubernetes API.

9. Conclusion
Service Accounts are a vital component in Kubernetes security, providing a way to authenticate and authorize Pods and workloads within a cluster. By associating Service Accounts with appropriate RBAC roles, you can enforce strict access control policies and reduce the risk of over-privileged access.
In this section, you have learned:
How to create and use Service Accounts.
How to associate Service Accounts with Pods.
The role of RBAC in controlling access to Kubernetes resources via Service Accounts.
Best practices for securing and managing Service Accounts in your cluster.
By following best practices and using Service Accounts appropriately, you can secure your Kubernetes applications and ensure that they interact with the Kubernetes API and other resources in a controlled and authorized manner.
