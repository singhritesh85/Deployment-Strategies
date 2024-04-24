# Blue-Green Deployment

**Blue-Green Deployment** In Blue-Green Deployment there is an active version (older version) of application which is also called as **Blue**. The newer version of application is called as preview version (also called as **Green**) which will be tested first and if every thing goes well then newer version will replace the older version. In order to achieve this we need to promote the rollout.

**To demonstrate Blue-Green Deployment I have used ArgoCD and Argo Rollout.** For ingress controller I have used nginx ingress controller.
<br><br/>
For older image I have used httpd:2.4.55 and newer version of image I have used nginx:1.25.5. 
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/cc505e05-6544-428c-abb5-7d9a94a9b26e)
<br><br/>
                                           ![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/cf016119-3ac9-4f67-ba2c-c3a40afc2f7e)
<br><br/>
![image](https://github.com/singhritesh85/Deployment-Strategies/assets/56765895/9aad43b4-7b58-40fd-a461-2fb5cb9a28d7)
