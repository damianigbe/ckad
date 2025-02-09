CKAD: Create & Consume Secrets
In Kubernetes, Secrets are a crucial way to store sensitive information such as passwords, API tokens, SSH keys, and other credentials in a secure and encrypted manner. These Secrets can be consumed by Pods and containers to access secure data without hardcoding it into your application code or configuration files.
In this section, we will cover the following topics:
How to create Kubernetes Secrets.
How to consume Secrets in a Pod.
Best practices for handling Secrets securely.

1. What are Kubernetes Secrets?
Kubernetes Secrets store sensitive information, such as:
Passwords
OAuth tokens
SSH keys
TLS certificates
Secrets are stored in the cluster and can be used by Pods and other resources in the cluster to access sensitive information without exposing it in your configuration files. The key advantage of using Secrets is that they are encrypted at rest, and their values can be managed securely.

2. Creating Kubernetes Secrets
Secrets can be created in Kubernetes in several ways: manually via kubectl, through YAML files, or using external tools (e.g., Helm, Kustomize).
a) Create Secrets Using kubectl Command
You can create Secrets directly from the command line using kubectl create secret. There are various ways to create Secrets depending on how you want to provide the secret data.
Example 1: Create a Secret from Literal Values
kubectl create secret generic my-secret \
  --from-literal=username=admin \
  --from-literal=password=secretpassword

This creates a Secret named my-secret with two keys: username and password, and their respective values.
Example 2: Create a Secret from a File
If you have a file containing sensitive information, you can create a Secret from the file content.
kubectl create secret generic my-secret \
  --from-file=my-ssh-key=/path/to/ssh/keyfile

This creates a Secret named my-secret with a key my-ssh-key containing the contents of the file /path/to/ssh/keyfile.
Example 3: Create a Secret from Multiple Files
kubectl create secret generic my-secret \
  --from-file=ssh-private-key=/path/to/private_key \
  --from-file=ssh-public-key=/path/to/public_key

This creates a Secret with two files, ssh-private-key and ssh-public-key, containing the respective SSH keys.
b) Create Secrets Using YAML
Secrets can also be defined in a YAML manifest. This is useful for version control and automation.
Example:
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=    # Base64 encoded string for 'admin'
  password: c2VjcmV0cGFzc3dvcmQ=    # Base64 encoded string for 'secretpassword'

type: Opaque: This indicates the secret is of a general type and not associated with any specific Kubernetes use case (e.g., Docker registry secrets).
data: The sensitive data in a Secret must be base64 encoded. In this example, the values for username and password are encoded strings for admin and secretpassword.
To apply the YAML manifest:
kubectl apply -f secret.yaml


3. Consuming Secrets in a Pod
Once the Secret is created, it can be consumed by a Pod in two primary ways:
Environment Variables
Mounted as Volumes
a) Consume Secrets as Environment Variables
You can expose a Secret as an environment variable inside a container. This method is useful when your application needs to access the Secret as a simple environment variable.
Example: Consume a Secret as an environment variable in a Pod
apiVersion: v1
kind: Pod
metadata:
  name: secret-demo
spec:
  containers:
  - name: my-container
    image: my-image
    env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
    - name: SECRET_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password

In this example:
The username and password keys from the Secret my-secret are injected into the container as environment variables (SECRET_USERNAME and SECRET_PASSWORD).
The application inside the container can now access the values through environment variables.
b) Consume Secrets as Mounted Volumes
Secrets can also be mounted as volumes, which allows your application to access them as files within the container. This is useful when you need to pass a file-based Secret (like an SSL certificate, SSH key, etc.).
Example: Consume a Secret as a mounted volume
apiVersion: v1
kind: Pod
metadata:
  name: secret-volume-demo
spec:
  containers:
  - name: my-container
    image: my-image
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret

In this example:
The Secret my-secret is mounted as a volume at /etc/secrets inside the container.
The container will have access to all the keys in the Secret as files under /etc/secrets, e.g., /etc/secrets/username and /etc/secrets/password.

4. Best Practices for Managing Secrets
Avoid Hardcoding Secrets: Never hardcode sensitive data like passwords, API tokens, or certificates directly into your code or Kubernetes YAML files.
Use kubectl to Read Secrets: Avoid printing Secrets in plain text to the console. Use kubectl get secret my-secret -o yaml or kubectl describe secret my-secret to view Secrets in a secure way.
Limit Access: Only allow the Pods or services that need to access a particular Secret to do so. Use Role-Based Access Control (RBAC) to control access to Secrets.
Encrypt Secrets at Rest: Kubernetes supports encrypting Secrets at rest. Ensure that your Kubernetes cluster has this enabled.
Use External Secrets Management: If you need to integrate with enterprise-grade secrets management solutions, consider using tools like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault.
Base64 Encoding Is Not Encryption: Secrets are base64 encoded, not encrypted. Ensure that your Kubernetes cluster uses encryption at rest to protect Secret data.

5. Deleting a Secret
To delete a Secret that is no longer required:
kubectl delete secret my-secret

This removes the Secret from the cluster. Make sure that any applications or resources depending on it are updated or deleted before removing a Secret.

6. Conclusion
As a Kubernetes Application Developer (CKAD), understanding how to securely create and consume Secrets is vital for building safe and efficient applications. Whether you choose to pass Secrets as environment variables or mount them as volumes, ensuring that they are handled securely and not exposed in your code is crucial for maintaining the confidentiality of sensitive data.
Creating Secrets: Use kubectl, YAML manifests, or external tools.
Consuming Secrets: Inject them as environment variables or mount them as volumes in your Pods.
Best Practices: Implement security measures like encryption at rest, and limit access to Secrets using RBAC.
By mastering these concepts, you can securely manage sensitive data in your Kubernetes clusters and build more secure, reliable applications.
