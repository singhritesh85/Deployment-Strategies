# Rolling Update

**Rolling Update**  In Rolling Update deployment strategy either a new pod will be created or a pod will be deleted then a newer one will be created with newer version of application depending on maxSurge or maxUnavailable respectively. When you use **maxSurge** then this much number of newer pods will be created above the desired replicas and **maxUnavailable** represents maximum number of pods unavailable during an upgrade.
<br><br/>
You cannot keep **maxSurge** and **maxUnavailable** both equals to zero simultaneously. For the current demonstration I kept **maxSurge** and **maxUnavailable** equals to 1 which means during the rollingUpdate one additional pod will be created and one pod can be unavailable.
<br><br/>
I have created an older version of application using httpd:2.4.55 image and newer version of application using nginx:1.25.5 image.
<br><br/>
Nginx Ingress Controller has been installed as per the screenshots shown below.
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
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4407df58-0cbf-471d-b5e7-0fcdad6f1d84)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/36ce9802-802a-4f5a-9095-e222da8f990d)
<br><br/>
I have used ArgoCD to create Application.
<br><br/>
**Installation of ArgoCD**
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Get the Password of ArgoCD
===========================
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
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
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/ded841df-263d-42ce-ae65-beba2e8697e2)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/3a5011a1-2029-4c16-9659-d716771f2c21)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4ed8676c-092c-40ac-8dca-12d61b6c1283)
<br><br/>
Change the ArgoCD default admin user password
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/a15d3b8e-f8f7-422e-a113-c34b3ad560d4)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/9afd3e2e-8b5a-4823-a59c-d10afe9dbeee)
**Create the Application using ArgoCD as shown in the screenshot below**
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/0c9225ac-c61c-462a-b3c2-d9679f97bb00)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/cade8a29-1c4f-44be-aa03-3a1d62a19822)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/58da5ed3-4cd2-482a-bbc4-07f26ae29433)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/e5ad1b19-7a41-437b-94c0-5dff55ef5b47)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/3e8e62bb-ff69-499e-91d0-a8bcb8c2aac7)
