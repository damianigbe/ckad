CKAD: Use Kubernetes Primitives to Implement Common Deployment Strategies (e.g., Blue/Green or Canary)
In Kubernetes, deploying applications efficiently and safely is critical to maintaining high availability and minimizing downtime. One of the primary ways to achieve this is by using deployment strategies such as Blue/Green and Canary deployments. These strategies allow you to roll out updates incrementally, minimizing risk and enabling better control over the release process.
As a Kubernetes Application Developer (CKAD), you must understand how to implement these strategies using Kubernetes primitives such as Deployments, Services, Labels, and Selectors.

1. Blue/Green Deployment Strategy
Blue/Green Deployment is a deployment strategy where two environments (the "Blue" and "Green" environments) are maintained. The "Blue" environment is the live, production version of your application, while the "Green" environment contains the updated version of the application.
How it Works:
Initially, the "Blue" environment (the live version) serves all the traffic.
The "Green" environment is deployed and tested in parallel with the "Blue" environment.
Once the "Green" environment is tested and verified, the traffic is switched to the "Green" environment, making it the new production.
The "Blue" environment can be kept as a fallback in case of issues with the new deployment.
Advantages:
Minimized risk: If there is an issue with the "Green" deployment, you can quickly revert back to the "Blue" version.
Zero downtime: With traffic routed between two separate environments, you can achieve a seamless switch.
Implementation in Kubernetes:
To implement Blue/Green in Kubernetes, you can use two separate Deployments (one for Blue, one for Green) and a Service that directs traffic to the active environment.
Step 1: Define Blue and Green Deployments
# Blue Deployment (Current live version)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: blue
  template:
    metadata:
      labels:
        app: my-app
        version: blue
    spec:
      containers:
        - name: my-app
          image: my-app:blue
          ports:
            - containerPort: 80

---
# Green Deployment (New version to be deployed)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
spec:
  replicas: 0  # Initially set to 0 replicas until switch
  selector:
    matchLabels:
      app: my-app
      version: green
  template:
    metadata:
      labels:
        app: my-app
        version: green
    spec:
      containers:
        - name: my-app
          image: my-app:green
          ports:
            - containerPort: 80

Step 2: Define a Service to Route Traffic
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
    version: blue  # Initially routes to the blue version
  ports:
    - port: 80
      targetPort: 80

Step 3: Switch Traffic to the Green Deployment
Once you have tested the "Green" deployment, you can modify the Service to point to the new version:
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
    version: green  # Switch traffic to green version
  ports:
    - port: 80
      targetPort: 80

Step 4: Scale Down/Remove Blue Deployment (Optional)
After the traffic is fully switched to the "Green" environment, you can scale down or delete the "Blue" Deployment:
kubectl scale deployment app-blue --replicas=0


2. Canary Deployment Strategy
Canary Deployment is a strategy where a new version of the application is deployed to a small subset of users before being rolled out to the entire user base. This allows you to monitor the performance of the new version in a production environment with minimal risk.
How it Works:
A small portion of traffic is routed to the new version (the "canary").
The rest of the traffic continues to go to the stable version.
If the new version performs well (i.e., no critical issues are found), more traffic is gradually shifted towards it until it is fully deployed.
If issues are detected, the new version can be rolled back or adjusted.
Advantages:
Incremental rollout: Allows testing in a real-world environment with minimal risk.
Easy rollback: If something goes wrong, the rollout can be stopped, and the traffic can be directed back to the stable version.
Implementation in Kubernetes:
To implement Canary deployment in Kubernetes, you typically use a Deployment and incrementally change the number of replicas for the new version. This is managed by adjusting the replica count for both the stable and canary versions.
Step 1: Define the Stable Version (e.g., stable) and Canary Version (e.g., canary) Deployments
# Stable Version Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-stable
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: stable
  template:
    metadata:
      labels:
        app: my-app
        version: stable
    spec:
      containers:
        - name: my-app
          image: my-app:stable
          ports:
            - containerPort: 80

---
# Canary Version Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-canary
spec:
  replicas: 1  # Start with a small number of replicas for the canary
  selector:
    matchLabels:
      app: my-app
      version: canary
  template:
    metadata:
      labels:
        app: my-app
        version: canary
    spec:
      containers:
        - name: my-app
          image: my-app:canary
          ports:
            - containerPort: 80

Step 2: Define a Service
The Service will initially route traffic to both the stable and canary versions. As the rollout progresses, you can gradually change the traffic distribution.
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80

Step 3: Gradually Increase the Canary Replicas
Initially, the canary version will have a small number of replicas (e.g., 1 replica). Over time, you can gradually scale up the canary deployment and scale down the stable version to increase traffic to the new version:
kubectl scale deployment app-canary --replicas=3  # Increase canary replicas
kubectl scale deployment app-stable --replicas=2  # Scale down stable version

Step 4: Monitor and Adjust
While the canary deployment is running, monitor its performance. If it’s working as expected, continue scaling it up. If any issues arise, you can easily scale it back down or roll it back to the stable version.
Step 5: Complete the Deployment
Once the canary version is fully tested, you can scale it to the desired number of replicas, ensuring that all traffic is now routed to the new version.

3. Comparison of Blue/Green and Canary Deployment
Feature
Blue/Green Deployment
Canary Deployment
Traffic Management
Entire traffic switches from Blue to Green once the new version is ready.
Gradually shifts a small portion of traffic to the new version.
Rollback
Rollback to the Blue version is instantaneous.
Rollback can be done by scaling down the canary replicas.
Risk Level
Low risk once the Green environment is validated.
Low risk with gradual exposure of the new version.
Deployment Complexity
Simpler, but requires managing two complete environments.
More complex, requires monitoring and gradual rollout.
Use Case
Ideal for major version upgrades with minimal downtime.
Ideal for testing a new version with a subset of users.


4. Conclusion
As a Kubernetes Application Developer (CKAD), understanding how to implement Blue/Green and Canary deployments will empower you to release applications safely and with minimal risk. These strategies help you manage application updates in a controlled manner, ensuring high availability and reducing the impact of issues that may arise with new versions.
Both strategies use Kubernetes primitives like Deployments, Services, Labels, and Selectors, allowing for flexibility in managing how updates are rolled out across your cluster.
Blue/Green deployments are excellent for switching between major versions with minimal downtime.
Canary deployments are useful for incrementally rolling out updates and monitoring their impact on production environments.
By mastering these strategies, you can ensure more resilient, reliable, and efficient application rollouts in Kubernetes.
