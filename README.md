# Deployment-Strategies

For the demonstration I have created the Kubernetes Cluster using EKS and with the help of terraform script **terraform-eks-withaddons** present in my github repository https://github.com/singhritesh85/terraform-eks-withaddons.git.

Following are the mainly used deployment strategies followed in DevOps practices.
```
(a) Recreate
(b) Rolling Update
(c) Blue-Green Deployment
(d) Canary Deployment
```
**(a) Recreate**  In recreate deployment strategy first of all the older version of application will be deleted and then newer version of application will be created. This deployment strategy will suffer a downtime and hence it is not suggestable for production environment. However this deployment strategy is relatively faster so you can prefer it for UAT/Stage environment.
<br><br/>
**(b) Rolling Update**  In Rolling Update deployment strategy either a new pod will be created or a pod will be deleted then a newer one will be created with newer version of application depending on maxSurge or maxUnavailable respectively. When you use **maxSurge** then this much number of newer pods will be created above the desired replicas and **maxUnavailable** represents maximum number of pods unavailable during an upgrade.
<br><br/>
**(c) Blue-Green Deployment** In Blue-Green Deployment there is an active version (older version) of application which is also called as **Blue**. The newer version of application is called as preview version (also called as **Green**) which will be tested first and if every thing goes well then newer version will replace the older version. In order to achieve this we need to promote the rollout.
<br><br/>
**(d) Canary Deployment** In Canary deployment you can split the traffic between newer version of application and older version of application. If you satisfied with the newer version of application then you can promote the rollout to newer version of application.
<br><br/>
```
You can use Rolling Update, Blue-Green Deployment or Canary Deployment for Production Environment.
```
