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
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/d1acabab-fce4-42da-bd79-4b8492977f92)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/73bc5beb-07d4-4e66-95bc-b89f4b8d4505)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/98b7d8f9-f3a9-4ee2-9ab4-dc0dadf1eed7)

<br><br/>
Generate ArgoCD password using the below command
<br><br/>
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
```
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/282efc15-f3fe-4fe6-b24d-18a0d560caaa)
<br><br/>
Change the ArgoCD password as shown below in screenshot
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4718e222-0a5e-4074-abf3-0fdd75f1a967)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/a339cee7-e103-4703-885e-c8fad7a84da0)
<br><br/>
Install Argo Rollout Controller
```
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```
<br><br/>
Install Argo Rollouts Kubectl plugin
```
curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x ./kubectl-argo-rollouts-linux-amd64
sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
```
<br><br/>
Initially kind: Rollout is not present on the EKS cluster but after Installation of Argo Rollout Controller and Argo Rollout Plugin it will present.
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/ff2f0213-5ef1-411d-b7bc-c976b7c7c065)
<br><br/>
Now Login with new password into ArgoCD GUI and create the application as shown in the screenshot below
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/0c3f15b3-e7a4-4a86-99a1-37cb221511f7)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/aac8b30d-aac1-4c94-8e0e-f6819a109b62)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f76afa59-e528-4489-9472-7645e4a8e29c)
<br><br/>
After Deploying Application through ArgoCD
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/492ee2ce-be53-4175-b17e-daa03e406d86)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/d6443f77-559d-425f-abca-bebb2bd97320)
<br><br/>
Test the Active Application using the URL
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/e6fb25a3-b766-4825-b348-ec46b8cb5ade)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/57dc29e7-03a4-4e1c-abc6-28d5435fc22e)
Now change the Image of the Application and use a new image nginx:1.25.5 as a new image.
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/eed481be-bac3-4b57-9138-9bd807d023cb)
<br><br/>
As the sync is Automatic in ArgoCD So changes in GitHub Repository will be deployed Automatically.
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/1a5a45fd-a419-45ba-ab7e-5ff3a5718cd4)
<br><br/>
Now test the newer version of Application using Preview Service. To do so change the service type as LoadBalancer instead of ClusterIP and after testing rechnage it to ClusterIP from LoadBalancer as shown in the screenshot below
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f55cf143-0b4c-4c26-95f2-df6000c7b76b)
<br><br/>
I am verified that I am able to access the newer version of Application as hown in the screenshot below
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/eee2c5f5-0631-4a52-8bd4-5448c002a531)
Now rechange the service type of preview service as ClusterIP from LoadBalancer
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/619d2512-0029-4dc1-9fa8-bf7baba795d7)
Finally Promote the rollout 
```
kubectl get ro -n <namespace>
kubectl argo rollouts get rollout <rollout-name> -n <namespace>
kubectl argo rollouts promote <rollout-name> -n <namespace>
```
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/c02a09b7-4829-4256-a9a9-e6ef0c6abebf)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4ecf38aa-d35f-4bcd-b318-79175c3a138f)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/7d55a0c7-5b1a-45c4-ab8a-4f995837af89)



