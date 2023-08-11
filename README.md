# kubeadm-self-hosted-cluster-guide
Unlock Kubernetes mastery! Build self-hosted clusters with kubeadm. Step-by-step setup, configs, troubleshooting. Your path to Kubernetes excellence starts here.

---------------------------------------- Kubeadm Installation ------------------------------------------ 

-------------------------------------- Both Master & Worker Node ---------------------------------------
sudo su
apt update -y
apt install docker.io -y

systemctl start docker
systemctl enable docker

curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg
echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' > /etc/apt/sources.list.d/kubernetes.list

apt update -y
apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y

##To connect with cluster execute below commands on master node and worker node respectively
--------------------------------------------- Master Node -------------------------------------------------- 
sudo su
kubeadm init

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

kubeadm token create --print-join-command
  

------------------------------------------- Worker Node ------------------------------------------------ 
sudo su
kubeadm reset pre-flight checks
-----> Paste the Join command on worker node and append --v=5 at end

#To verify cluster connection  
---------------------------------------on Master Node-----------------------------------------

kubectl get nodes 


-------------On worker node execute the below command which you will get after executing token create command on master------------------------------
 kubeadm join 173.31.44.76:6443 --token n4tfb4.grmew1s1unug0get     --discovery-token-ca-cert-hash sha256:c4dga2eaf5960bed4320d8175dc6a73b1556795b1b7f5aabc07642ed85c67430 --v=5
 kubeadm reset pre-flight checks  # to confirm that no kubeadm init command was executed on this worker node
 kubeadm token create --print-join-command
 kubectl label node ip-172-31-35-236 node-role.kubernetes.io/worker=worker
 kubectl label nodes ip-172-31-83-69 kubernetes.io/role=worker
 kubectl config set-context $(kubectl config current-context) --namespace=dev
