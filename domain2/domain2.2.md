CKAD: Understand Deployments and How to Perform Rolling Updates
In Kubernetes, a Deployment is a fundamental resource that manages the lifecycle of applications running in pods. It ensures that the desired number of replicas of a pod are running at all times and handles updates, scaling, and rollout strategies like Rolling Updates.
As a Kubernetes Application Developer (CKAD), it's essential to understand how to configure and use Deployments and perform Rolling Updates to ensure seamless application upgrades with minimal downtime and risk.

1. What is a Deployment?
A Deployment in Kubernetes is a higher-level abstraction that manages the state of your application. It defines the desired state of your application, such as which Docker image to use, how many replicas of a pod to run, and other configuration details. Kubernetes ensures that the actual state matches the desired state.
Key Features of a Deployment:
Declarative Updates: You can define your desired application state, and Kubernetes will handle the deployment process (e.g., rolling updates or scaling).
Scaling: Deployments automatically scale the number of pods to the specified number of replicas.
Self-healing: If a pod crashes, the Deployment controller automatically creates new pods to replace them.
Versioning: Deployments support versioned updates and rollbacks to previous versions.

2. Understanding Rolling Updates
Rolling updates allow you to update an application without downtime by gradually replacing old pods with new ones. This process ensures that some instances of the application remain available while new versions are deployed.
How Rolling Updates Work:
Step 1: When you update the Deployment (e.g., change the Docker image), Kubernetes will create new pods with the updated configuration.
Step 2: It will slowly scale down the old pods and scale up the new ones, maintaining the desired number of pods running throughout the process.
Step 3: The new version of the pods will only be rolled out if they pass health checks, preventing the deployment of a failing version.
Step 4: If necessary, the update can be rolled back to a previous stable version.
Advantages of Rolling Updates:
Zero Downtime: Rolling updates help ensure that at least some replicas of the application are always running, preventing downtime during updates.
Safe Gradual Rollout: If issues occur with the new version, you can pause or roll back the update.
Version Control: You can easily revert to a previous stable version if necessary.

3. Creating a Deployment
To use a Deployment in Kubernetes, you create a YAML manifest that defines the deployment's configuration, including the Docker image, number of replicas, and other parameters.
Example of a simple Deployment manifest for an application:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app-container
          image: my-app:v1  # Version 1 of the app
          ports:
            - containerPort: 80

In this example:
The deployment is named my-app-deployment.
Three replicas are defined, meaning Kubernetes will maintain three pods running at all times.
The image: my-app:v1 defines the version of the Docker image being used for the application.

4. Performing a Rolling Update
Kubernetes automatically performs a rolling update when you change the Deployment's specification, such as updating the image version. The update can be done via the command line (kubectl) or by modifying the Deployment YAML file.
Step-by-Step Guide to Perform a Rolling Update:
Update the Deployment with a New Docker Image:
You can modify the Deployment by changing the Docker image tag (e.g., from v1 to v2). The rolling update will be initiated when the Deployment is updated.
Example:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app-container
          image: my-app:v2  # Updated to version 2 of the app
          ports:
            - containerPort: 80

To apply the update, run:
kubectl apply -f my-app-deployment.yaml

Alternatively, you can update the image directly using kubectl set image:
kubectl set image deployment/my-app-deployment my-app-container=my-app:v2

Kubernetes Starts the Rolling Update:


Kubernetes will replace the old pods (running v1) with new pods (running v2) one by one.
It ensures that the desired number of replicas (3 in this case) are running throughout the update process.
It monitors the health of the new pods (via liveness and readiness probes) before scaling down the old ones.
Verify the Rolling Update: You can track the progress of the rolling update with the following command:

 kubectl rollout status deployment/my-app-deployment
 This will show the status of the rolling update, such as "deployment rolling update complete."


Rollback if Necessary: If the rolling update encounters issues, Kubernetes can roll back to the previous stable version of the deployment. This can be done with the following command:

 kubectl rollout undo deployment/my-app-deployment
 This command will restore the previous version of the deployment and revert the changes.



5. Customizing Rolling Update Behavior
Kubernetes provides several options for customizing the behavior of rolling updates. You can define how quickly the update proceeds, how many pods can be unavailable at a time, and other parameters. These options are specified in the Deployment spec under the rollingUpdate strategy.
Example of Customizing Rolling Update Strategy:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Maximum number of pods that can be created above the desired number of pods
      maxUnavailable: 1  # Maximum number of pods that can be unavailable during the update
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app-container
          image: my-app:v2  # Updated image version
          ports:
            - containerPort: 80

In this example:
maxSurge: 1 allows one additional pod to be created during the update, ensuring that the overall pod count can temporarily exceed the desired replicas.
maxUnavailable: 1 ensures that no more than one pod can be unavailable at any time during the update.

6. Troubleshooting Rolling Updates
Occasionally, rolling updates can encounter issues, such as pods failing health checks or misconfigured resources. Here are some common troubleshooting steps:
Check Deployment Status:

 kubectl rollout status deployment/my-app-deployment
 This command provides information about the current state of the update, whether it’s progressing or has stalled.


Check Pod Logs:

 kubectl logs <pod-name>
 If the new pods aren’t starting correctly, check their logs for errors.


Revert to the Previous Version: If you encounter issues, use kubectl rollout undo to roll back to the previous stable version.



7. Conclusion
As a Kubernetes Application Developer (CKAD), understanding how to configure and manage Deployments and perform Rolling Updates is essential to deploying applications safely and efficiently. Rolling updates allow you to update applications with minimal disruption and no downtime, which is crucial for production-grade environments.
Deployments are used to manage the lifecycle of your applications and ensure that the desired state is always maintained.
Rolling updates enable you to gradually replace old versions of an application with new ones, while ensuring that the service remains available.
Customizing rolling update parameters like maxSurge and maxUnavailable helps fine-tune the update process for your specific needs.
By mastering these concepts, you’ll be able to implement seamless and safe application updates in your Kubernetes clusters.
