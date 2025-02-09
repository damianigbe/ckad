CKAD: Understand Authentication, Authorization, and Admission Control
As a Kubernetes Application Developer (CKAD), it’s crucial to understand the foundational concepts of authentication, authorization, and admission control in Kubernetes. These are core components of Kubernetes' security architecture, ensuring that only authorized users and processes can access the cluster and that resources are deployed in a secure and controlled manner.
Let’s break down these concepts in the context of Kubernetes.

1. Authentication in Kubernetes
Authentication is the process of verifying the identity of a user, application, or service trying to access the Kubernetes API. Kubernetes supports several methods of authentication:
a) Common Authentication Methods in Kubernetes
Certificates: Kubernetes can authenticate clients via TLS client certificates. When a user or service makes a request to the API server, the client presents a certificate, and the server verifies it.


Bearer Tokens: A bearer token is typically used in service-to-service communication. When a client makes a request, the bearer token in the request header is validated by the API server.


OpenID Connect (OIDC): Kubernetes can be integrated with external identity providers (e.g., Google, Azure AD) via OIDC for authentication. This allows you to leverage existing user directories to authenticate users.


Static Password Files: Kubernetes also supports simple static password files for user authentication. This method is less secure but still available for basic setups.


Webhook: Kubernetes can authenticate users via external systems through a webhook mechanism. When a request is made, the API server can send a request to an external authentication service, which can verify the identity and respond with a success or failure status.


b) How Authentication Works in Kubernetes
When a user or service interacts with the Kubernetes API, the kube-apiserver is responsible for authenticating the client. Once authenticated, the request proceeds to authorization (covered next).

2. Authorization in Kubernetes
Authorization determines whether an authenticated user has the appropriate permissions to perform a specific action on a resource. Kubernetes uses Role-Based Access Control (RBAC) as its primary authorization mechanism.
a) Key Authorization Concepts in Kubernetes
Role-Based Access Control (RBAC): RBAC allows Kubernetes administrators to define policies that specify what actions are allowed or denied on resources based on roles. RBAC has the following core components:
Role: A set of permissions within a specific namespace. A Role grants access to resources within that namespace.
ClusterRole: A set of permissions that can be applied across all namespaces or cluster-wide.
RoleBinding: A binding that assigns a Role to a user, group, or service account within a namespace.
ClusterRoleBinding: A binding that assigns a ClusterRole to a user, group, or service account across the entire cluster.
Example RBAC Role:
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: read-only
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]

ServiceAccount: Kubernetes allows workloads (like Pods) to authenticate as a ServiceAccount. A ServiceAccount is associated with specific RBAC roles, determining its permissions.


ABAC (Attribute-Based Access Control): Although Kubernetes mainly uses RBAC for authorization, it also supports ABAC, which allows decisions based on attributes such as user attributes, resource attributes, etc. ABAC is less commonly used compared to RBAC.


b) How Authorization Works in Kubernetes
After authentication, the kube-apiserver checks whether the authenticated identity is authorized to perform the requested operation. If authorized, the request proceeds to the next stage; if not, the request is denied with an HTTP 403 Forbidden response.

3. Admission Control in Kubernetes
Admission Control is a set of plugins that govern and enforce policies on requests to the Kubernetes API server. These plugins operate after authentication and authorization but before the API server writes objects to etcd.
Admission control is essential for enforcing security policies, ensuring that resources conform to organizational standards, and preventing misconfigurations or harmful deployments.
a) Common Admission Controllers in Kubernetes
NamespaceLifecycle: Ensures that the creation or deletion of resources occurs only within active namespaces.
LimitRanger: Enforces resource limits (like CPU or memory) for pods and containers.
ServiceAccount: Ensures that every Pod is associated with a ServiceAccount, and that ServiceAccount is properly configured.
PodSecurityPolicy (PSP): This deprecated controller enforces security policies at the Pod level, such as restricting privileged containers or enforcing security contexts.
ValidatingAdmissionWebhook: Allows integration with external admission control systems to validate requests dynamically.
MutatingAdmissionWebhook: Allows modification of Kubernetes objects before they are persisted (e.g., adding default labels or annotations).
AlwaysPullImages: Enforces that every Pod in a namespace pulls images from a remote registry rather than using locally cached images, helping prevent the use of outdated images.
b) How Admission Control Works in Kubernetes
After authentication and authorization checks, the admission controllers evaluate the request and can:
Allow the request to be processed by the API server.
Reject the request with an error message (e.g., if a resource request exceeds limits or violates a policy).
Mutate the request by modifying it before saving to the cluster (e.g., adding labels or annotations).
For example, the LimitRanger admission controller ensures that Pods can only request resources within a predefined range. If a user attempts to create a Pod requesting more than the allowed resources, the controller will reject the request.

4. Example Workflow: Authentication, Authorization, and Admission Control
Authentication: A user submits a request to the Kubernetes API to create a Pod.


The API server verifies the user's identity (e.g., using certificates or tokens).
If the authentication is successful, the request proceeds to the authorization phase.
Authorization: Kubernetes checks if the user has the correct permissions to create a Pod.


If the user has the necessary permissions (via RBAC or other methods), the request moves to admission control.
If the user lacks permissions, they are denied with a 403 Forbidden error.
Admission Control: The request to create the Pod is then evaluated by admission controllers.


If the Pod spec is valid and compliant with policies (such as resource limits), the request is allowed.
If there are any violations (e.g., exceeding resource limits), the request is rejected.

5. Practical Steps to Configure Authentication, Authorization, and Admission Control
a) Enabling and Configuring Authentication
Kubernetes supports multiple authentication methods. When setting up a cluster, you can configure which methods to use (certificates, tokens, OIDC, etc.) in the API server’s flags or config file.
b) Configuring RBAC for Authorization
RBAC roles are defined in YAML files. For example, you can create roles for users or service accounts and bind them to specific resources with RoleBinding and ClusterRoleBinding objects.
Example of a RoleBinding:
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-only-binding
  namespace: default
subjects:
  - kind: User
    name: "johndoe"
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: read-only
  apiGroup: rbac.authorization.k8s.io

c) Setting Up Admission Controllers
Admission controllers are enabled via the --enable-admission-plugins flag in the Kubernetes API server configuration. Some admission controllers come pre-enabled by default, but you may need to enable others manually depending on your needs.
For example, enabling the PodSecurityPolicy (deprecated) admission controller:
kube-apiserver --enable-admission-plugins=PodSecurityPolicy

d) Viewing and Debugging Authentication and Authorization
You can view the roles and bindings in the cluster using:
kubectl get roles,rolebindings --all-namespaces
kubectl get clusterroles,clusterrolebindings

You can debug authentication and authorization issues by looking at the API server logs, which often contain detailed error messages.

6. Conclusion
As a Kubernetes Application Developer, understanding authentication, authorization, and admission control is crucial to ensuring the security and proper functioning of your applications and the cluster. Here's a quick summary:
Authentication ensures that only valid users or services can access the cluster.
Authorization determines what an authenticated user or service is allowed to do, based on roles and permissions.
Admission Control enforces policies and validates or modifies requests before they are applied to the cluster.
Together, these three mechanisms help secure the Kubernetes ecosystem by ensuring that only authorized users and processes can modify resources, while also enforcing best practices and organizational policies.
