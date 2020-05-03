# Kubernetes Cluster Deployment with Ansible Playbooks


 The setup used to test the Ansible Playbooks:

 - Python 3 and Ansible installed on the host machine
    - my host machine is MacOS Catalina
 - 3 VMs (tested in vmware fusion) : 4 gig RAM( 2 gig min), 2 vCPUs, 32 Gig vmDisk
 - Os - Ubuntu Server LTS 18.04
 - The VMs must have internet access
    - master node vm -  IP - 10.0.1.201/24  
    - worker1 node vm - IP - 10.0.1.211/24
    - worker2 node vm - IP - 10.0.1.212/24   
 - Create user  ansible on all the vms

 - copy your ssh key on your host machine to the VMs for password-less access
    - $ ssh-copy-id ansible@10.0.1.201
    - $ ssh-copy-id ansible@10.0.1.211
    - $ ssh-copy-id ansible@10.0.1.212
 - On each VM enable 'ansible' user for paswordless sudo:
    - sudo visudo -f /etc/sudoers.d/myoverrides
      This will open up /etc/sudoers.d/myoverrides file with the default editor
      Add this line and save.  

      ansible ALL=NOPASSWD: ALL

 - You are all set to run the ansible playbooks.
   Run the playbooks from the within the directory that contains these .yml and hosts file.

   $ ansible-playbook -i hosts kube-dependencies.yml
   $ ansible-playbook -i hosts master.yml
   $ ansible-playbook -i hosts workers.yml




 - Verify the Kubernetes cluster:

 Log on to the master node and run the following

   - Check nodes and status of the pods
    kubectl get nodes
    kubectl get pods --all-namespaces

   - Deploy ngix
    kubectl create deployment nginx --image=nginx
    kubectl expose deploy nginx --port 80 --target-port 80 --type NodePort
    kubectl get services

    exmaple out put of kubectl get service :
    NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
     kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        13m
     nginx        NodePort    10.110.164.192   <none>        80:30560/TCP   12s

    note:  30560 is the port exposed on the worker nodes to access nginx service in this example
    nginx_port=30560

    To test that everything is working,
    visit http://worker_1_ip:nginx_port or http://worker_2_ip:nginx_port
    through a browser on your local machine. You will see Nginx’s familiar welcome page.


    If you would like to remove the Nginx application, first delete the nginx service from the master node:

    kubectl delete service nginx
    Run the following to ensure that the service has been deleted:

    kubectl get services
    You will see the following output:

    Output
    NAME         TYPE        CLUSTER-IP       EXTERNAL-IP           PORT(S)        AGE
    kubernetes   ClusterIP   10.96.0.1        <none>                443/TCP        1d
    Then delete the deployment:

    kubectl delete deployment nginx
    Run the following to confirm that this worked:

    kubectl get deployments
    Output
    No resources found.
