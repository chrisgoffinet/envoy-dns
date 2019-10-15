Pod-Envoy creates a pod with two containers. First container is ubuntu, second is envoy.

$ kubectl create configmap envoy --from-file=envoy.yaml
$ kubectl create -f pod-envoy.yml
$ kubectl create -f myapp.yml

Now exec into ubuntu container:

$ kubectl exec -it envoy -c ubuntu sh

<ENTER>

Once inside the container:

$ apt-get update && apt-get install curl iptables dnsutils

curl envoy to make sure you get a response from upstream cluster:

$ curl http://localhost:10000
Hi there, I love !#

Block DNS via iptables

iptables -I OUTPUT -p udp --dport 53 -j DROP

$ curl http://localhost:10000
# no healthy upstream
