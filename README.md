# Minikube
# 1.Requirements
```bash
apt install -y socat
apt install -y  apt-transport-https
apt install -y conntrack
apt install -y  virtualbox virtualbox-ext-pack
####Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
install minikube-linux-amd64 /usr/local/bin/minikube
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
cp kubectl /usr/local/bin/
chmod +x  /usr/local/bin/kubectl
```
In my case I set up these aliases (~/.bashrc) and autocompletion:
```bash
cat <<EOF >> ~/.bashrc
source <(kubectl completion bash)
source <(minikube completion bash)
alias k=kubectl
alias kga='watch -x kubectl get all -o wide'
EOF
```

# 2.Start
```bash
minikube start --vm-driver=none
systemctl enable kubelet.service
```
# Check out
```bash
k cluster-info
kubectl get po -A 
OR
minikube kubectl -- get po -A
```
# 3.Creating a sample user
```bash
touch dashboard-adminuser.yaml
```
then fill it as below:
```bash
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```
# 4.Creating a ClusterRoleBinding
```bash
touch rolebinding.yaml 
```
then fill it as below:
```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
# 5.Your Token
to catchig your secret key run this command:
```bash
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
```
here you go copy the token and paste it into Enter token field on the login screen.
# 6.Dashboard
```bash
minikube addons list
minikube addons enable dashboard
minikube dashboard --url
```
TOKEN FOR DASHBOARD: 
```bash
kubectl -n kube-system describe secret deployment-controller-token
```
# 7.Visit dashboard on local
You need to setting up Tunnel to your VMware so run this command with your details:
```bash
ssh -i D:\YOURKEY.ppk -L 8081:localhost:PORTOFYOURDASHBOARD root@VMIP
```
"Do not close your ssh session"
Now visit your dashboard on your system (your link will looks like mine)
```bash
http://127.0.0.1:8081/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```
Here you go ;)





