# Blue-Green Deployment
<br><br/>
**Blue-Green Deployment** In Blue-Green Deployment there is an active version (older version) of application which is also called as **Blue**. The newer version of application is called as preview version (also called as **Green**) which will be tested first and if every thing goes well then newer version will replace the older version. In order to achieve this we need to promote the rollout.
<br><br/>
I have create EKS Cluster using the terraform script https://github.com/singhritesh85/terraform-eks-withaddons.git with keeping the desired, min and max node count as 4.
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f2bbbd0d-1529-4a47-9829-51187db434a5)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/29a92a2a-84ae-442a-81ee-180e7ab070e7)
<br><br/>
**To demonstrate Blue-Green Deployment I have used ArgoCD and Argo Rollout.** For ingress controller I have used nginx ingress controller.
<br><br/>
For older image I have used httpd:2.4.55 and newer version of image I have used nginx:1.25.5. 
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/642539d6-b954-482a-bd7d-19e76a2bb561)
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/2bd0ffa0-a910-4315-8831-49f818926d0c)
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/7568dff2-8ce2-43fa-ab92-bc85bf04215d)
<br><br/>
**Install Nginx Ingress Controller**
<br><br/>
```
kubectl create ns ingress-nginx
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx
```
edit the service for nginx-ingress-controller and provide the ARN of Amazon Certificate Manager in annotations, chnage the targetPort as http instead of https as shown in the screenshot below.
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/58721827-5643-4632-9c43-61312deeec70)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/1840cded-0245-45fa-87d9-7a33ad051b5d)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/513b2a7d-b20c-41f8-bf3a-c9696376334d)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/148f9dba-2a52-444e-900a-e16e12f5c81a)
<br><br>
Install Argocd as shown in the screenshot below
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f405a881-96bc-44fc-9858-dc4b32545363)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/ec11029c-5c50-4221-bd45-8813a58dd20e)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/73bc5beb-07d4-4e66-95bc-b89f4b8d4505)
