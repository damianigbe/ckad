CKAD: Discover and Use Resources That Extend Kubernetes (CRDs, Operators)
As a Kubernetes Application Developer (CKAD), you will often need to extend the native capabilities of Kubernetes to meet the specific needs of your application or workload. This is where Custom Resources (CRDs) and Operators come into play. These resources allow you to customize and automate application management, creating a richer, more flexible Kubernetes ecosystem.
In this section, we'll explore how to discover and use Custom Resource Definitions (CRDs) and Operators, and how they help extend Kubernetes functionality.

1. What Are Custom Resources (CRDs)?
A Custom Resource (CR) is an extension of the Kubernetes API that allows you to add your own API objects, outside of the standard built-in Kubernetes resources (such as Pods, Deployments, and Services).
Custom Resource Definitions (CRDs): A CRD is a way to define your own custom resources in Kubernetes. Once a CRD is created, you can manage instances of the custom resource just like any other Kubernetes object (e.g., Pods, Services, etc.) using kubectl.


Example use case: You might create a custom resource for a database configuration (e.g., DatabaseConfig) or for an application-specific resource like a CacheCluster.

2. How to Discover and Use CRDs
a) Checking for Available CRDs in Your Cluster
To discover available CRDs in your cluster, you can use the following kubectl command:
kubectl get crds

This will list all the custom resource definitions that are available in the cluster.
b) Creating a Custom Resource Definition (CRD)
To extend Kubernetes with a new custom resource, you first need to define the CRD. A CRD is simply a YAML file that specifies the kind, metadata, and specifications of the custom resource.
Here is an example of a CRD definition for a custom resource called CacheCluster:
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: cacheclusters.example.com
spec:
  group: example.com
  names:
    kind: CacheCluster
    listKind: CacheClusterList
    plural: cacheclusters
    singular: cachecluster
  scope: Namespaced
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              size:
                type: integer
                description: "Size of the cache"
                example: 3
              engine:
                type: string
                description: "Cache engine (e.g., Redis)"
                example: "redis"

Kind: Defines the custom resource’s name (CacheCluster).
Scope: Defines whether the resource is namespaced or cluster-scoped. In this case, it's namespaced.
Spec: Specifies the fields the resource will have, such as size and engine in the example.
Once the CRD is defined, you apply it to your Kubernetes cluster:
kubectl apply -f cachecluster-crd.yaml

After the CRD is applied, Kubernetes will start recognizing the custom resource.
c) Creating and Managing Custom Resources
Once the CRD is applied, you can create instances of your custom resource (in this case, CacheCluster):
apiVersion: example.com/v1
kind: CacheCluster
metadata:
  name: my-cache-cluster
spec:
  size: 3
  engine: redis

Save this as my-cache-cluster.yaml and apply it to your cluster:
kubectl apply -f my-cache-cluster.yaml

You can now manage this resource like any other Kubernetes resource:
Get the resource:

 kubectl get cacheclusters


Describe the resource:

 kubectl describe cachecluster my-cache-cluster


Delete the resource:

 kubectl delete cachecluster my-cache-cluster



3. What Are Kubernetes Operators?
An Operator is a method of packaging, deploying, and managing a Kubernetes application. It uses Custom Resources (CRs) to extend the Kubernetes API and make the application management more automated, handling lifecycle events such as installation, scaling, backups, and upgrades.
Operators are typically written as controllers (which watch for changes to resources and take actions accordingly). They are very useful for managing stateful applications that have complex lifecycle management needs.

4. How Operators Work with CRDs
Operators use CRDs to define the resources they manage. The operator watches for changes to these CRDs, and when a change occurs, it takes actions to reconcile the desired state of the resource with the actual state. For example, if you have a CacheCluster custom resource, an operator would ensure that the right number of Redis instances are running and automatically scale them up or down based on the size field in the CacheCluster spec.

5. How to Discover, Install, and Use Operators
a) Discovering Available Operators
You can discover available Operators through OperatorHub, a catalog of Kubernetes Operators. To browse OperatorHub, visit the OperatorHub.io website.
Additionally, many Kubernetes distributions like OpenShift or Rancher have integrated operator catalogs.
b) Installing Operators
Operators are typically installed as Deployments or StatefulSets within a cluster. You can install Operators either manually by applying a YAML file or through Helm.
Example Installation via YAML: Suppose you want to install a Redis operator, which can manage Redis clusters using the RedisCluster custom resource. First, find the Operator's installation YAML (often provided in the official documentation of the Operator).

 You can install the Redis operator with:

 kubectl apply -f redis-operator.yaml


Example Installation via Helm: If the operator is available as a Helm chart, you can install it using Helm:

 helm install redis-operator stable/redis-operator


c) Using Operators
Once the operator is installed, you can create instances of the custom resource it manages. For example, if you're using a Redis operator, you might define a RedisCluster custom resource to create a Redis cluster:
apiVersion: redis.k8s.io/v1
kind: RedisCluster
metadata:
  name: my-redis-cluster
spec:
  size: 3
  redisVersion: "6.0.5"

Apply it using:
kubectl apply -f my-redis-cluster.yaml

The operator will then create the necessary Redis Pods and manage the cluster lifecycle automatically.
d) Updating Operators and Custom Resources
Updating the Operator: To update the operator, you would update the Deployment or StatefulSet running the operator, either through a new version of the YAML or a Helm chart.


Updating the Custom Resource: To update a custom resource (e.g., RedisCluster), modify the resource definition and apply the changes using kubectl apply.



6. Benefits of CRDs and Operators
Automated Management: Operators enable the automation of complex application lifecycle management tasks such as installation, configuration, backup, scaling, and upgrades.
Custom Resource Management: CRDs allow you to define custom resources that meet the unique needs of your application.
Seamless Integration with Kubernetes: Both CRDs and Operators are first-class citizens in the Kubernetes ecosystem. They integrate seamlessly into Kubernetes’ native management tools (kubectl, Helm, etc.).
Declarative Model: Both CRDs and Operators use the declarative model of Kubernetes, which ensures that the desired state of the application is continuously maintained by the Kubernetes controller.

7. Conclusion
In this section, we have covered how to discover and use resources that extend Kubernetes, specifically focusing on Custom Resource Definitions (CRDs) and Operators.
CRDs allow you to create custom resources that extend Kubernetes’ capabilities and define your own objects for managing applications.
Operators provide an automated way to manage the lifecycle of complex applications by leveraging CRDs to handle the installation, configuration, scaling, and maintenance of stateful applications.
Mastering these concepts will significantly enhance your ability to work with Kubernetes, enabling you to build more flexible, scalable, and automated systems that go beyond the built-in Kubernetes resources.
