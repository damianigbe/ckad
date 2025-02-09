CKAD: Use Ingress Rules to Expose Applications
In Kubernetes, Ingress is an API object that manages external access to services within a cluster, typically HTTP and HTTPS traffic. Ingress allows you to expose HTTP/S-based applications in a Kubernetes cluster to the outside world, providing advanced routing, SSL termination, load balancing, and host-based or path-based routing.
In this section, we will cover:
How to define and configure Ingress rules.
Common use cases for Ingress in Kubernetes.
How to troubleshoot Ingress-related issues.
Best practices when using Ingress to expose applications.

1. Overview of Kubernetes Ingress
Ingress is a powerful Kubernetes resource that allows you to configure external HTTP/S access to your services. Unlike Services, which provide simple access to a group of Pods within the cluster, Ingress allows you to define fine-grained routing rules and handle SSL termination, path-based routing, and more.
Components of Ingress:
Ingress Resource: The configuration for how external HTTP/S traffic should be routed to Services inside the cluster.
Ingress Controller: A controller that implements the rules defined in the Ingress resource. It is responsible for managing and directing traffic based on Ingress rules. Popular controllers include NGINX, Traefik, and HAProxy.
Benefits of Using Ingress:
URL-based Routing: Route traffic to different services based on the URL path (e.g., /app1 to one service, /app2 to another).
Host-based Routing: Route traffic to services based on the host domain (e.g., example.com vs app.example.com).
SSL Termination: Terminate SSL/TLS at the Ingress controller, offloading the burden from individual services.
Path-based Routing: Route traffic to different services based on request paths.

2. How to Define Ingress Rules
Ingress rules are defined using a Kubernetes Ingress resource. A basic Ingress configuration looks like this:
Example: Basic Ingress Resource
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app-ingress
  namespace: default
spec:
  rules:
    - host: my-app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-app-service
                port:
                  number: 80

Explanation:
host: The domain name (my-app.example.com) that will be used to route traffic to this Ingress.
http: This section defines HTTP routing rules.
paths: Defines URL paths and the backend service to forward traffic to. In this case, any traffic to / will be directed to my-app-service on port 80.
pathType: Defines how the path should be interpreted (Prefix matches the path and everything that starts with it, Exact matches only the exact path).
Example: Ingress with Path and Host-based Routing
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-path-ingress
  namespace: default
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /app1
            pathType: Prefix
            backend:
              service:
                name: app1-service
                port:
                  number: 80
          - path: /app2
            pathType: Prefix
            backend:
              service:
                name: app2-service
                port:
                  number: 80

Explanation:
Traffic to example.com/app1 will be routed to app1-service, while traffic to example.com/app2 will be routed to app2-service.
Example: Ingress with SSL Termination
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ssl-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  rules:
    - host: secure-app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: secure-app-service
                port:
                  number: 443
  tls:
    - hosts:
        - secure-app.example.com
      secretName: my-ssl-secret

Explanation:
This configuration enables SSL termination at the Ingress controller for the host secure-app.example.com.
The SSL certificate is stored in a Secret (my-ssl-secret), which is used by the Ingress controller to handle HTTPS traffic.
The nginx.ingress.kubernetes.io/ssl-redirect annotation ensures HTTP requests are redirected to HTTPS.

3. Setting Up an Ingress Controller
In order to use Ingress, you need to have an Ingress controller running in your cluster. Kubernetes does not come with a built-in Ingress controller, so you will need to deploy one.
Here’s an example of how to deploy the NGINX Ingress Controller:
Deploy NGINX Ingress Controller
Create the NGINX Ingress Controller using Helm or kubectl. To install NGINX via Helm, run:

 helm install nginx-ingress ingress-nginx/ingress-nginx --namespace kube-system


Alternatively, to deploy using kubectl:

 kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
 This will create all necessary resources for NGINX to act as your Ingress controller.


Once deployed, verify that the controller is running:

 kubectl get pods -n kube-system


You can check if the controller has an external IP address by running:

 kubectl get svc -n kube-system



4. Troubleshooting Ingress Issues
If you're experiencing issues with your Ingress configuration, there are several key areas to check:
1. Verify Ingress Resource Configuration
Check if the Ingress resource is properly created and configured.
kubectl get ingress my-app-ingress -o yaml

Ensure that the host, paths, and backend service names are correctly defined.
2. Check Ingress Controller Logs
The Ingress controller is responsible for routing the traffic based on your rules. If traffic is not being routed correctly, check the logs of your Ingress controller.
For NGINX Ingress, you can run:
kubectl logs <nginx-ingress-pod> -n kube-system

Look for errors related to configuration, missing services, or SSL issues.
3. Verify Service and Pod Availability
Ensure that the backend Service and Pods are running and reachable by the Ingress controller.
kubectl get svc my-app-service
kubectl get pods -l app=my-app

Verify that the service exists and that there are Pods available to handle requests.
4. Check DNS Resolution
Ensure that the DNS for the Ingress host is correctly set up. For example, if you're using my-app.example.com, make sure that the DNS record points to the Ingress controller’s external IP address or LoadBalancer IP.
Use nslookup or dig to verify:
nslookup my-app.example.com

5. SSL/TLS Issues
If you're using SSL, verify that the SSL certificate is correctly installed and the TLS Secret is present.
kubectl get secret my-ssl-secret

If there are issues with SSL/TLS, check the Ingress controller logs for SSL-related errors.

5. Best Practices for Using Ingress
Use Annotations for Fine-grained Control: Many Ingress controllers (like NGINX) support annotations for additional configuration, such as SSL redirection, rewrite-targets, rate limiting, etc. Make sure to utilize them when needed.


Leverage Path and Host-Based Routing: Organize your services logically by exposing multiple applications through the same domain using path-based or host-based routing. This reduces the need for multiple public IPs or LoadBalancers.


Secure Ingress with SSL: Always use SSL/TLS for secure communication. Use Kubernetes Secrets to manage SSL certificates and configure your Ingress controller to terminate SSL/TLS traffic.


Monitor and Log Traffic: Enable logging and monitoring in the Ingress controller to troubleshoot issues and monitor traffic patterns.


Consider Using a Centralized Ingress Resource: If you have multiple namespaces and applications, consider using a centralized Ingress resource or controller for consistent routing policies across all applications.



6. Conclusion
Ingress provides a powerful way to expose Kubernetes applications to the outside world. It offers advanced features like path-based routing, SSL termination, and host-based routing, which makes it ideal for complex applications with multiple services. By understanding how to define Ingress resources and troubleshoot issues, you can ensure that your Kubernetes applications are accessible and functioning as intended.
Key Takeaways:
Ingress allows advanced HTTP/S routing for Kubernetes applications.
Ingress controllers are needed to implement Ingress rules, with NGINX being a popular choice.
Ensure DNS, SSL, and Service configurations are correctly set up.
Troubleshoot
issues by checking logs, resources, and DNS configuration.
Follow best practices for securing and organizing your Ingress resources.


