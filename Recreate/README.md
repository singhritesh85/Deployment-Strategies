# Recreate Deployment Strategy
**Recreate** In recreate deployment strategy first of all the older version of application will be deleted and then newer version of application will be created. This deployment strategy will suffer a downtime and hence it is not suggestable for production environment. However this deployment strategy is relatively faster so you can prefer it for UAT/Stage environment.
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
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/36403ea1-1109-4639-9753-dbfef472293c)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/eb948388-f510-4e85-b0c4-b3eec2feca3a)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/f3c6842e-8b0e-4dcc-bfc6-cf11c5b8f64b)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/c029ccc4-263a-41ca-a552-68bb5706417a)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/34d41c54-8466-4436-9d9e-0c772990c7a0)
<br><br/>
Finally Able to access the application using the URL.
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/2bea0613-c483-42e1-b0a6-f81d74039abb)
<br><br/>
Now change the Image from httpd:2.4.55 to nginx:1.25.5 and observe how Recreate deployment strategy spin up the pods. As the sync policy is kept Automatic for this Application in ArgoCD and hence these changes will be automactically deployed to created newer version of application.
<br><br/>
**You will find first of all all the older application pods will be deleted and the newer application pods will be created.**
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/49b8d301-5940-4fa4-88e1-10ac38a15da5)
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/61e2663b-6b21-4498-ac63-60fcc2a3cb2c)

