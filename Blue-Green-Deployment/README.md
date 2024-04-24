# Blue-Green Deployment

**Blue-Green Deployment** In Blue-Green Deployment there is an active version (older version) of application which is also called as **Blue**. The newer version of application is called as preview version (also called as **Green**) which will be tested first and if every thing goes well then newer version will replace the older version. In order to achieve this we need to promote the rollout.

To demonstrate Blue-Green Deployment I have used ArgoCD and Argo Rollout. For ingress controller I have used nginx ingress controller.<Shift+Enter>For older image I have used httpd:2.4.55 and newer version of image I have used nginx:1.25.5. 

