# _Jenkins Master/Node_

## _Theory Part_

Using Master/Node architecture we can perform multiple parellel CI/CD pipelines so that the master node won't get any performance issues. So that the Jenkins Master will assign Node Agents to perform CI/CD actions via help of Jenkinsfile pipeline script.

## _Practical Part_

Step 1: Create a container first using the following command.  
```
CMD -> docker run -d -p <PORT1>:<PORT2> -p <PORT2>:<PORT2> --name <CONTAINER_NAME> <IMAGE_NAME>:<TAG>
EX  -> docker run -d --name -p 5000:5000 -p 85:80 jenkins_agent_01 ubuntu:latest
```
Step 2: After creating the container enter into it using the following command.
```
CMD -> docker exec -it <CONTAINER_NAME> <SHELL_NAME>
EX  -> docker exec -it jenkins_agent_01 bash
```
or
```
EX  -> docker exec -it jenkins_agent_01 /bin/bash
```
Step 3: After entering into the container execute the following command for installing java 17 (as jenkins runs on java env only) & ssh.
```
CMD -> apt update && apt install openjdk-17-jre -y && apt install openssh-server -y && apt install vim -y
```
Step 4: Upon installing ssh, start the server and add this commands so that the ssh server won't stop.
```
CMD -> echo '/usr/sbin/sshd' >> /root/.bashrc
CMD -> service ssh start
```
Step 5: Now generate ssh private public key with below command.
```
CMD -> ssh-keygen
```
Step 6: Once it is generted, create `authorized_keys` file and paste the public key into it using 'vim'. As we are performing this in local, we are pasting public key into authorized_keys so that jenkins master can access the node agent via ssh without any password authentication. Later we will define this private key in the jenkins nodes section.
```
CMD -> cat id_ed25519
CMD -> vim authorized_keys
```	
Step 7: Create a jenkins directory in opt so that jenkins can use it as a workspace to build, test & deploy.
```
CMD -> mkdir /opt/jenkins
```	
Step 8: Run this command to get the hostname IPAddress.
```
CMD ->  hostname -i
```
Step 9: Now, start the jenkins server and go to config to create a node agent and configure it.
```
> Click `New Node`.
> Provide a `node name`, select `Permanent Agent` and `click create`.
> Provide any description or leave it blank.
> Select `Remote root directory` where the jenkins has to create workspace to perform actions.
> Provide `Labels` so that we can use this in the jenkins pipeline script to assign jenkins agents via labels.
> Now select `Launch method' as `Launch agents via SSH`.
    > Here provide the IPAddress of the jenkins agent container that we have created in `Host`.
    > Under `credentials` click on `+Add` -> and click on `Jenkins`, which will pop-up anothe window to configure credentials.
        > Under `kind` select `SSH Username with private key`.
        > Provide info in `ID` for the agent identification.
        > Provide any description or leave it blank.
        > Provide `username` as `root`.
        > under `private key' select `Enter directly` and click on `add` to paste the `id_ed25519` private key.
    > Once all the above changes are done, click on `Add`.
> Under `Host Key Verification Strategy` select `Non verifying Verficication Strategy`.
> Click on `Apply` and `Save` the changes.
> Once it is done, click on `Launch agent`.
```
Step 10: Now, perform these steps to create a `jenkins pipeline project` using `jenkins pipeline script` to assign the newly created node agents for performing CI/CD.
```
> Go to Jenkins dashboard and click on `+ New Item`.
    > Provide any description or leave it blank.
    > Under `Definition` select `Pipeline script` and paste the `Jenkinsfile` script.
    > Click on `Apply` and `Save` the changes.
```
Step 11: Then click on the `Build Now` and check the `Console Output` for any success or failure logs.
#
