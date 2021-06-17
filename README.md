*Kubernetes Deployments 
##Septs for AWS
1. Setup AWS CLI ( [Follow Online Doc](https://docs.aws.amazon.com/polly/latest/dg/setup-aws-cli.html) )
1. Create AWS ECR ([Elastic Container Registery](https://docs.aws.amazon.com/ecr/index.html))
	- Create individual repo
	- Create local images from /dcafe-ai/docker
	- Push all image to [ECR](https://console.aws.amazon.com/ecr/repositories)
1. Create AWS EKS ([Amazon Elastic Kubernetes Service](https://docs.aws.amazon.com/eks/index.html))
	- Create Cluster ([Doc](https://eksctl.io/usage/creating-and-managing-clusters/) )
  1. Create ClusterConfig Yaml (/dcafe-ai/kubernetes/cluster_config/cluster.yaml)
  1. Install eksctl [Link](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)
  1. Apply Cluster Config
  ```
  eksctl create cluster -f cluster.yaml
  ```
1. Create config map for all service. Check and verify configmap name
  - Command to create configmap.
  - Use individual config map for all individual service using respective config file or create custom configmap and secrets
  ```
  kubectl create configmap config_map_name --from-file config_file_path
  kubectl create configmap front-config-yaml --from-file dcafe-ai/src/frontend/config/config.yaml
  kubectl create configmap ingestion-config-yaml --from-file dcafe-ai/src/ingestion/config/config.yaml
  kubectl create configmap rc2-config-yaml --from-file dcafe-ai/src/rc_2_detection/config/config.yaml
  kubectl create configmap rc3-config-yaml --from-file dcafe-ai/src/rc_3_recognition/config/config.yaml
  kubectl create configmap rc4-config-yaml --from-file dcafe-ai/src/rc_4_matcher/config/config.yaml
  kubectl create configmap rc5-config-yaml --from-file dcafe-ai/src/rc_5_collector/config/config.yaml
  ```
1. Deployments
	- Use the deployment template for all service. /dcafe-ai/kubernetes
	- Check and update storage required for server in deployment file
	- Follow online doc for details. [kubernetes and aws doc](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

1. Individaul Service Deployment
  1. Frontend Service ( Deployment yaml)
    - Create Service yaml /dcafe-ai/kubernetes/frontend/deployemnt.yaml
    - Verify or update port and selector ( App: )
    - Verify or update other deployment details on same file. ( Follow yaml doc for more info )
    - Apply frontend deployment
    ```
    kubectl apply -f ./kubernetes/forntend/deployment.yaml
    ```
  1. Ingestion Service ( Deployment yaml )
    - Deployment yaml /dcafe-ai/kubernetes/ingestion/deployemnt.yaml contain service and deployment both
    - Verify or update service port, selecter app label etc for both service and deployment
    - Apply deployment

  1. For rc2,rc3 apply their deployment file same as frontend and ingestion
    - Service yaml is not required for these rc2 and rc3
  1. Rc4 verify,update and apply service and deployment same as above.

  1. ETCD deployment
    - ETCD uses gcr.io/etcd-development/etcd:v3.4.16 image
    - Use same template for deployemnt /dcafe-ai/kubernetes/etcd-deployment.yaml
    - Verify or update port,storage for both StatefulSet and service yaml
    - Apply deployment yaml

  1. MySql deployment
    - In this case we are using custom local build docker images for mysql deployment. You can use docker hub repo as well.
    - Use the given templates inside project kubernetes folder
    - Verify and update storage or PersistentVolume required for mysql.
    - Apply the deployment yaml. Template contain service,PersistentVolumeClaim and deployment yaml on single file.
    - Done
  1. Redis deployment
    - In this case we are using dedicated and seperate node for redis deployment.
    ```
      nodeSelector:
        alpha.eksctl.io/nodegroup-name: redis
    ```
    - You can do it same by updating nodeSelector
    - Use the given template for reference. Follow online doc for more yaml details.
    - Update and Verify port, app selector for both both service and deployment yaml
    - Apply deployment
  1. Expose service as public
    - To expose these service to outside world you can use any of these service type.
    ```
    NodePort
    LoadBalancer
    ExternalName
    ```
    - Follow online doc for more info. [Link](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport)
    - In this case, for demo NodePort service type is used for rc-5. NodePort Service Yaml (/dcafe-ai/kubernetes/rc5/nodeport.yaml)
    - Note : You must have to change nodes security group's inbound rule to allow incoming connection on service ports. Example port 30000.
  1. Some usefull kubernetes commads
    ```
    List Nodes        : kubectl get nodes
    List Config Map   : kubectl get cm
    Edit Config Map   : kubectl edit cm configmap_name
    Describe CM       : kubectl describe cm cm_name
    List Pods         : kubectl get pods
    Describe Pods     : kubectl describe pod_name
    Pod Log           : kubectl logs -f pod_name
    Delete Pod        : kubectl delete pod pod_name
    Delete Deployment : kubectl delete -f deployent.yaml
    ```

  Note: Doc link and some folder might be change in future.
  Thanks
