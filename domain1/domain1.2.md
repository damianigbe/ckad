CKAD: Choose and Use the Right Workload Resource (Deployment, DaemonSet, CronJob, etc.)
In Kubernetes, workloads are the resources responsible for running your applications. They define the application’s deployment and execution patterns, scaling, and availability. As a Kubernetes Application Developer (CKAD), one of your key responsibilities is to choose the appropriate workload resource for your application based on your requirements.
In this section of the CKAD training, we will explore the different types of workload resources available in Kubernetes and how to use them effectively.

1. What are Workload Resources in Kubernetes?
A workload resource in Kubernetes is a resource type that helps you run your application containers in a Kubernetes cluster. It defines the desired state for your application (e.g., how many replicas of a container to run, whether it should be scheduled on specific nodes, etc.) and allows Kubernetes to manage the lifecycle of the application according to that state.
There are several types of workload resources in Kubernetes, and each has specific use cases. Some of the most common workload resources are:
Deployment
DaemonSet
StatefulSet
ReplicaSet
Job
CronJob

2. Deployments: Managing Stateless Applications
A Deployment is the most common workload resource used in Kubernetes for stateless applications. It provides declarative updates to applications, meaning you can describe the desired state of your application, and Kubernetes will automatically manage the deployment to match that state.
Use Cases:
Stateless applications where replicas of a pod can be spread across nodes for load balancing.
Rolling updates and rollbacks for zero-downtime deployment.
Key Features:
Scaling: Automatically adjusts the number of replicas as required.
Rolling Updates: Kubernetes automatically rolls out updates to your application without downtime.
Rollback: If something goes wrong with the new deployment, you can easily roll back to the previous version.
Example:
To create a simple Deployment for a web application, you can use the following YAML:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-web-app
  template:
    metadata:
      labels:
        app: my-web-app
    spec:
      containers:
        - name: my-web-container
          image: my-web-app:latest
          ports:
            - containerPort: 8080

This YAML file describes a deployment with three replicas of the my-web-app container, ensuring high availability.

3. DaemonSets: Running One Pod per Node
A DaemonSet ensures that a copy of a specific pod is running on all (or some) nodes in the cluster. This is particularly useful for node-level services such as monitoring agents, log collectors, or networking tools that need to run on each node.
Use Cases:
Cluster-wide services that need to be run on every node (e.g., logging agents, monitoring agents).
Single-application per node deployment.
Key Features:
Ensures that each node gets a pod (or a set of pods).
Pods can be scheduled only on certain nodes by using node selectors, affinities, or taints and tolerations.
Automatically adds new pods to newly added nodes to the cluster.
Example:
To run a DaemonSet that deploys a logging agent on every node:
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
        - name: fluentd
          image: fluent/fluentd:v1.12-debian-1
          volumeMounts:
            - name: varlog
              mountPath: /var/log
      volumes:
        - name: varlog
          hostPath:
            path: /var/log

This example runs a logging agent (fluentd) on each node, ensuring that logs are collected across all nodes in the cluster.

4. StatefulSets: Managing Stateful Applications
A StatefulSet is used for stateful applications that require unique network identifiers, stable storage, and ordered deployment. It is ideal for applications like databases or distributed file systems that need persistent state across pod restarts.
Use Cases:
Stateful applications such as databases (e.g., MySQL, MongoDB), message queues (e.g., Kafka), or distributed file systems (e.g., GlusterFS).
Applications that require stable, persistent storage and network identity.
Key Features:
Stable, unique pod names: Each pod in a StatefulSet gets a unique, stable name (e.g., my-app-0, my-app-1).
Persistent storage: Allows each pod to be associated with a persistent volume claim (PVC).
Ordered deployment and scaling: Pods are created and terminated in a specific order.
Example:
A StatefulSet for running a stateful web application:
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-db
spec:
  serviceName: "my-db"
  replicas: 3
  selector:
    matchLabels:
      app: my-db
  template:
    metadata:
      labels:
        app: my-db
    spec:
      containers:
        - name: my-db-container
          image: my-db-image:v1
          volumeMounts:
            - name: db-data
              mountPath: /data/db
  volumeClaimTemplates:
    - metadata:
        name: db-data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi

This example ensures that each pod in the StatefulSet has its own persistent storage for database data.

5. CronJobs: Running Jobs on a Schedule
A CronJob is a workload resource that runs jobs on a scheduled basis, similar to a cron job in Unix-based systems. This is useful for tasks that need to be executed periodically, such as backups, report generation, or cleanup tasks.
Use Cases:
Periodic tasks like backups, database cleanups, or scheduled data processing.
Batch jobs that need to run at specific times or intervals.
Key Features:
Cron-like syntax for specifying the schedule (e.g., 0 0 * * * to run at midnight every day).
Job management: Automatically creates a new job at each scheduled time and ensures that the job completes successfully.
Concurrency policies: Control whether multiple jobs can run concurrently or if the next job should wait for the previous one to finish.
Example:
A CronJob that runs a backup every day at midnight:
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-cronjob
spec:
  schedule: "0 0 * * *"  # Run at midnight every day
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: backup-container
              image: backup-image
              command: ["/bin/sh", "-c", "backup.sh"]
          restartPolicy: OnFailure

This cron job runs a backup script every day at midnight.

6. Jobs: Running One-Time Tasks
A Job is a Kubernetes workload used for one-time, batch processing tasks that can be completed in the background, such as processing a queue, migrating data, or running a report.
Use Cases:
One-off tasks like database migrations or data processing.
Batch jobs that can run to completion and do not need to persist.
Key Features:
Completion tracking: Jobs are considered complete when the specified number of pods successfully terminate.
Parallel jobs: You can run multiple jobs concurrently or sequentially depending on your use case.
Example:
A Job for running a database migration:
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migration
spec:
  template:
    spec:
      containers:
        - name: migration-container
          image: migration-image
          command: ["/bin/sh", "-c", "migrate.sh"]
      restartPolicy: OnFailure
  backoffLimit: 4

This job will run a database migration script and retry up to 4 times in case of failure.

7. Conclusion: Choosing the Right Workload Resource
As a Kubernetes Application Developer (CKAD), choosing the correct workload resource for your application is a critical skill. Here's a summary of when to use each type:
Deployment: For stateless applications that need scaling and rolling updates.
DaemonSet: For running a pod on every node (e.g., monitoring, logging).
StatefulSet: For stateful applications requiring stable storage and identity.
CronJob: For running jobs on a scheduled basis (e.g., backups, periodic tasks).
Job: For one-off tasks that run to completion.
Selecting the correct workload resource helps optimize application performance, scalability, and maintainability, and ensures that your applications run efficiently in a Kubernetes cluster.
