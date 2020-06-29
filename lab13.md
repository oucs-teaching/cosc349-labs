---
tags: cosc349
---
# COSC349 Lab 13—Cloud Architecture—2019
## Lab 13—Kubernetes and AWS orchestration with CloudFormation

This purpose of this lab is to provide you with some potentially interesting starting points for your future exploration of cloud technologies beyond COSC349.

## Kubernetes

### Installing Kubernetes

- There are many ways to run Kubernetes infrastructure on AWS, as discussed in lectures...
- ... however most of the more convenient approaches will not work using AWS Educate, due to missing permissions.
- It is, of course, possible create a complete Kubernetes infrastructure just using EC2 nodes, but this is not how developers would typically build such a system.

### Minikube

- We will use a special distribution of Kubernetes that is designed to be run on a single machine, in this case a VM provisioned on your computer using Vagrant.
- The installation of Minikube is not automated, so that you can watch it progress.
- Start by cloning the repository at: https://altitude.otago.ac.nz/cosc349/lab13-kubernetes
- Provision the VM using `vagrant up`.
- `vagrant ssh` into the VM.
- On the VM, run the following two commands to download and then install minikube within your VM:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

- Now try to start Minikube with the following command, which will fail, but will create a `.minikube` folder with the correct privileges.

```
minikube start --vm-driver=none
```

- Execute the command again, this time with root privileges.

```
sudo minikube start --vm-driver=none
```

- You should see the system downloading the Docker images that it uses to drive the components of Kubernetes. (This will take some time to complete. You can potentially read the second section of this lab on AWS CloudFormation while you wait...) After a number of minutes, the output should resemble:

```
😄  minikube v1.4.0 on Ubuntu 16.04 (vbox/amd64)
🤹  Running on localhost (CPUs=2, Memory=3951MB, Disk=9861MB) ...
ℹ️   OS release is Ubuntu 16.04.6 LTS
🐳  Preparing Kubernetes v1.16.0 on Docker 18.09.7 ...
💾  Downloading kubeadm v1.16.0
💾  Downloading kubelet v1.16.0
🚜  Pulling images ...
🚀  Launching Kubernetes ... 
🤹  Configuring local host environment ...

⚠️  The 'none' driver provides limited isolation and may reduce system security and reliability.
⚠️  For more information, see:
👉  https://minikube.sigs.k8s.io/docs/reference/drivers/none/

⚠️  kubectl and minikube configuration will be stored in /home/vagrant
⚠️  To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:

    ▪ sudo mv /home/vagrant/.kube /home/vagrant/.minikube $HOME
    ▪ sudo chown -R $USER $HOME/.kube $HOME/.minikube

💡  This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
⌛  Waiting for: apiserver proxy etcd scheduler controller dns
🏄  Done! kubectl is now configured to use "minikube"
```

- As noted above, some of the directories created will be owned by the wrong user, so repair the permissions by invoking:
```
sudo chown -R vagrant ~/.kube ~/.minikube
```

### Looking into your Kubernetes "cluster"

- You can now examine your Kubernetes cluster, using the `kubectl` command.

- List the nodes on your cluster (there should only be one):
```
kubectl get nodes
```

- List the pods currently deployed (there shouldn't be any in your default namespace):
```
kubectl get pods
```

- ... but there will be, if looking across all namespaces:
```
kubectl get pods -A
```

- You can thus confirm that a significant amount of software infrastructure is already running, as the core of the Kubernetes platform.

### Opening up a web dashboard

- Invoke the following command to start a web-dashboard. This invocation will occupy your SSH session, but you can open another terminal window and from it `vagrant ssh` in again.
```
sudo minikube dashboard
```
- After some delay, output such as the following should be presented:
```
🔌  Enabling dashboard ...
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
http://127.0.0.1:45089/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
- The URL will need to be opened on your host computer's web browser, which means that you will need to set up port forwarding. Since this is a dynamically-selected port, it is likely easiest if you open VirtualBox, and add a network forwarding rule from `127.0.0.1` on some port you know (e.g., `8000`) to `127.0.0.1` on whatever port is displayed in the output of the above command.

- After starting VirtualBox, the VM that Vagrant started is visible:

![](https://i.imgur.com/GJGBBDx.png)

- ... and from that running VM you can display the network configuration.

![](https://i.imgur.com/N8cutB8.png)

- Unfold the "Advanced" controls ...

![](https://i.imgur.com/lv8qVHR.png)

- ... and select the "Port Forwarding" button. From which you can add appropriate forwarding rules (note that the ports shown in this screen-capture are inconsistent from those in the console output above, but this matters little given that you need to substitute your specific parameter values anyway).

![](https://i.imgur.com/c0lYELx.png)

- Remember that you need to click "OK" not only on the port forwarding configuration, but also to click "OK" on the underlying networking configuration window before port forwarding rules are applied.

- You can then navigate in your web browser, to the address shown in the command output above, but ensure that the appropriate port number is substituted, i.e., the "Host Port" in your port forwarding rule.

- You should see a fully-featured dashboard for your (cut-down) Kubernetes cluster.

![](https://i.imgur.com/iohAwVU.png)

- For example, by clicking on "Nodes" in the left-hand side menu, you can see the information that the `kubectl` command provided previously:

![](https://i.imgur.com/s3DS6rL.png)


:::info
The dashboard is extremely powerful: do explore it, but for now we will proceed with a specific deployment exercise.
:::

### Deploying a Kubernetes service

- The instructions that follow are adapted from https://kubernetes.io/docs/tutorials/hello-minikube/

- First create a deployment of the hello-node application. The command that follows confirms that the deployment has been fetched.

```
vagrant@ubuntu-xenial:~$ kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
deployment.apps/hello-node created
vagrant@ubuntu-xenial:~$ kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   0/1     1            0           10s
```
- Looking at the pods will show that the hello-node is being created. 
```
vagrant@ubuntu-xenial:~$ kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
hello-node-7676b5fb8d-m7xc9   0/1     ContainerCreating   0          20s
```
- You can get a list of recent events:
```
vagrant@ubuntu-xenial:~$ kubectl get events
LAST SEEN   TYPE     REASON                    OBJECT                             MESSAGE
<unknown>   Normal   Scheduled                 pod/hello-node-7676b5fb8d-m7xc9    Successfully assigned default/hello-node-7676b5fb8d-m7xc9 to minikube
27s         Normal   Pulling                   pod/hello-node-7676b5fb8d-m7xc9    Pulling image "gcr.io/hello-minikube-zero-install/hello-node"
29s         Normal   SuccessfulCreate          replicaset/hello-node-7676b5fb8d   Created pod: hello-node-7676b5fb8d-m7xc9
29s         Normal   ScalingReplicaSet         deployment/hello-node              Scaled up replica set hello-node-7676b5fb8d to 1
40m         Normal   NodeHasSufficientMemory   node/minikube                      Node minikube status is now: NodeHasSufficientMemory
40m         Normal   NodeHasNoDiskPressure     node/minikube                      Node minikube status is now: NodeHasNoDiskPressure
40m         Normal   NodeHasSufficientPID      node/minikube                      Node minikube status is now: NodeHasSufficientPID
40m         Normal   RegisteredNode            node/minikube                      Node minikube event: Registered Node minikube in Controller
40m         Normal   Starting                  node/minikube                      Starting kube-proxy.

```

- Just running the containers is not enough to make the service available though. An invocation of the `expose` function of `kubectl` is required.

```
vagrant@ubuntu-xenial:~$ kubectl expose deployment hello-node --type=LoadBalancer --port=8080
service/hello-node exposed
```
- Listing the services will now confirm your hello-node service is present.
```
vagrant@ubuntu-xenial:~$ kubectl get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.108.192.141   <pending>     8080:31762/TCP   66s
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP          62m
```

- Let's try to connect to the `hello-node`. Although the following command will fail, it will tell you where you want to point your host machine's web browser.

```
vagrant@ubuntu-xenial:~$ minikube service hello-node
|-----------|------------|-------------|------------------------|
| NAMESPACE |    NAME    | TARGET PORT |          URL           |
|-----------|------------|-------------|------------------------|
| default   | hello-node |             | http://10.0.2.15:31762 |
|-----------|------------|-------------|------------------------|
🎉  Opening kubernetes service  default/hello-node in default browser...
browser failed to open url: exec: "xdg-open": executable file not found in $PATH
```

- You can set up port forwarding using VirtualBox in the same manner as above, and navigate your host computer's browser to a port that you have set up to reach the guest's IP and port number as shown in the output above.

- However, the service itself may not be ready: you can use the commands above to list services and events to get an idea of what the application deployment is up to.

- Eventually, you will be able to reach the test page:

![](https://i.imgur.com/eOpMClD.png)


### A stateless application using a deployment

- Now, without caring so much about the actual functionality of the containers being orchestrated, let's explore the power of the orchestration itself.
    - This material is adapted from https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/
- Examine https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment.yaml
- Deploy this application from the YAML description:

```
vagrant@ubuntu-xenial:~$ kubectl apply -f https://k8s.io/examples/application/deployment.yaml
deployment.apps/nginx-deployment created
```

- The output from this command has indicated the name of the deployment, i.e., `nginx-deployment`.
- You can use the `describe` function to find out more information:

```
vagrant@ubuntu-xenial:~$ kubectl describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 04 Oct 2019 04:12:07 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"default"},"spec":{"replica...
Selector:               app=nginx
Replicas:               2 desired | 2 updated | 2 total | 0 available | 2 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-54f57cf6bf (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  18s   deployment-controller  Scaled up replica set nginx-deployment-54f57cf6bf to 2
```
- Note the mention of a ScalingReplicaSet. The contests of that set can be viewed using the following command, indicating that it's just a pair of replicas.
```
vagrant@ubuntu-xenial:~$ kubectl get pods -l app=nginx
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54f57cf6bf-bvfrb   1/1     Running   0          48s
nginx-deployment-54f57cf6bf-qbskn   1/1     Running   0          48s
```

- If the status column does not show "Running" for all pods, then wait until all pods are "Running".

- An updated version of the same deployment is available that steps the nginx version from 1.7.9 to 1.8 at: https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment-update.yaml

- Apply this deployment, and watch how your pods change:

```
kubectl apply -f https://k8s.io/examples/application/deployment-update.yaml ;\
while true; do sleep 0.5; kubectl get pods -l app=nginx; done
```

- You will need to press <kbd>control</kbd><kbd>c</kbd> to stop this command. Here's an example of edited output:

```
vagrant@ubuntu-xenial:~$ kubectl apply -f https://k8s.io/examples/application/deployment-update.yaml; while true; do sleep 0.5; kubectl get pods -l app=nginx; done
deployment.apps/nginx-deployment configured
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54f57cf6bf-bvfrb   1/1     Running   0          4m31s
nginx-deployment-54f57cf6bf-qbskn   1/1     Running   0          4m31s
nginx-deployment-9f46bb5-xzp2r      0/1     Pending   0          1s
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-54f57cf6bf-bvfrb   1/1     Running             0          4m34s
nginx-deployment-54f57cf6bf-qbskn   1/1     Running             0          4m34s
nginx-deployment-9f46bb5-xzp2r      0/1     ContainerCreating   0          4s
...
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54f57cf6bf-qbskn   1/1     Running   0          5m53s
nginx-deployment-9f46bb5-hxqqh      1/1     Running   0          32s
nginx-deployment-9f46bb5-xzp2r      1/1     Running   0          83s
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-54f57cf6bf-qbskn   1/1     Terminating   0          6m
nginx-deployment-9f46bb5-hxqqh      1/1     Running       0          39s
nginx-deployment-9f46bb5-xzp2r      1/1     Running       0          90s
...
NAME                             READY   STATUS    RESTARTS   AGE
nginx-deployment-9f46bb5-hxqqh   1/1     Running   0          47s
nginx-deployment-9f46bb5-xzp2r   1/1     Running   0          98s
^C
```

- What you should see in the above, is that the new version of the deployment progressively replaced the containers from the old version of the deployment. This is exactly what is desirable, in order to upgrade or modify container-driven applications without causing service interruptions.

### Observe your work in the dashboard

- The above commands have—unsurprisingly—had significant effect in the Kubernetes dashboard.
- For example, selecting "Workloads" on the left-side menu shows all of the deployments, pods, etc.

![](https://i.imgur.com/47Oi9k0.png)

- (The following screen-shot is just content reached by scrolling down from content shown in the preceding screen-capture.)

![](https://i.imgur.com/CzVG451.png)

### Further deployments

:::success
Exercise:
- Using the dashboard or the command line, experiment with installing other Kubernetes deployments that you locate on the web.
- You may want to continue with the cloud orchestration section below, and return to this exercise.
:::

### Cleaning up

- When you are done with your experimentation, you can simply `vagrant destroy` the VM used to host Minikube, since there should be no problematic, persistent changes to your host computer's setup.

## Cloud orchestration

- We will use a template to deploy the WordPress content management system on EC2 using AWS CloudFormation, which is Amazon's cloud orchestration tool.

- From the AWS Console ...

![](https://i.imgur.com/VqSI9gk.png)

- ... navigate to the CloudFormation console.
- There you will see an empty list of "stacks". The term "stack" is in the sense of a deployed software stack.

![](https://i.imgur.com/2fuMdkC.png)

- Click on "Create stack", and select "Template is ready", and "Upload a template file".
- Using your host's web browser download https://raw.githubusercontent.com/awslabs/aws-cloudformation-templates/master/aws/solutions/WordPress_Single_Instance.yaml to a local file.
- Choose that file under the "Upload a template file" selector.
- The template file will undergo some preliminary checks, and when they are complete, you can click the "View in Designer" button.

![](https://i.imgur.com/kDyUnj2.png)

- The Designer provides a graphical and a YAML / JSON display of your template.

:::info 
In this case, if we were to run the template, WordPress wouldn't actually work, as the latest version of WordPress requires a later version of PHP than gets installed. We will use the designer to fix this, although of course could have edited the template file before uploading it to AWS CloudFormation.
:::

![](https://i.imgur.com/a7s5vON.png)

- Navigate to around line 471, which indicates that `/var/www/html` should be created from `http://wordpress.org/latest.tar.gz`.

![](https://i.imgur.com/qMMeE81.png)

- Downgrade this URL for the latest version of WordPress to version 5.1, using the URL: `https://wordpress.org/wordpress-5.1.2.tar.gz`

![](https://i.imgur.com/so6ASVB.png)

- The graphical display will note that it is out-of-sync, so feel free to click the top-right reload button.
- Then select the cloud icon with an up-arrow in it, to deploy your template.

![](https://i.imgur.com/mVvyPqB.png)

- You will be returned to the "Create stack" page.
- Click "Next".

![](https://i.imgur.com/FINyrUN.png)

- On the "Specify stack details" page, give your Stack a name.
- Fill out all of the other details that the template requires to be completed. Note that for parameters such as the database passwords, these are for you to control what gets deployed, so they can be any values that you will remember.

![](https://i.imgur.com/f8O6MKF.png)

- Scrolling down, you will see a "Next" button, which you should click when you have filled in all of the required form fields.

![](https://i.imgur.com/BIq9XNx.png)

- You will reach the "Stack options" page, but none of the defaults should need to be changed.

![](https://i.imgur.com/xBv0EuD.png)

- So scrolling down you will again see a "Next" button that you should click.

![](https://i.imgur.com/1e2HLxY.png)

- You will reach the Review page for your CloudFormation Stack.

![](https://i.imgur.com/UekCBTl.png)

- ... and again scrolling down, you should not need to change any of the parameters.
- You can click the "Create stack" button.

![](https://i.imgur.com/gqY36gt.png)

- This will return you to the "Stack details" page, where you can watch the deployment begin.

![](https://i.imgur.com/JncSDWA.png)

- After a short amount of time, more events will appear, until the page announces "CREATE_COMPLETE".
- From looking at the template, you can see that some outputs are specified.
- Click on the "Outputs" tab to see the outputs that were generated.

![](https://i.imgur.com/EZ4y1dc.png)

- In particular, we see the URL of the webserver that has been provisioned and configured, as key "WebsiteURL".

![](https://i.imgur.com/IljCiiU.png)

- Click on that link, and you should reach your EC2 instance's WordPress configuration page.

![](https://i.imgur.com/D7m98ym.png)

- Feel free to experiment with WordPress, but when you are done return to the CloudFormation page and choose "Delete" for your WordPress Stack.

![](https://i.imgur.com/eGXBqzx.png)

- After you confirm your request to "Delete stack" you will be returned to the "Stack details" page, where you can watch your resources being de-provisioned.

![](https://i.imgur.com/hSwC75Z.png)

- You should ensure that the Stacks count returns to 0, so that you are not being charged for services that you are not using.

![](https://i.imgur.com/mEymc4R.png)

:::success
Exercise:
- Experiment with deploying other CloudFormation templates.
- Make sure that you delete the "stack" after you have installed it, however, or services that you orchestrate will continue to run.
:::


