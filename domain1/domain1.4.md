
CKAD: Utilize Persistent and Ephemeral Volumes
In Kubernetes, volumes are essential for providing storage to containers within Pods. Containers are typically ephemeral, meaning that when a container is terminated or rescheduled, any data stored in its filesystem is lost. To manage data more effectively, Kubernetes offers two primary types of volumes: ephemeral and persistent.
As a Kubernetes Application Developer (CKAD), understanding when and how to use these different types of volumes is crucial for building scalable and resilient applications. This section will explain the difference between ephemeral and persistent volumes, provide use cases, and show how to configure them.

1. Ephemeral Volumes
Ephemeral volumes are temporary storage that exists for the duration of a Pod. Once the Pod is deleted, the data stored in these volumes is also deleted. Ephemeral volumes are ideal for scenarios where data persistence is not required or where data can be re-created easily (e.g., temporary files, caches, or scratch space).
Common Ephemeral Volume Types:
emptyDir: A simple empty directory that is created when a Pod is scheduled on a node and exists as long as the Pod is running.
configMap: Stores configuration data that can be injected into Pods as files.
secret: Stores sensitive information, such as passwords or tokens, which are injected into Pods.
downwardAPI: Provides metadata about the Pod, such as labels, annotations, or resource usage metrics.
projected: A volume that aggregates multiple sources into one volume, such as a mix of secrets, config maps, or downward API data.
Use Cases for Ephemeral Volumes:
Temporary files: Storing files that don’t need to persist beyond the life of a Pod (e.g., logs, caches).
Configuration data: Using configMap or secret volumes to inject configuration files or environment variables into Pods.
Shared memory: Using emptyDir for sharing files between containers within the same Pod (e.g., a web server and a sidecar container).
Example: emptyDir Volume
Here’s an example of using an emptyDir volume in a Pod to store temporary data:
apiVersion: v1
kind: Pod
metadata:
  name: ephemeral-volume-example
spec:
  containers:
    - name: main-container
      image: busybox
      command: ["sh", "-c", "echo 'Hello World' > /data/hello.txt; sleep 3600"]
      volumeMounts:
        - mountPath: /data
          name: emptydir-volume
  volumes:
    - name: emptydir-volume
      emptyDir: {}

In this example:
The emptyDir volume is created for the Pod, and the main container writes data to the /data directory.
The data stored in emptyDir will only exist for as long as the Pod is running. If the Pod is deleted, the data is lost.

2. Persistent Volumes (PVs)
Persistent Volumes (PVs) are storage resources in the Kubernetes cluster that exist independently of the Pod lifecycle. Unlike ephemeral volumes, persistent volumes are designed to store data that needs to persist beyond the life of a Pod. This makes them ideal for applications that require durable storage, such as databases or file storage.
Persistent Volumes can be backed by various storage systems such as local disks, cloud-based block storage (e.g., AWS EBS, GCE Persistent Disk), or network-attached storage (e.g., NFS, GlusterFS).
Key Concepts for Persistent Volumes:
PersistentVolume (PV): A resource in the cluster that represents a piece of storage. It’s provisioned by an administrator or dynamically provisioned using StorageClasses.
PersistentVolumeClaim (PVC): A request for storage by a user/application. A PVC is bound to a PV that meets its storage requirements.
StorageClass: Defines the type of storage to be used (e.g., standard, SSD, NFS) and allows for dynamic provisioning of PVs.
Use Cases for Persistent Volumes:
Databases: Storing the data of stateful applications, such as databases, that require persistent storage.
Shared storage: Enabling multiple Pods to access the same storage for read/write purposes (e.g., file sharing).
Long-term data storage: For applications that need to store user data, logs, or backups.
Example: PersistentVolume and PersistentVolumeClaim
Here’s an example that demonstrates how to use a PersistentVolume (PV) and PersistentVolumeClaim (PVC):
PersistentVolume (PV) definition:
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: /mnt/data

In this example:
The hostPath volume type uses the /mnt/data directory on the node as the storage backend for the PV.
The accessModes field defines that the volume can be read and written by a single node at a time (ReadWriteOnce).
persistentVolumeReclaimPolicy: Retain indicates that when the PVC is deleted, the PV itself will not be deleted, allowing manual cleanup.
PersistentVolumeClaim (PVC) definition:
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: standard

The PVC specifies a storage request of 1Gi and is bound to a PV that can fulfill these requirements.
The accessModes and storageClassName should match the PV’s definition.
Pod Using PVC:
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  containers:
    - name: main-container
      image: busybox
      command: ["sh", "-c", "echo 'Persistent storage' > /mnt/data/storage.txt; sleep 3600"]
      volumeMounts:
        - mountPath: /mnt/data
          name: pvc-storage
  volumes:
    - name: pvc-storage
      persistentVolumeClaim:
        claimName: my-pvc

In this setup:
The Pod uses a PersistentVolumeClaim (PVC) to request storage from the PV.
The container writes data to /mnt/data/storage.txt, which is backed by the persistent volume.

3. Difference Between Ephemeral and Persistent Volumes
Feature
Ephemeral Volumes
Persistent Volumes
Lifetime
Tied to the lifecycle of a Pod.
Exists independently of Pods and can persist beyond Pod lifecycle.
Use Case
Temporary storage for caches, logs, or scratch data.
Durable storage for stateful applications like databases.
Data Loss
Data is lost when the Pod is deleted.
Data persists even if the Pod is deleted.
Backends
Typically local (e.g., emptyDir, configMap, secret).
Can be backed by cloud storage, NFS, or other persistent storage solutions.
Access Modes
Read/write for a single Pod/container.
Access modes like ReadWriteOnce, ReadOnlyMany, or ReadWriteMany for multiple Pods.


4. Best Practices for Using Volumes
Use Ephemeral Volumes for Temporary Data: If your application generates temporary data that doesn’t need to persist after the Pod terminates, use ephemeral volumes (e.g., emptyDir, configMap, or secret).
Use Persistent Volumes for Stateful Applications: For applications that require data persistence (e.g., databases, logging systems), use persistent volumes backed by cloud storage or networked file systems.
Claim Resources Efficiently: When using Persistent Volumes, ensure that your PVCs request appropriate storage resources based on your application’s needs, and be mindful of storage classes and capacity.
Security Considerations: When using sensitive data, consider using secret volumes for injecting secrets (e.g., passwords, API keys) into Pods securely.

5. Conclusion
As a Kubernetes Application Developer (CKAD), understanding how to use both ephemeral and persistent volumes is critical for building applications that are both scalable and resilient. By using ephemeral volumes for temporary data and persistent volumes for data that needs to be stored long-term, you can ensure your applications are both performant and reliable.
Make sure to:
Use ephemeral volumes for temporary data that doesn’t need to survive beyond the Pod.
Use persistent volumes for stateful applications or when you need data persistence across Pod restarts or node failures.
Proper volume management is a key component of Kubernetes application development, and mastering it will help you build efficient and effective cloud-native applications.


