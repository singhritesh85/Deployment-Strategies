# ![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/84d55fb3-718b-4c91-a44a-6edc9265e1e3)Canary Deployment

**Canary Deployment** In Canary deployment you can split the traffic between newer version of application and older version of application. If you satisfied with the newer version of application then you can promote the rollout to newer version of application.
<br><br/>
I have created EKS Cluster using the terraform script https://github.com/singhritesh85/terraform-eks-withaddons.git with keeping the desired, min and max node count as 4.
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/679a10e4-ce04-4ae4-a60a-d082b07ad4f6)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/bfa079a1-0fe0-4820-b24c-65d8bc5c8fca)
<br><br/>
**To demonstrate Canary Deployment I have used ArgoCD and Argo Rollout.** For ingress controller I have used nginx ingress controller.
<br><br/>
For older image I have used httpd:2.4.55 and newer version of image I have used nginx:1.25.5. 
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/5ddd1f70-03e3-45e9-bb50-dc2417efcda9)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/fb534bb6-8c2b-4843-995a-fe60547b5ced)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8b3b3074-7924-4b03-b4ee-f0300732eee6)
