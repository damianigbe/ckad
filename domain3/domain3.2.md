CKAD: Implement Probes and Health Checks
In Kubernetes, probes and health checks are essential components for maintaining the health and availability of applications running in your clusters. These mechanisms help Kubernetes determine whether an application is running correctly, and they play a vital role in ensuring high availability and reliability.
As a Kubernetes Application Developer (CKAD), understanding and properly implementing probes and health checks in your application deployment is crucial for creating resilient applications that self-heal and remain stable under varying conditions.

1. What Are Probes and Health Checks in Kubernetes?
Kubernetes uses probes to periodically check the health of the containers running in Pods. If a container fails a health check, Kubernetes can automatically restart or replace it, ensuring minimal downtime and reducing the likelihood of failure.
There are three primary types of health checks (or probes) in Kubernetes:
Liveness Probe:
 This probe checks whether the container is still running. If the liveness probe fails, Kubernetes will kill the container and restart it. This is useful for situations where the container is alive but stuck in a non-functional state.


Readiness Probe:
 This probe checks if the container is ready to serve traffic. A failing readiness probe will prevent traffic from being routed to the container, but it will not kill it. This is useful during startup or when the application is not yet ready to handle requests.


Startup Probe:
 The startup probe checks whether the application within the container has started properly. This probe is useful when containers require significant startup time. If the startup probe fails, Kubernetes will kill the container, assuming it has not started successfully.



2. Why Are Probes Important?
Self-Healing: Probes allow Kubernetes to automatically restart failed containers, ensuring that your application remains available even when something goes wrong.
Service Discovery: By using readiness probes, Kubernetes ensures that only healthy and ready containers receive traffic, avoiding sending requests to containers that are not yet prepared to serve.
Resource Efficiency: Liveness probes help in identifying containers that are stuck or in a failed state, ensuring resources are not wasted on containers that are not functioning properly.
Improved User Experience: By ensuring that only healthy containers serve traffic, probes help avoid situations where users may experience downtime or degraded performance.

3. Types of Health Checks (Probes)
1. Liveness Probe
The liveness probe determines whether your application is still running and healthy. If it fails, Kubernetes will restart the container.
Use cases:
If an application gets stuck and cannot recover on its own (e.g., a deadlock or infinite loop).
If the application is non-responsive but can still be running.
How it works:
 Kubernetes will periodically call the endpoint you specify (e.g., an HTTP endpoint) to check if the application is healthy. If the probe fails, Kubernetes restarts the container.
Example of Liveness Probe:
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: my-app:latest
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
      failureThreshold: 3

Parameters:
initialDelaySeconds: How long to wait before performing the first probe.
periodSeconds: How often to run the probe.
failureThreshold: The number of consecutive failures before considering the container unhealthy.
2. Readiness Probe
The readiness probe determines whether the application inside the container is ready to serve traffic. Unlike the liveness probe, if the readiness probe fails, the container remains running but will not receive traffic until it passes.
Use cases:
If your application needs time to initialize (e.g., loading large datasets, establishing connections, etc.).
If your application becomes temporarily unresponsive due to high load or resource constraints.
Example of Readiness Probe:
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: my-app:latest
    readinessProbe:
      httpGet:
        path: /readiness
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
      failureThreshold: 3

Parameters:
httpGet: Specifies the HTTP endpoint that Kubernetes will probe.
failureThreshold: The number of consecutive failures before the container is considered not ready to serve traffic.
3. Startup Probe
The startup probe helps in scenarios where your container may take time to start (e.g., loading large configurations or running initial setup tasks). If the startup probe fails, Kubernetes assumes the container has failed to start and will kill it.
Use cases:
When the container takes a long time to start (e.g., databases, large applications).
When the application needs time to establish external dependencies, like database connections or external API calls.
Example of Startup Probe:
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: my-app:latest
    startupProbe:
      httpGet:
        path: /startup
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 5
      failureThreshold: 5

Parameters:
initialDelaySeconds: How long to wait before performing the first probe.
failureThreshold: Number of failures before considering the container as failed.

4. Health Check Methods
Health checks can be implemented in several ways:
HTTP-based Health Checks: Kubernetes sends HTTP requests to a specified path (such as /healthz or /readiness) and expects a 200 OK response to determine if the application is healthy.


TCP Socket Checks: Kubernetes can check if a specific port is open and accepting connections. If a connection can be established, the container is considered healthy.


Command-based Health Checks: Kubernetes executes a specified command inside the container. If the command exits with a status of 0, the container is considered healthy. Otherwise, it’s considered failed.


Example of Command-based Health Check:
livenessProbe:
  exec:
    command:
      - "/bin/sh"
      - "-c"
      - "cat /tmp/healthy"
  initialDelaySeconds: 5
  periodSeconds: 10

The command returns 0 if the file /tmp/healthy exists, which indicates the container is healthy.

5. Best Practices for Health Checks
Set appropriate initialDelaySeconds: Allow the application enough time to start and initialize before the probes begin. Setting an initial delay too short can result in false failures.


Choose the right probe for the right purpose:


Use readiness probes for containers that need time to initialize before receiving traffic.
Use liveness probes to detect and restart containers that become unresponsive.
Use startup probes to detect if containers take longer to start than expected.
Monitor probe results: Use Kubernetes’ monitoring and logging tools to track the success or failure of health probes. This helps in identifying issues early.


Test probes locally: Before deploying to Kubernetes, test your health check endpoints locally to ensure they work as expected.


Avoid over-tightening probe thresholds: Set reasonable thresholds for failures and delays to prevent unnecessary restarts or blocking of traffic. Too aggressive health checks can cause unnecessary container restarts.



6. Conclusion
Implementing probes and health checks is essential for maintaining the health and availability of your applications in Kubernetes. As a Kubernetes Application Developer (CKAD), you must know how to use liveness, readiness, and startup probes effectively to ensure your applications remain resilient, self-healing, and able to serve traffic without downtime.
By correctly setting up health checks:
Your containers will be monitored and restarted automatically when necessary.
Kubernetes will route traffic only to ready containers.
You’ll be able to identify and resolve issues early, improving your application's overall reliability.
Always test your probes, monitor the results, and adjust them based on the specific needs of your application!
