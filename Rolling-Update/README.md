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
