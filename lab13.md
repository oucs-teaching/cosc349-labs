---
tags: cosc349
---
# COSC349 Lab 13—Cloud Architecture—2020
## Lab 13—Kubernetes and AWS orchestration with CloudFormation

This purpose of this lab is to provide you with potentially interesting starting points for your future exploration of cloud technologies beyond COSC349.

The Kubernetes and CloudFormation parts of this lab are entirely independent so if you get blocked completing one part, you should be able to continue with the other.

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
- On the VM, run the following two commands to download and then install Minikube within your VM (note that we're using a specific, old version of Minikube!):

```
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.5.1/minikube-linux-amd64
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

- You should see the system downloading the Docker images that it uses to drive the components of Kubernetes. (This will take some time to complete.) After a number of minutes, the output should resemble:

```
😄  minikube v1.5.1 on Ubuntu 16.04 (vbox/amd64)
🤹  Running on localhost (CPUs=2, Memory=3951MB, Disk=9861MB) ...
ℹ️   OS release is Ubuntu 16.04.5 LTS
🐳  Preparing Kubernetes v1.16.2 on Docker 18.09.7 ...
💾  Downloading kubeadm v1.16.2
💾  Downloading kubelet v1.16.2
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
⌛  Waiting for: apiserver
🏄  Done! kubectl is now configured to use "minikube"
⚠️  /usr/bin/kubectl is version 1.19.2, and is incompatible with Kubernetes 1.16.2. You will need to update /usr/bin/kubectl or use 'minikube kubectl' to connect with this cluster
```

- As noted above, some of the directories created will be owned by the wrong user, so repair the permissions by invoking:
```
sudo chown -R vagrant ~/.kube ~/.minikube
```

### Looking into your Kubernetes "cluster"

- You can now examine your Kubernetes cluster, using the `kubectl` command.
- However, since we're using an old version of Minikube, you might want to first run the suggested form of command: (for me this suggests that it downloads an appropriate version of the `kubectl` command, but I haven't tested whether or not you can leave this step out):
```
minikube kubectl get nodes
```

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
http://127.0.0.1:46786/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
- The URL will need to be opened on your host computer's web browser, which means that you will need to set up port forwarding. Since this is a dynamically-selected port, it is likely easiest if you open VirtualBox, and add a network forwarding rule from `127.0.0.1` on some port you know (e.g., `8000`) to `127.0.0.1` on whatever port is displayed in the output of the above command.

- After starting VirtualBox, the VM that Vagrant started is visible:

![](https://i.imgur.com/Tqx1ZG6.png)

- ... open the "Settings" (e.g., using the toolbar) when focused on that running VM.

![](https://i.imgur.com/cGTGqc7.png)

- From there you can select the "Network" tab to display the network configuration.

![](https://i.imgur.com/yRQ7LTp.png)

- Unfold the "Advanced" controls ...

![](https://i.imgur.com/kvZtBAH.png)

- ... and select the "Port Forwarding" button. From which you can add appropriate forwarding rules (note that you need to substitute your specific parameter values).

![](https://i.imgur.com/C2jlcyv.png)

- Remember that you need to click "OK" not only on the port forwarding configuration, but also to click "OK" on the underlying networking configuration window before port forwarding rules are applied.

- You can then navigate in your web browser, to the address shown in the command output above, but ensure that the appropriate port number is substituted, i.e., the "Host Port" in your port forwarding rule.

- You should see a fully-featured dashboard for your (cut-down) Kubernetes cluster.

![](https://i.imgur.com/FbQDIXm.png)

- For example, by clicking on "Nodes" in the left-hand side menu, you can see the information that the `kubectl` command provided previously:

![](https://i.imgur.com/subzIpw.png)

:::info
The dashboard is extremely powerful: do explore it, but for now we will proceed with a specific deployment exercise.
:::

### Deploying a Kubernetes service

- The instructions that follow are adapted from https://kubernetes.io/docs/tutorials/hello-minikube/

- If you are leaving the dashboard running, you will need to open another terminal window and `vagrant ssh` into the VM running Minikube another time.

- First create a deployment of the hello-node application. The command that follows confirms that the deployment has been fetched.

```
vagrant@ubuntu-xenial:~$ kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
deployment.apps/hello-node created
vagrant@ubuntu-xenial:~$ kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   0/1     1            0           10s
```
- Looking at the pods may show that the hello-node is still being created, so you can try again until the pod is running.
```
vagrant@ubuntu-xenial:~$ kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
hello-node-7dc7987866-lwps5   1/1     Running   0          47s
```
- You can get a list of recent events (this event list is out of sync with the lab exercise, but is still indicative):
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
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.96.210.82   <pending>     8080:32626/TCP   30s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          17m
```

- Let's try to connect to the `hello-node`. Although the following command won't show the service, it will tell you where you want to point your host machine's web browser.

```
vagrant@ubuntu-xenial:~$ minikube service hello-node
|-----------|------------|-------------|------------------------|
| NAMESPACE |    NAME    | TARGET PORT |          URL           |
|-----------|------------|-------------|------------------------|
| default   | hello-node |             | http://10.0.2.15:32626 |
|-----------|------------|-------------|------------------------|
```

- You can set up port forwarding using VirtualBox in the same manner as above, and navigate your host computer's browser to a port that you have set up to reach the guest's IP and port number as shown in the output above.

- However, the service itself may not be ready: you can use the commands above to list services and events to get an idea of what the application deployment is up to.

- Eventually, you will be able to reach the test page:

![](https://i.imgur.com/V9quVN4.png)

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
CreationTimestamp:      Tue, 06 Oct 2020 10:22:10 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               2 desired | 2 updated | 2 total | 0 available | 2 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.14.2
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
NewReplicaSet:   nginx-deployment-574b87c764 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  8s    deployment-controller  Scaled up replica set nginx-deployment-574b87c764 to 2
```
- Note the mention of a ScalingReplicaSet. The contests of that set can be viewed using the following command, indicating that it's just a pair of replicas.
```
vagrant@ubuntu-xenial:~$ kubectl get pods -l app=nginx
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-574b87c764-2hcbd   1/1     Running   0          40s
nginx-deployment-574b87c764-6chqt   1/1     Running   0          40s
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
vagrant@ubuntu-xenial:~$ kubectl apply -f https://k8s.io/examples/application/deployment-update.yaml ; while true; do sleep 0.5; kubectl get pods -l app=nginx; done
deployment.apps/nginx-deployment configured
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-574b87c764-2hcbd   1/1     Running             0          78s
nginx-deployment-574b87c764-6chqt   1/1     Running             0          78s
nginx-deployment-5d66cc795f-hlv54   0/1     ContainerCreating   0          0s
...
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-574b87c764-2hcbd   1/1     Terminating         0          90s
nginx-deployment-574b87c764-6chqt   1/1     Running             0          90s
nginx-deployment-5d66cc795f-hlv54   1/1     Running             0          12s
nginx-deployment-5d66cc795f-tcjcs   0/1     ContainerCreating   0          1s
...
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-574b87c764-2hcbd   0/1     Terminating   0          91s
nginx-deployment-574b87c764-6chqt   1/1     Terminating   0          91s
nginx-deployment-5d66cc795f-hlv54   1/1     Running       0          13s
nginx-deployment-5d66cc795f-tcjcs   1/1     Running       0          2s
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-574b87c764-6chqt   0/1     Terminating   0          92s
nginx-deployment-5d66cc795f-hlv54   1/1     Running       0          14s
nginx-deployment-5d66cc795f-tcjcs   1/1     Running       0          3s
...
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5d66cc795f-hlv54   1/1     Running   0          24s
nginx-deployment-5d66cc795f-tcjcs   1/1     Running   0          13s
^C
```

- What you should see in the above, is that the new version of the deployment progressively replaced the containers from the old version of the deployment. This is exactly what is desirable, in order to upgrade or modify container-driven applications without causing service interruptions.

### Observe your work in the dashboard

- The above commands have—unsurprisingly—had significant effect in the Kubernetes dashboard.
- For example, selecting "Workloads" on the left-side menu shows all of the deployments, pods, etc.

![](https://i.imgur.com/lwkDql5.png)

- (The following screen-shot is just content reached by scrolling down from content shown in the preceding screen-capture.)

![](https://i.imgur.com/59kaoFl.png)

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

![](https://i.imgur.com/rSRxfwn.png)

- ... navigate to the CloudFormation console.

![](https://i.imgur.com/ZkQHuC2.png)

- You might instead see a page showing an empty list of "stacks". The term "stack" is in the sense of a deployed software stack.

![](https://i.imgur.com/2fuMdkC.png)

- In either case, click on "Create stack", and select "Template is ready", and "Upload a template file".
- Using your host's web browser download https://raw.githubusercontent.com/awslabs/aws-cloudformation-templates/master/aws/solutions/WordPress_Single_Instance.yaml to a local file.
- Choose that file under the "Upload a template file" selector.
- The template file will undergo some preliminary checks, and when they are complete, you can click the "View in Designer" button.

![](https://i.imgur.com/LRS5rje.png)

- The Designer provides a graphical and a YAML / JSON display of your template.

:::info 
In this case, if we were to run the template, WordPress wouldn't actually work, as the latest version of WordPress requires a later version of PHP than gets installed. We will use the designer to fix this, although of course could have edited the template file before uploading it to AWS CloudFormation.
:::

![](https://i.imgur.com/GIaS2yt.png)

- Navigate to around line 345, which indicates that `/var/www/html` should be created from `http://wordpress.org/latest.tar.gz`.

![](https://i.imgur.com/DABV3rN.png)

- Downgrade this URL for the latest version of WordPress to version 5.1, using the URL: `https://wordpress.org/wordpress-5.1.2.tar.gz`

![](https://i.imgur.com/cXfBylc.png)

- The graphical display will note that it is out-of-sync, so feel free to click the top-right reload button.
- Then select the cloud icon with an up-arrow in it, to deploy your template.

![](https://i.imgur.com/yCf5JXy.png)

- You will be returned to the "Create stack" page.
- Click "Next".

![](https://i.imgur.com/fbuC04H.png)

- On the "Specify stack details" page, give your Stack a name.
- Fill out all of the other details that the template requires to be completed. Note that for parameters such as the database passwords, these are for you to control what gets deployed, so they can be any values that you will remember.

![](https://i.imgur.com/VqzPNbN.png)

- Scrolling down, you will see a "Next" button, which you should click when you have filled in all of the required form fields.

![](https://i.imgur.com/1GBdlFr.png)

- You will reach the "Stack options" page, but none of the defaults should need to be changed.

![](https://i.imgur.com/1u0eeKj.png)

- So scrolling down you will again see a "Next" button that you should click.

![](https://i.imgur.com/ddhRVDb.png)

- You will reach the Review page for your CloudFormation Stack.

![](https://i.imgur.com/SjPbrEb.png)

- ... and again scrolling down, you should not need to change any of the parameters.
- You can click the "Create stack" button.

![](https://i.imgur.com/YVVVLto.png)

- This will return you to the "Stack details" page, where you can watch the deployment begin.

![](https://i.imgur.com/HRrclTN.png)

- After an amount of time (it might be a short time, or might be longer: first time for me was really quick, second time was... not), more events will appear, until the page announces "CREATE_COMPLETE".
- From looking at the template, you can see that some outputs are specified.

![](https://i.imgur.com/3wU2ZE7.png)

- Click on the "Outputs" tab to see the outputs that were generated.

![](https://i.imgur.com/It4MJ4r.png)

- In particular, we see the URL of the webserver that has been provisioned and configured, as key "WebsiteURL".

- Click on that link, and you should reach your EC2 instance's WordPress configuration page.

![](https://i.imgur.com/VFmS9p4.png)

- Feel free to experiment with WordPress, but when you are done return to the CloudFormation page and choose "Delete" for your WordPress Stack.

![](https://i.imgur.com/ieEEBQL.png)

- After you confirm your request to "Delete stack" you will be returned to the "Stack details" page, where you can watch your resources being de-provisioned.

![](https://i.imgur.com/JcMDVbg.png)

- You should ensure that the Stacks count returns to 0, so that you are not being charged for services that you are not using.

![](https://i.imgur.com/JuCLwSU.png)

:::success
Exercise:
- Experiment with deploying other CloudFormation templates.
- Make sure that you delete the "stack" after you have installed it, however, or services that you orchestrate will continue to run.
:::


