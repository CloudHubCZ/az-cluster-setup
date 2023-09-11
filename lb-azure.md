az network public-ip create \
--resource-group ppf-k8s-rg \
--name controlplaneip \
--sku Standard \
--dns-name nilfrankubeadm

az network lb create \
--resource-group ppf-k8s-rg \
--name kubemaster \
--sku Standard \
--public-ip-address controlplaneip \
--frontend-ip-name controlplaneip \
--backend-pool-name masternodes

az network lb probe create \
--resource-group ppf-k8s-rg \
--lb-name kubemaster \
--name kubemasterweb \
--protocol tcp \
--port 6443

az network lb rule create \
--resource-group ppf-k8s-rg \
--lb-name kubemaster \
--name kubemaster \
--protocol tcp \
--frontend-port 6443 \
--backend-port 6443 \
--frontend-ip-name controlplaneip \
--backend-pool-name masternodes \
--probe-name kubemasterweb \
--disable-outbound-snat true \
--idle-timeout 15 \
--enable-tcp-reset true

az network nic ip-config address-pool add \
--address-pool masternodes \
--ip-config-name ipconfigkube-master-1 \
--nic-name kube-master-1VMNic \
--resource-group ppf-k8s-rg \
--lb-name kubemaster

az network nic ip-config address-pool add \
--address-pool masternodes \
--ip-config-name ipconfigkube-master-2 \
--nic-name kube-master-2VMNic \
--resource-group ppf-k8s-rg \
--lb-name kubemaster