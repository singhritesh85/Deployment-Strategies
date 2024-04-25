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
<br><br/>
After creation of EKS Cluster I have installed nginx ingress controller and ArgoCD. Created ingress rule for ArgoCD as shown in screenshot below.
**Installation of Nginx Ingress Controller**
```
kubectl create ns ingress-nginx
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx
```
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/2a166758-4f6d-47f9-b497-147e4df26b6c)
<br><br/>
Edit the nginx ingress controller service and provide ARN of Amazon Certificate Manager and change the tagetPort from https to http as shown in the screenshot below.
```
kubectl get svc -n ingress-nginx
kubectl edit svc ingress-nginx-controller  -n ingress-nginx

Change the targetPort from https to http in nginx ingress controller service as written below:-
-------------------------------------------------------------------------------------------------------------------------------
Before:

  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
After:

  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: http
```
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/0e4d62be-99ab-4200-8cfc-eeaef5ec8fa2)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/b89b614b-06e7-49cb-bf82-989ee97f7378)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/b006d290-84e0-4db6-8e20-48a3839987cd)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8a03ac6d-d943-4406-9851-507a2cc869fd)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/7618170b-d02b-4826-a846-7e8fad451bda)
<br><br/>
**Installation of ArgoCD**
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Get the Password of ArgoCD
===========================
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8744fba8-07f7-4678-8b4f-7ad82dd629d9)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/46312643-f863-4e1a-9bf2-901e27b65558)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/d7b6f095-4847-4a45-b3b4-7f8513ce32db)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8b557163-7bcb-43ff-900a-4196e0422442)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/5484999e-c8d2-437b-ac8a-28b176b87f9c)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/5bff1224-72fa-4cac-bba4-0d238c23bdce)


