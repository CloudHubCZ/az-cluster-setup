./calicoctl create -f -<<EOF
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
name: new-pool
spec:
cidr: 10.224.0.0/16
ipipMode: Always
natOutgoing: true
EOF

calicoctl get ippool -o yaml > pool.yaml


k get no master-1 -o yaml > file.yaml; sed -i "s~172.17.0.0/24~10.224.0.0/16~" file.yaml; kubectl delete no master-1 && kubectl create -f file.yaml
k get no worker-1 -o yaml > file.yaml; sed -i "s~172.17.0.0/24~10.224.0.0/16~" file.yaml; kubectl delete no worker-1 && kubectl create -f file.yaml
