# Test Setup
Pod-Envoy creates a pod with two containers. First container is ubuntu, second is envoy.

```
$ kubectl create configmap envoy --from-file=envoy.yaml
$ kubectl create -f pod-envoy.yml
$ kubectl create -f myapp.yml
```

Now exec into ubuntu container:

```
$ kubectl exec -it envoy -c ubuntu sh
```

<ENTER>

Once inside the container (ubuntu):

```
$ curl http://localhost:10000
Hi there, I love !#
```

Block DNS via iptables using DROP (sends nothing back)

```
$ iptables -I OUTPUT -p udp --dport 53 -j DROP
$ curl http://localhost:10000
# no healthy upstream
```

Block DNS via iptables using REJECT (sends a SYN back letting client know)
```
$ iptables -I OUTPUT -p udp --dport 53 -j REJECT
$ curl http://localhost:10000
# no healthy upstream
```

# Building Envoy
If you need to build a release version of Envoy to test with here are the instructions.

```
$ ./ci/run_envoy_docker.sh './ci/do_ci.sh bazel.release.server_only'
$ docker build -f ci/Dockerfile-envoy-image -t envoy .
```

This will produce a docker image tagged `envoy`