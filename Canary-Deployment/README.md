# ![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/84d55fb3-718b-4c91-a44a-6edc9265e1e3)Canary Deployment

**Canary Deployment** In Canary deployment you can split the traffic between newer version of application and older version of application. If you satisfied with the newer version of application then you can promote the rollout to newer version of application.
<br><br/>
I have created EKS Cluster using the terraform script https://github.com/singhritesh85/terraform-eks-withaddons.git with keeping the desired, min and max node count as 4.
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/679a10e4-ce04-4ae4-a60a-d082b07ad4f6)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/bfa079a1-0fe0-4820-b24c-65d8bc5c8fca)
<br><br/>
**To demonstrate Canary Deployment I have used ArgoCD and Argo Rollout.** For ingress controller I have used nginx ingress controller.
<br><br/>
For older image I have used nginx:1.25.5 and newer version of image I have used httpd:2.4.55. 
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
```
Ingress Rule for ArgoCD
=============================
vim ingress-rule.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  namespace: argocd
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"    ###  Using this annotation, you can Access ArgoCD on port http and https
spec:
#  ingressClassName: nginx
  rules:
  - host: argocd.singhritesh85.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server   ### Provide your service Name
            port:
              number: 80   
```
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8744fba8-07f7-4678-8b4f-7ad82dd629d9)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/960df167-878f-40b0-9e38-5ac9a841da05)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/d7b6f095-4847-4a45-b3b4-7f8513ce32db)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/8b557163-7bcb-43ff-900a-4196e0422442)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/5484999e-c8d2-437b-ac8a-28b176b87f9c)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/5bff1224-72fa-4cac-bba4-0d238c23bdce)
<br><br/>
Change the ArgoCD default admin password
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/1662f989-e0e0-4c6e-b34e-d613fef57742)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/9dbe4502-4276-456b-b675-126b44278142)
<br><br/>
**Install Argo Rollout Controller**
```
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```
**Install Argo Rollouts kubectl plugin**
```
curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x ./kubectl-argo-rollouts-linux-amd64
sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
```
<br><br/>
**Create the Application in ArgoCD as shown in the screenshot below**
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/c8fc6a2b-a5fb-4492-be2e-89dc3f6f9c52)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/65b635be-0d18-4c24-b130-38899b5ae15a)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/d2f57810-8bdc-4577-b77d-89e82e6a1031)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/b87f8967-3db3-4b04-b1d5-3993a07a9341)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/dd16329d-39bd-4495-85c3-8a545cbe59d3)
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/2faf32aa-87b9-40f2-809f-ed5a2a5fa404)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/3a8accc2-8f04-4203-b0f1-fb67f9b1801c)
<br><br/>
**Now Changed the Image to httpd:2.4.55 from older image nginx:1.25.5, In ArgoCD while creation of Application sync policy is chosen as Automatic. So when the image is changed in yaml then it will automatically reflects in Application's new version.**
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/506b4e2d-e85f-4a6f-bcef-2b7867266cb4)
<br><br/>
**Promote the Rollout**
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f1e72c71-f940-4654-afd5-51e5ca5a9b67)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/63277083-8064-4908-bffa-1edb999dda2c)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/af4dbfe3-555d-4115-9bee-cbba09ab8674)
<br><br/>
**Now new Application reflects when accessing the URL**
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/fb386350-470d-4992-82f8-d8ea2280de9e)


**Reference**=https://argo-rollouts.readthedocs.io/en/stable/features/canary/
