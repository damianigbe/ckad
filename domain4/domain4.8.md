CKAD: Understand Application Security (SecurityContexts, Capabilities, etc.)
Application security in Kubernetes focuses on securing containers and their environments to minimize vulnerabilities and risks. This involves configuring and controlling various security features to ensure that applications run with the least privilege, restrict unnecessary permissions, and protect the integrity of both the host and the containers.
In this section, we'll cover:
SecurityContext: What it is, and how to use it for controlling security settings at the Pod or container level.
Linux Capabilities: Understanding and configuring Linux capabilities for containers to limit the permissions granted to a container.
Pod Security Policies (PSP): How to enforce security policies at the Pod level (if enabled).
Other Key Security Features: User namespaces, read-only file systems, seccomp, and AppArmor.

1. What is a SecurityContext?
A SecurityContext is a Kubernetes resource used to define security-related settings for Pods and containers. You can specify a SecurityContext at two levels:
Pod-level SecurityContext: This applies to all containers in the Pod.
Container-level SecurityContext: This applies to individual containers in the Pod.
SecurityContext allows you to configure various security settings such as user and group IDs, privilege escalation, and more. By configuring the SecurityContext, you can ensure that containers run with the appropriate security configuration, reducing the risk of exploitation.
Common SecurityContext Settings
runAsUser: Specifies the user ID (UID) to run the container as.
runAsGroup: Specifies the group ID (GID) to run the container as.
fsGroup: The group ID to apply to files created by the container in volumes.
privileged: If true, gives the container extended privileges. By default, containers do not run in privileged mode.
allowPrivilegeEscalation: If set to false, it prevents a container from gaining more privileges than its parent process.
readOnlyRootFilesystem: If set to true, it mounts the root filesystem as read-only.
capabilities: Controls which Linux capabilities to add or drop from the container.

Example of Pod-level SecurityContext
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: secure-container
    image: nginx
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true

In this example:
The Pod-level SecurityContext specifies that the Pod will run with a user ID of 1000, group ID of 3000, and any files created in volumes will be associated with the group ID 2000.
The Container-level SecurityContext ensures the container cannot escalate privileges (allowPrivilegeEscalation: false) and the root filesystem is mounted as read-only (readOnlyRootFilesystem: true).

2. Linux Capabilities
Linux capabilities are a set of fine-grained access controls that allow you to grant or remove specific privileges from processes. Containers by default have a limited set of Linux capabilities to minimize security risks. However, some applications require additional capabilities.
Key Capabilities
NET_ADMIN: Allows the process to configure networking interfaces.
SYS_ADMIN: Grants a range of system-level administrative capabilities, such as mounting file systems.
DAC_OVERRIDE: Allows overriding file read, write, and execute permissions.
How to Modify Capabilities
In Kubernetes, you can add or remove Linux capabilities from a container using the securityContext.capabilities field. For example, if you want to add the NET_ADMIN capability, you can do so like this:
apiVersion: v1
kind: Pod
metadata:
  name: capability-pod
spec:
  containers:
  - name: capability-container
    image: my-container
    securityContext:
      capabilities:
        add:
        - NET_ADMIN

In this example, the container is granted the NET_ADMIN capability, which allows it to modify networking settings.
Best Practice:
Always try to remove unnecessary capabilities to adhere to the principle of least privilege. Adding capabilities should only be done when absolutely necessary.

3. Pod Security Policies (PSP)
Pod Security Policies (PSPs) are a set of policies that control the security features available to Pods in a cluster. PSPs allow you to define rules around:
What security context settings are allowed.
What privileges containers can request.
Restrictions on the use of host network, volumes, and namespaces.
Key PSP Settings:
privileged: Whether privileged mode containers are allowed.
runAsUser: Controls which user IDs are allowed to run.
allowPrivilegeEscalation: Defines if privilege escalation is allowed.
hostNetwork: Restricts Pods from using the host network.
hostPID: Restricts Pods from using the host's process ID namespace.
readOnlyRootFilesystem: Enforces read-only root filesystem for Pods.
PSP Example:
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted-psp
spec:
  privileged: false
  runAsUser:
    rule: MustRunAsNonRoot
  allowPrivilegeEscalation: false
  volumes:
    - configMap
    - secret
  hostNetwork: false
  readOnlyRootFilesystem: true

In this example, the PSP:
Disallows privileged containers.
Requires containers to run as non-root.
Disallows privilege escalation.
Restricts volumes to only ConfigMaps and Secrets.
Disallows the use of the host network.
Enforces read-only root filesystem for containers.
Note: PSP is deprecated and will be removed in Kubernetes 1.25+. It is being replaced with PodSecurity policies.

4. Other Security Features
a) Seccomp
Seccomp (Secure Computing Mode) is a Linux kernel feature that allows you to filter system calls that containers can make, limiting the attack surface. By using seccomp profiles, you can enforce what system calls a container is allowed to invoke.
apiVersion: v1
kind: Pod
metadata:
  name: seccomp-pod
spec:
  containers:
  - name: seccomp-container
    image: my-container
    securityContext:
      seccompProfile:
        type: RuntimeDefault

In this example, the container uses the default seccomp profile, which restricts it from making potentially dangerous system calls.
b) AppArmor
AppArmor is another Linux security module that provides mandatory access control (MAC) by restricting the capabilities of containers and their access to the underlying system. You can configure AppArmor profiles to restrict what a container can access or do.
apiVersion: v1
kind: Pod
metadata:
  name: apparmor-pod
spec:
  containers:
  - name: apparmor-container
    image: my-container
    securityContext:
      appArmorProfile: "runtime/default"

This example ensures that the container is constrained by the runtime/default AppArmor profile.
c) User Namespaces
Kubernetes can isolate the user namespace of containers, allowing containers to run with different user IDs and group IDs on the host. This helps to isolate containers and reduce the risks associated with running containers as root.

5. Best Practices for Application Security
Run containers as non-root users: Always configure your containers to run as non-root users unless absolutely necessary.
Limit the privileges of containers: Use SecurityContexts to limit capabilities and prevent privilege escalation.
Use read-only root filesystem: Configure containers to use a read-only root filesystem to prevent modifications to container files.
Use seccomp and AppArmor profiles: Implement seccomp and AppArmor to limit system calls and interactions with the host OS.
Use RBAC for authorization: Implement Role-Based Access Control (RBAC) to restrict access to Kubernetes resources, ensuring only authorized entities can access sensitive resources.
Audit security configurations: Regularly audit security settings like SecurityContexts, PSPs, and user privileges to ensure they adhere to the principle of least privilege.

6. Conclusion
In Kubernetes, application security is a vital part of ensuring that your workloads run in a controlled and secure environment. By leveraging tools like SecurityContexts, Linux capabilities, Pod Security Policies, seccomp, and AppArmor, you can minimize the risk of vulnerabilities and ensure your containers adhere to security best practices.
By implementing the principles of least privilege, restricting unnecessary capabilities, and utilizing advanced security features, you ensure that your Kubernetes applications are robust and secure, both inside the container and in the broader Kubernetes cluster.
Key takeaways:
Always use SecurityContexts to define specific security configurations for Pods and containers.
Limit container privileges using Linux capabilities and ensure Pod Security Policies are enforced.
Leverage seccomp and AppArmor to restrict container interactions with the host.
Continuously apply security best practices to safeguard your Kubernetes workloads.
