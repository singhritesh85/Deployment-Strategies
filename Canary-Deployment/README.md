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

Change the targetPort for https to 80 in nginx ingress controller service as written below:-
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
      targetPort: 80
```
