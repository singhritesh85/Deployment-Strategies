# Rolling Update

**Rolling Update**  In Rolling Update deployment strategy either a new pod will be created or a pod will be deleted then a newer one will be created with newer version of application depending on maxSurge or maxUnavailable respectively. When you use **maxSurge** then this much number of newer pods will be created above the desired replicas and **maxUnavailable** represents maximum number of pods unavailable during an upgrade.
<br><br/>
You cannot keep **maxSurge** and **maxUnavailable** both equals to zero simultaneously. For the current demonstration I kept **maxSurge** equals to 1 which means during the rollingUpdate one additional pod will be created after it comes in Running state one already running pod from the previous version of application will be Terminated. In this way Rolling Update works.
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
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4030e584-43a6-435d-9782-59feb9e19d70)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/3e8e62bb-ff69-499e-91d0-a8bcb8c2aac7)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/803f67ef-4b2c-4e03-acc2-9a22463db42a)
<br><br/>
Finally Able to access the application using the URL.
<br><br/>

![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4c40048d-4817-4069-b62d-e0777003dfdc)
<br><br/>
Now change the Image from httpd:2.4.55 to nginx:1.25.5 and observe how Rolling Update spin up the pods. As the sync policy is kept Automatic for this Application in ArgoCD and hence these changes will be automactically deployed to created newer version of application.
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/4d04e754-505f-4df4-8d85-59e3a03d775d)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/07b1be1d-ef3d-44f6-ba12-8929df8d6d91)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/81ba569b-ade0-409a-ab80-32dfc8703f4b)
