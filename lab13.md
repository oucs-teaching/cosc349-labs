---
title: COSC349 Lab 7—Cloud Architecture—2026
tags: [cosc349, lab]

---

## Lab 7—Kubernetes and AWS orchestration with CloudFormation

This purpose of this lab is to provide you with an experience of container orchestration tools, and a cloud provider's infrastructure as code offering.

:::info
:bulb: 
The Kubernetes and CloudFormation parts of this lab are entirely independent so if you get blocked completing one part, you should be able to continue with the other.
:::

## Port 1: Kubernetes

### Installing Kubernetes

- There are many ways to run Kubernetes infrastructure on AWS, as discussed in lectures...
- ... however most of the more convenient approaches will not work using AWS Academy Learner Labs, due to missing management of permissions through IAM.
- It is, of course, possible create a complete Kubernetes infrastructure just using EC2 nodes, but this is not how developers would typically build such a system.

### Minikube

- We will use a special distribution of Kubernetes that is designed to be run on a single machine, in this case a VM provisioned on your computer using Vagrant.
- The installation of Minikube is not automated, so that you can watch it progress.
- Start by cloning the repository at: https://altitude.otago.ac.nz/cosc349/lab13-kubernetes
- Provision the VM using `vagrant up`.
- `vagrant ssh` into the VM.
- On the VM, run the following two commands to download and then install Minikube within your VM (note that we're using a specific version of Minikube so that there's a higher chance these instructions cause reproducible effects):
  - For Intel-based CPUs, run:
    ```
    curl -LO https://github.com/kubernetes/minikube/releases/download/v1.34.0/minikube_1.34.0-0_amd64.deb
    sudo dpkg -i minikube_1.34.0-0_amd64.deb
    ```
  - For Arm-based CPUs, change `amd64` to `arm64` as in by running:
    ```
    curl -LO https://github.com/kubernetes/minikube/releases/download/v1.34.0/minikube_1.34.0-0_arm64.deb
    sudo dpkg -i minikube_1.34.0-0_arm64.deb
    ```

- Now start Minikube with the following command:

```
minikube start
```

- You should see the system downloading the Docker images that it uses to drive the components of Kubernetes. (This will take some time to complete.) After a number of minutes, the output should resemble:

```
😄  minikube v1.34.0 on Ubuntu 24.04 (vbox/amd64)
✨  Automatically selected the docker driver. Other choices: ssh, none
📌  Using Docker driver with root privileges
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.45 ...
🎉  minikube 1.38.1 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.38.1
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

💾  Downloading Kubernetes v1.31.0 preload ...
    > preloaded-images-k8s-v18-v1...:  326.69 MiB / 326.69 MiB  100.00% 24.09 M
    > gcr.io/k8s-minikube/kicbase...:  487.90 MiB / 487.90 MiB  100.00% 18.13 M
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.31.0 on Docker 27.2.0 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Looking into your Kubernetes "cluster"

- You can now examine your Kubernetes cluster, using the `kubectl` command. In our case we'll prepend `minikube` to these invocations and run the `kubectl` command on the Kubernetes control plane. You may be able to get `kubectl` to work directly, e.g., installing from the Ubuntu package repositories or similar.

- List the nodes on your cluster (there should only be one):
```
minikube kubectl get nodes
```

- List the pods currently deployed (there shouldn't be any in your default namespace):
```
minikube kubectl get pods
```

- ... but there will be, if looking across all namespaces:
```
minikube kubectl -- get pods -A
```

- You can thus confirm that a significant amount of software infrastructure is already running, as the core of the Kubernetes platform.

### Opening up a web dashboard

- Invoke the following command to start a web-dashboard. This invocation will occupy your SSH session, but you can open another terminal window and from it `vagrant ssh` in again.
```
minikube dashboard
```
- After some delay, output such as the following should be presented:
```
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server

🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:37085/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
👉  http://127.0.0.1:37085/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
- The URL will need to be opened on your host computer's web browser, which means that you will need to set up port forwarding. Since this is a dynamically-selected port, it is likely easiest if you open VirtualBox, and add a network forwarding rule from `127.0.0.1` on some port you know (e.g., `8000`) to `127.0.0.1` on whatever port is displayed in the output of the above command.

- After starting VirtualBox, the VM that Vagrant started is visible:

![image](https://hackmd.io/_uploads/ByyPfwLwfe.png)

- ... open the "Settings" (e.g., using the toolbar) when focused on that running VM.

![image](https://hackmd.io/_uploads/BJ5uzwIDzx.png)

- From there you can select the "Network" tab to display the network configuration.

![image](https://hackmd.io/_uploads/r14FGPLwGl.png)

- Select the "Port Forwarding" button. From which you can add appropriate forwarding rules (note that you need to substitute your specific parameter values).

![image](https://hackmd.io/_uploads/ByGZ7wIDGx.png)

- Remember that you need to click "OK" not only on the port forwarding configuration, but also to click "OK" on the underlying networking configuration window before port forwarding rules are applied.
- You can then navigate in your web browser, to the address shown in the command output above, but ensure that the appropriate port number is substituted, i.e., the "Host Port" in your port forwarding rule.
- You should see a fully-featured dashboard for your (cut-down) Kubernetes cluster.
- ... although I have to admit that port forwarding didn't work when I tried it this year, so I used a different approach: running `vagrant ssh-config >ssh-conf` to write the OpenSSH configuration required to connect to the Vagrant VM into a temporary file, and then using SSH to forward a host local port to the guest local port, for me opened via opening another connection to the Vagrant VM with `ssh -F ssh-conf -L8080:localhost:32915 default`.
- I clicked on the "Service" left menu:

![image](https://hackmd.io/_uploads/r1pZNDIvMl.png)

- For example, by clicking on "Nodes" in the left-hand side menu, you can see the information that the `kubectl` command provided previously:

![image](https://hackmd.io/_uploads/ry0mEPIDzl.png)

:::info
:bulb:
The dashboard is extremely powerful: do explore it, but for now we will proceed with a specific deployment exercise.
:::

### Deploying a Kubernetes service

- The instructions that follow are adapted from https://kubernetes.io/docs/tutorials/hello-minikube/
- If you are leaving the dashboard running, you will need to open another terminal window and `vagrant ssh` into the VM running Minikube another time.
- First create a deployment of the hello-node application. The command that follows confirms that the deployment has been fetched.

```
vagrant@vagrant:~$ minikube kubectl -- create deployment hello-minikube --image=gcr.io/kubernetes-e2e-test-images/echoserver:2.2
deployment.apps/hello-node created
vagrant@vagrant:~$ minikube kubectl -- get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube   1/1     1            1           118s
```
- Looking at the pods may show that the hello-node is still being created, so you can try again until the pod is running.
```
vagrant@vagrant:~$ minikube kubectl -- get pods
NAME                             READY   STATUS    RESTARTS   AGE
hello-minikube-c4b877759-gmqss   1/1     Running   0          62s
```
- You can get a list of recent events:
```
vagrant@vagrant:~$ minikube kubectl -- get events
LAST SEEN   TYPE     REASON                    OBJECT                                MESSAGE
72s         Normal   Scheduled                 pod/hello-minikube-c4b877759-gmqss    Successfully assigned default/hello-minikube-c4b877759-gmqss to minikube
71s         Normal   Pulling                   pod/hello-minikube-c4b877759-gmqss    Pulling image "gcr.io/kubernetes-e2e-test-images/echoserver:2.2"
55s         Normal   Pulled                    pod/hello-minikube-c4b877759-gmqss    Successfully pulled image "gcr.io/kubernetes-e2e-test-images/echoserver:2.2" in 16.849s (16.849s including waiting). Image size: 21692741 bytes.
54s         Normal   Created                   pod/hello-minikube-c4b877759-gmqss    Created container echoserver
54s         Normal   Started                   pod/hello-minikube-c4b877759-gmqss    Started container echoserver
72s         Normal   SuccessfulCreate          replicaset/hello-minikube-c4b877759   Created pod: hello-minikube-c4b877759-gmqss
72s         Normal   ScalingReplicaSet         deployment/hello-minikube             Scaled up replica set hello-minikube-c4b877759 to 1
17m         Normal   Starting                  node/minikube                         Starting kubelet.
17m         Normal   NodeAllocatableEnforced   node/minikube                         Updated Node Allocatable limit across pods
17m         Normal   NodeHasSufficientMemory   node/minikube                         Node minikube status is now: NodeHasSufficientMemory
17m         Normal   NodeHasNoDiskPressure     node/minikube                         Node minikube status is now: NodeHasNoDiskPressure
17m         Normal   NodeHasSufficientPID      node/minikube                         Node minikube status is now: NodeHasSufficientPID
17m         Normal   RegisteredNode            node/minikube                         Node minikube event: Registered Node minikube in Controller
17m         Normal   Starting                  node/minikube
```

- Just running the containers is not enough to make the service available though. An invocation of the `expose` function of `kubectl` is required.

```
vagrant@vagrant:~$ minikube kubectl -- expose deployment hello-minikube --type=LoadBalancer --port=8080
service/hello-minikube exposed
```
- Listing the services will now confirm your hello-node service is present.
```
vagrant@vagrant:~$ minikube kubectl -- get services
NAME             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-minikube   LoadBalancer   10.103.198.197   <pending>     8080:30440/TCP   6s
kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP          17m
```

- Let's try to connect to the `hello-node`. Although the following command won't show the service, it will tell you where you want to point your host machine's web browser.

```
vagrant@vagrant:~$ minikube service hello-minikube
|-----------|----------------|-------------|---------------------------|
| NAMESPACE |      NAME      | TARGET PORT |            URL            |
|-----------|----------------|-------------|---------------------------|
| default   | hello-minikube |        8080 | http://192.168.49.2:30440 |
|-----------|----------------|-------------|---------------------------|
🎉  Opening service default/hello-minikube in default browser...
👉  http://192.168.49.2:30440
```
- You can set up port forwarding using VirtualBox in the same manner as above, and navigate your host computer's browser to a port that you have set up to reach the guest's IP and port number as shown in the output above.
- However, the service itself may not be ready: you can use the commands above to list services and events to get an idea of what the application deployment is up to.
- Eventually, you will be able to reach the test page:

![image](https://hackmd.io/_uploads/HJZrSwUPGg.png)

### A stateless application using a deployment

- Now, without caring so much about the actual functionality of the containers being orchestrated, let's explore the power of the orchestration itself.
    - This material is adapted from https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/
- Examine https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment.yaml
- Deploy this application from the YAML description:

```
vagrant@vagrant:~$ minikube kubectl -- apply -f https://k8s.io/examples/application/deployment.yaml
deployment.apps/nginx-deployment created
```

- The output from this command has indicated the name of the deployment, i.e., `nginx-deployment`.
- You can use the `describe` function to find out more information:

```
vagrant@vagrant:~$ minikube kubectl -- describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 21 Aug 2026 23:54:50 +0000
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
    Image:         nginx:1.14.2
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-d556bf558 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  6s    deployment-controller  Scaled up replica set nginx-deployment-d556bf558 to 2
```
- Note the mention of a ScalingReplicaSet. The contents of that set can be viewed using the following command, indicating that it's just a pair of replicas.
```
vagrant@vagrant:~$ minikube kubectl -- get pods -l app=nginx
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-d556bf558-95rm8   1/1     Running   0          17s
nginx-deployment-d556bf558-cvscp   1/1     Running   0          17s
```
- If the status column does not show "Running" for all pods, then wait until all pods are "Running".
- An updated version of the same deployment is available that steps the nginx version from 1.7.9 to 1.8 at: https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment-update.yaml
- Apply this deployment, and watch how your pods change:

```
minikube kubectl -- apply -f https://k8s.io/examples/application/deployment-update.yaml ;\
while true; do sleep 0.5; minikube kubectl -- get pods -l app=nginx; done
```

- You will need to press <kbd>control</kbd><kbd>c</kbd> to stop this command. Here's an example of edited output:

```
vagrant@vagrant:~$ minikube kubectl -- apply -f https://k8s.io/examples/application/deployment-update.yaml ;\
while true; do sleep 0.5; minikube kubectl -- get pods -l app=nginx; done
deployment.apps/nginx-deployment configured
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-7dbfbc79cf-j582z   0/1     ContainerCreating   0          1s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          106s
nginx-deployment-d556bf558-cvscp    1/1     Running             0          106s
...
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   0/1     ContainerCreating   0          1s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running             0          12s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          117s
nginx-deployment-d556bf558-cvscp    1/1     Terminating         0          117s
...
nginx-deployment-7dbfbc79cf-2mr2p   0/1     ContainerCreating   0          2s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running             0          13s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          118s
...
NAME                                READY   STATUS      RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   1/1     Running     0          3s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running     0          14s
nginx-deployment-d556bf558-95rm8    0/1     Completed   0          119s
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   1/1     Running   0          4s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running   0          15s
^C
```

- What you should see in the above, is that the new version of the deployment progressively replaced the containers from the old version of the deployment. This is exactly what is desirable, in order to upgrade or modify container-driven applications without causing service interruptions.

### Observe your work in the dashboard

- The above commands have—unsurprisingly—had significant effect in the Kubernetes dashboard.
- For example, selecting "Workloads" on the left-side menu shows all of the deployments, pods, etc.

![image](https://hackmd.io/_uploads/H1ieIPUwGl.png)

- (The following screen-shot is just content reached by scrolling down from content shown in the preceding screen-capture.)

![image](https://hackmd.io/_uploads/rkTb8DIwMl.png)

### Further deployments

:::success
:pencil: 
Suggested exercise:
- Using the dashboard or the command line, experiment with installing other Kubernetes deployments that you locate on the web.
- You may want to continue with the cloud orchestration section below, and return to this exercise.
:::

### Cleaning up

- When you are done with your experimentation, you can simply `vagrant destroy` the VM used to host Minikube, since there should be no problematic, persistent changes to your host computer's setup.

## Part 2: Cloud orchestration

- We will use a template to deploy the WordPress content management system on EC2 using AWS CloudFormation, which is Amazon's cloud orchestration tool.
- From the AWS Console ...

![image](https://hackmd.io/_uploads/SJqw_FLwfg.png)

- ... navigate to the CloudFormation console.
- You might see a splash page for CloudFormation, or you might instead see a page showing a list of "stacks" (possibly an empty list). The term "stack" is in the sense of a deployed software stack.

![image](https://hackmd.io/_uploads/SJznTfMkkx.png)

- In either case, click on "Create stack", suboption "With new resources (standard)", and select "Choose an existing template", and "Upload a template file".

![image](https://hackmd.io/_uploads/B1sMYYUDMe.png)

- Using your host's web browser download https://gist.githubusercontent.com/dme26/1f2aab3de43f0a58a0cc8c70560f5778/raw/9da5a50c8657cd1864adc68a7a89fef9d420902b/WordPress_Single_Instance_AL2023.yaml to a local file.
- Choose that file under the "Upload a template file" selector.

![image](https://hackmd.io/_uploads/r1Nhm58Dzg.png)

:::warning 
:warning:
This template downloads the latest WordPress, which will change in content as new versions are released, however given 7.1 was released a few days ago, it is unlikely that this will cause instability for this semester in 2026!
:::

- The template file will undergo some preliminary checks, and when they are complete, you can click the "View in Infrastructure Composer" button and explore a graphical view of the specification...

![image](https://hackmd.io/_uploads/BkW0Xq8DGx.png)

- Or if you chose the "Template" tab, you will see the YAML source.
- Click the "Create template" button in the top right:

![image](https://hackmd.io/_uploads/SycyNqIvfl.png)

- Confirm and continue back to CloudFormation.

![image](https://hackmd.io/_uploads/BkSx498wfe.png)

- Click "Next".

![image](https://hackmd.io/_uploads/SkUbE9UDGl.png)

- On the "Specify stack details" page, give your Stack a name.
- Fill out all of the other details that the template requires to be completed. Note that for parameters such as the database passwords, these are for you to control what gets deployed, so they can be any values that you will remember.

![image](https://hackmd.io/_uploads/rJBEV98wze.png)

- Scrolling down, you will see a "Next" button, which you should click when you have filled in all of the required form fields.
- You will reach the "Configure stack options" page. Out of caution, I chose the LabRole that AWS Academy provides (but the deployment might work without CloudFormation using the role).

![image](https://hackmd.io/_uploads/SynJ7gwDfx.png)

- Scrolling down you will again see a "Next" button that you should click.

![image](https://hackmd.io/_uploads/SynhcKUvzg.png)

- You will reach the Review page for your CloudFormation Stack.

![image](https://hackmd.io/_uploads/HJJ_E5UDzl.png)

- ... and again scrolling down, you should not need to change any of the parameters.
- You can click the "Submit" button.

![image](https://hackmd.io/_uploads/SJbKV58Pze.png)

- This will return you to the "Stack details" page, where you can watch the deployment begin.

![image](https://hackmd.io/_uploads/r15Nmxvwfe.png)

- After an amount of time (it might be a short time, or might be longer: first time for me was really quick, second time was... not), more events will appear, until the page announces "CREATE_COMPLETE".
- From looking at the template, you can see that some outputs are specified.

![image](https://hackmd.io/_uploads/rJNVNePPze.png)

- Click on the "Outputs" tab to see the outputs that were generated. (Your Stack will need to have completed its deployment for these Outputs to be available.)

![image](https://hackmd.io/_uploads/BypS4gDwGe.png)

- In particular, we see the URL of the webserver that has been provisioned and configured, as key "WebsiteURL".
- Click on that link, and you should reach your EC2 instance's WordPress configuration page.

![image](https://hackmd.io/_uploads/Hkvu4xwwGl.png)

- Feel free to experiment with setting up and then using WordPress.

![image](https://hackmd.io/_uploads/H1PWHxvPfg.png)

### Cleaning up

- When you have interacted with WordPress to your satisfaction, return to the CloudFormation page and choose "Delete" for your WordPress Stack.

![image](https://hackmd.io/_uploads/HJLXSevPMl.png)

- After you confirm your request to "Delete stack" you will be returned to the "Stack details" page, where you can watch your resources being de-provisioned.

![image](https://hackmd.io/_uploads/ByxBHgwwfl.png)

- You should ensure that the Stacks count returns to what it was before you started (1 for me, possibly 0 for you?), so that you are not being charged for services that you are not using.

![image](https://hackmd.io/_uploads/SyBPBgDDfl.png)

### Exercises

:::success
:pencil: 
Suggested exercise:
- Experiment with deploying other CloudFormation templates.
- Make sure that you delete the "stack" after you have installed it, however, or services that you orchestrate will continue to run.
:::


## Lab 7—Kubernetes and AWS orchestration with CloudFormation

This purpose of this lab is to provide you with an experience of container orchestration tools, and a cloud provider's infrastructure as code offering.

:::info
:bulb: 
The Kubernetes and CloudFormation parts of this lab are entirely independent so if you get blocked completing one part, you should be able to continue with the other.
:::

## Port 1: Kubernetes

### Installing Kubernetes

- There are many ways to run Kubernetes infrastructure on AWS, as discussed in lectures...
- ... however most of the more convenient approaches will not work using AWS Academy Learner Labs, due to missing management of permissions through IAM.
- It is, of course, possible create a complete Kubernetes infrastructure just using EC2 nodes, but this is not how developers would typically build such a system.

### Minikube

- We will use a special distribution of Kubernetes that is designed to be run on a single machine, in this case a VM provisioned on your computer using Vagrant.
- The installation of Minikube is not automated, so that you can watch it progress.
- Start by cloning the repository at: https://altitude.otago.ac.nz/cosc349/lab13-kubernetes
- Provision the VM using `vagrant up`.
- `vagrant ssh` into the VM.
- On the VM, run the following two commands to download and then install Minikube within your VM (note that we're using a specific version of Minikube so that there's a higher chance these instructions cause reproducible effects):
  - For Intel-based CPUs, run:
    ```
    curl -LO https://github.com/kubernetes/minikube/releases/download/v1.34.0/minikube_1.34.0-0_amd64.deb
    sudo dpkg -i minikube_1.34.0-0_amd64.deb
    ```
  - For Arm-based CPUs, change `amd64` to `arm64` as in by running:
    ```
    curl -LO https://github.com/kubernetes/minikube/releases/download/v1.34.0/minikube_1.34.0-0_arm64.deb
    sudo dpkg -i minikube_1.34.0-0_arm64.deb
    ```

- Now start Minikube with the following command:

```
minikube start
```

- You should see the system downloading the Docker images that it uses to drive the components of Kubernetes. (This will take some time to complete.) After a number of minutes, the output should resemble:

```
😄  minikube v1.34.0 on Ubuntu 24.04 (vbox/amd64)
✨  Automatically selected the docker driver. Other choices: ssh, none
📌  Using Docker driver with root privileges
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.45 ...
🎉  minikube 1.38.1 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.38.1
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

💾  Downloading Kubernetes v1.31.0 preload ...
    > preloaded-images-k8s-v18-v1...:  326.69 MiB / 326.69 MiB  100.00% 24.09 M
    > gcr.io/k8s-minikube/kicbase...:  487.90 MiB / 487.90 MiB  100.00% 18.13 M
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.31.0 on Docker 27.2.0 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Looking into your Kubernetes "cluster"

- You can now examine your Kubernetes cluster, using the `kubectl` command. In our case we'll prepend `minikube` to these invocations and run the `kubectl` command on the Kubernetes control plane. You may be able to get `kubectl` to work directly, e.g., installing from the Ubuntu package repositories or similar.

- List the nodes on your cluster (there should only be one):
```
minikube kubectl get nodes
```

- List the pods currently deployed (there shouldn't be any in your default namespace):
```
minikube kubectl get pods
```

- ... but there will be, if looking across all namespaces:
```
minikube kubectl -- get pods -A
```

- You can thus confirm that a significant amount of software infrastructure is already running, as the core of the Kubernetes platform.

### Opening up a web dashboard

- Invoke the following command to start a web-dashboard. This invocation will occupy your SSH session, but you can open another terminal window and from it `vagrant ssh` in again.
```
minikube dashboard
```
- After some delay, output such as the following should be presented:
```
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server

🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:37085/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
👉  http://127.0.0.1:37085/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
- The URL will need to be opened on your host computer's web browser, which means that you will need to set up port forwarding. Since this is a dynamically-selected port, it is likely easiest if you open VirtualBox, and add a network forwarding rule from `127.0.0.1` on some port you know (e.g., `8000`) to `127.0.0.1` on whatever port is displayed in the output of the above command.

- After starting VirtualBox, the VM that Vagrant started is visible:

![image](https://hackmd.io/_uploads/ByyPfwLwfe.png)

- ... open the "Settings" (e.g., using the toolbar) when focused on that running VM.

![image](https://hackmd.io/_uploads/BJ5uzwIDzx.png)

- From there you can select the "Network" tab to display the network configuration.

![image](https://hackmd.io/_uploads/r14FGPLwGl.png)

- Select the "Port Forwarding" button. From which you can add appropriate forwarding rules (note that you need to substitute your specific parameter values).

![image](https://hackmd.io/_uploads/ByGZ7wIDGx.png)

- Remember that you need to click "OK" not only on the port forwarding configuration, but also to click "OK" on the underlying networking configuration window before port forwarding rules are applied.
- You can then navigate in your web browser, to the address shown in the command output above, but ensure that the appropriate port number is substituted, i.e., the "Host Port" in your port forwarding rule.
- You should see a fully-featured dashboard for your (cut-down) Kubernetes cluster.
- ... although I have to admit that port forwarding didn't work when I tried it this year, so I used a different approach: running `vagrant ssh-config >ssh-conf` to write the OpenSSH configuration required to connect to the Vagrant VM into a temporary file, and then using SSH to forward a host local port to the guest local port, for me opened via opening another connection to the Vagrant VM with `ssh -F ssh-conf -L8080:localhost:32915 default`.
- I clicked on the "Service" left menu:

![image](https://hackmd.io/_uploads/r1pZNDIvMl.png)

- For example, by clicking on "Nodes" in the left-hand side menu, you can see the information that the `kubectl` command provided previously:

![image](https://hackmd.io/_uploads/ry0mEPIDzl.png)

:::info
:bulb:
The dashboard is extremely powerful: do explore it, but for now we will proceed with a specific deployment exercise.
:::

### Deploying a Kubernetes service

- The instructions that follow are adapted from https://kubernetes.io/docs/tutorials/hello-minikube/
- If you are leaving the dashboard running, you will need to open another terminal window and `vagrant ssh` into the VM running Minikube another time.
- First create a deployment of the hello-node application. The command that follows confirms that the deployment has been fetched.

```
vagrant@vagrant:~$ minikube kubectl -- create deployment hello-minikube --image=gcr.io/kubernetes-e2e-test-images/echoserver:2.2
deployment.apps/hello-node created
vagrant@vagrant:~$ minikube kubectl -- get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube   1/1     1            1           118s
```
- Looking at the pods may show that the hello-node is still being created, so you can try again until the pod is running.
```
vagrant@vagrant:~$ minikube kubectl -- get pods
NAME                             READY   STATUS    RESTARTS   AGE
hello-minikube-c4b877759-gmqss   1/1     Running   0          62s
```
- You can get a list of recent events:
```
vagrant@vagrant:~$ minikube kubectl -- get events
LAST SEEN   TYPE     REASON                    OBJECT                                MESSAGE
72s         Normal   Scheduled                 pod/hello-minikube-c4b877759-gmqss    Successfully assigned default/hello-minikube-c4b877759-gmqss to minikube
71s         Normal   Pulling                   pod/hello-minikube-c4b877759-gmqss    Pulling image "gcr.io/kubernetes-e2e-test-images/echoserver:2.2"
55s         Normal   Pulled                    pod/hello-minikube-c4b877759-gmqss    Successfully pulled image "gcr.io/kubernetes-e2e-test-images/echoserver:2.2" in 16.849s (16.849s including waiting). Image size: 21692741 bytes.
54s         Normal   Created                   pod/hello-minikube-c4b877759-gmqss    Created container echoserver
54s         Normal   Started                   pod/hello-minikube-c4b877759-gmqss    Started container echoserver
72s         Normal   SuccessfulCreate          replicaset/hello-minikube-c4b877759   Created pod: hello-minikube-c4b877759-gmqss
72s         Normal   ScalingReplicaSet         deployment/hello-minikube             Scaled up replica set hello-minikube-c4b877759 to 1
17m         Normal   Starting                  node/minikube                         Starting kubelet.
17m         Normal   NodeAllocatableEnforced   node/minikube                         Updated Node Allocatable limit across pods
17m         Normal   NodeHasSufficientMemory   node/minikube                         Node minikube status is now: NodeHasSufficientMemory
17m         Normal   NodeHasNoDiskPressure     node/minikube                         Node minikube status is now: NodeHasNoDiskPressure
17m         Normal   NodeHasSufficientPID      node/minikube                         Node minikube status is now: NodeHasSufficientPID
17m         Normal   RegisteredNode            node/minikube                         Node minikube event: Registered Node minikube in Controller
17m         Normal   Starting                  node/minikube
```

- Just running the containers is not enough to make the service available though. An invocation of the `expose` function of `kubectl` is required.

```
vagrant@vagrant:~$ minikube kubectl -- expose deployment hello-minikube --type=LoadBalancer --port=8080
service/hello-minikube exposed
```
- Listing the services will now confirm your hello-node service is present.
```
vagrant@vagrant:~$ minikube kubectl -- get services
NAME             TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
hello-minikube   LoadBalancer   10.103.198.197   <pending>     8080:30440/TCP   6s
kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP          17m
```

- Let's try to connect to the `hello-node`. Although the following command won't show the service, it will tell you where you want to point your host machine's web browser.

```
vagrant@vagrant:~$ minikube service hello-minikube
|-----------|----------------|-------------|---------------------------|
| NAMESPACE |      NAME      | TARGET PORT |            URL            |
|-----------|----------------|-------------|---------------------------|
| default   | hello-minikube |        8080 | http://192.168.49.2:30440 |
|-----------|----------------|-------------|---------------------------|
🎉  Opening service default/hello-minikube in default browser...
👉  http://192.168.49.2:30440
```
- You can set up port forwarding using VirtualBox in the same manner as above, and navigate your host computer's browser to a port that you have set up to reach the guest's IP and port number as shown in the output above.
- However, the service itself may not be ready: you can use the commands above to list services and events to get an idea of what the application deployment is up to.
- Eventually, you will be able to reach the test page:

![image](https://hackmd.io/_uploads/HJZrSwUPGg.png)

### A stateless application using a deployment

- Now, without caring so much about the actual functionality of the containers being orchestrated, let's explore the power of the orchestration itself.
    - This material is adapted from https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/
- Examine https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment.yaml
- Deploy this application from the YAML description:

```
vagrant@vagrant:~$ minikube kubectl -- apply -f https://k8s.io/examples/application/deployment.yaml
deployment.apps/nginx-deployment created
```

- The output from this command has indicated the name of the deployment, i.e., `nginx-deployment`.
- You can use the `describe` function to find out more information:

```
vagrant@vagrant:~$ minikube kubectl -- describe deployment nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 21 Aug 2026 23:54:50 +0000
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
    Image:         nginx:1.14.2
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-d556bf558 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  6s    deployment-controller  Scaled up replica set nginx-deployment-d556bf558 to 2
```
- Note the mention of a ScalingReplicaSet. The contents of that set can be viewed using the following command, indicating that it's just a pair of replicas.
```
vagrant@vagrant:~$ minikube kubectl -- get pods -l app=nginx
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-d556bf558-95rm8   1/1     Running   0          17s
nginx-deployment-d556bf558-cvscp   1/1     Running   0          17s
```
- If the status column does not show "Running" for all pods, then wait until all pods are "Running".
- An updated version of the same deployment is available that steps the nginx version from 1.7.9 to 1.8 at: https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/deployment-update.yaml
- Apply this deployment, and watch how your pods change:

```
minikube kubectl -- apply -f https://k8s.io/examples/application/deployment-update.yaml ;\
while true; do sleep 0.5; minikube kubectl -- get pods -l app=nginx; done
```

- You will need to press <kbd>control</kbd><kbd>c</kbd> to stop this command. Here's an example of edited output:

```
vagrant@vagrant:~$ minikube kubectl -- apply -f https://k8s.io/examples/application/deployment-update.yaml ;\
while true; do sleep 0.5; minikube kubectl -- get pods -l app=nginx; done
deployment.apps/nginx-deployment configured
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-7dbfbc79cf-j582z   0/1     ContainerCreating   0          1s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          106s
nginx-deployment-d556bf558-cvscp    1/1     Running             0          106s
...
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   0/1     ContainerCreating   0          1s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running             0          12s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          117s
nginx-deployment-d556bf558-cvscp    1/1     Terminating         0          117s
...
nginx-deployment-7dbfbc79cf-2mr2p   0/1     ContainerCreating   0          2s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running             0          13s
nginx-deployment-d556bf558-95rm8    1/1     Running             0          118s
...
NAME                                READY   STATUS      RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   1/1     Running     0          3s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running     0          14s
nginx-deployment-d556bf558-95rm8    0/1     Completed   0          119s
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7dbfbc79cf-2mr2p   1/1     Running   0          4s
nginx-deployment-7dbfbc79cf-j582z   1/1     Running   0          15s
^C
```

- What you should see in the above, is that the new version of the deployment progressively replaced the containers from the old version of the deployment. This is exactly what is desirable, in order to upgrade or modify container-driven applications without causing service interruptions.

### Observe your work in the dashboard

- The above commands have—unsurprisingly—had significant effect in the Kubernetes dashboard.
- For example, selecting "Workloads" on the left-side menu shows all of the deployments, pods, etc.

![image](https://hackmd.io/_uploads/H1ieIPUwGl.png)

- (The following screen-shot is just content reached by scrolling down from content shown in the preceding screen-capture.)

![image](https://hackmd.io/_uploads/rkTb8DIwMl.png)

### Further deployments

:::success
:pencil: 
Suggested exercise:
- Using the dashboard or the command line, experiment with installing other Kubernetes deployments that you locate on the web.
- You may want to continue with the cloud orchestration section below, and return to this exercise.
:::

### Cleaning up

- When you are done with your experimentation, you can simply `vagrant destroy` the VM used to host Minikube, since there should be no problematic, persistent changes to your host computer's setup.

## Part 2: Cloud orchestration

- We will use a template to deploy the WordPress content management system on EC2 using AWS CloudFormation, which is Amazon's cloud orchestration tool.
- From the AWS Console ...

![image](https://hackmd.io/_uploads/SJqw_FLwfg.png)

- ... navigate to the CloudFormation console.
- You might see a splash page for CloudFormation, or you might instead see a page showing a list of "stacks" (possibly an empty list). The term "stack" is in the sense of a deployed software stack.

![image](https://hackmd.io/_uploads/SJznTfMkkx.png)

- In either case, click on "Create stack", suboption "With new resources (standard)", and select "Choose an existing template", and "Upload a template file".

![image](https://hackmd.io/_uploads/B1sMYYUDMe.png)

- Using your host's web browser download https://gist.githubusercontent.com/dme26/1f2aab3de43f0a58a0cc8c70560f5778/raw/9da5a50c8657cd1864adc68a7a89fef9d420902b/WordPress_Single_Instance_AL2023.yaml to a local file.
- Choose that file under the "Upload a template file" selector.

![image](https://hackmd.io/_uploads/r1Nhm58Dzg.png)

:::warning 
:warning:
This template downloads the latest WordPress, which will change in content as new versions are released, however given 7.1 was released a few days ago, it is unlikely that this will cause instability for this semester in 2026!
:::

- The template file will undergo some preliminary checks, and when they are complete, you can click the "View in Infrastructure Composer" button and explore a graphical view of the specification...

![image](https://hackmd.io/_uploads/BkW0Xq8DGx.png)

- Or if you chose the "Template" tab, you will see the YAML source.
- Click the "Create template" button in the top right:

![image](https://hackmd.io/_uploads/SycyNqIvfl.png)

- Confirm and continue back to CloudFormation.

![image](https://hackmd.io/_uploads/BkSx498wfe.png)

- Click "Next".

![image](https://hackmd.io/_uploads/SkUbE9UDGl.png)

- On the "Specify stack details" page, give your Stack a name.
- Fill out all of the other details that the template requires to be completed. Note that for parameters such as the database passwords, these are for you to control what gets deployed, so they can be any values that you will remember.

![image](https://hackmd.io/_uploads/rJBEV98wze.png)

- Scrolling down, you will see a "Next" button, which you should click when you have filled in all of the required form fields.
- You will reach the "Configure stack options" page. Out of caution, I chose the LabRole that AWS Academy provides (but the deployment might work without CloudFormation using the role).

![image](https://hackmd.io/_uploads/SynJ7gwDfx.png)

- Scrolling down you will again see a "Next" button that you should click.

![image](https://hackmd.io/_uploads/SynhcKUvzg.png)

- You will reach the Review page for your CloudFormation Stack.

![image](https://hackmd.io/_uploads/HJJ_E5UDzl.png)

- ... and again scrolling down, you should not need to change any of the parameters.
- You can click the "Submit" button.

![image](https://hackmd.io/_uploads/SJbKV58Pze.png)

- This will return you to the "Stack details" page, where you can watch the deployment begin.

![image](https://hackmd.io/_uploads/r15Nmxvwfe.png)

- After an amount of time (it might be a short time, or might be longer: first time for me was really quick, second time was... not), more events will appear, until the page announces "CREATE_COMPLETE".
- From looking at the template, you can see that some outputs are specified.

![image](https://hackmd.io/_uploads/rJNVNePPze.png)

- Click on the "Outputs" tab to see the outputs that were generated. (Your Stack will need to have completed its deployment for these Outputs to be available.)

![image](https://hackmd.io/_uploads/BypS4gDwGe.png)

- In particular, we see the URL of the webserver that has been provisioned and configured, as key "WebsiteURL".
- Click on that link, and you should reach your EC2 instance's WordPress configuration page.

![image](https://hackmd.io/_uploads/Hkvu4xwwGl.png)

- Feel free to experiment with setting up and then using WordPress.

![image](https://hackmd.io/_uploads/H1PWHxvPfg.png)

### Cleaning up

- When you have interacted with WordPress to your satisfaction, return to the CloudFormation page and choose "Delete" for your WordPress Stack.

![image](https://hackmd.io/_uploads/HJLXSevPMl.png)

- After you confirm your request to "Delete stack" you will be returned to the "Stack details" page, where you can watch your resources being de-provisioned.

![image](https://hackmd.io/_uploads/ByxBHgwwfl.png)

- You should ensure that the Stacks count returns to what it was before you started (1 for me, possibly 0 for you?), so that you are not being charged for services that you are not using.

![image](https://hackmd.io/_uploads/SyBPBgDDfl.png)

### Exercises

:::success
:pencil: 
Suggested exercise:
- Experiment with deploying other CloudFormation templates.
- Make sure that you delete the "stack" after you have installed it, however, or services that you orchestrate will continue to run.
:::


