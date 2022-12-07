# Linkerd
- Linkerd is a service mesh for Kubernetes.
- It makes running services easier and safer by giving you runtime debugging, observability, reliability, and security
- Fully open source (https://github.com/linkerd)
- CNCF project (https://www.cncf.io/projects/linkerd/)
- Lightweight (Go and Rust)

Linkerd has three basic components: 
- UI
- data plane
- control plane

Benefits of Service Mesh
1. Observability
- Traffic metrics
- example: request rate, latency, success rate between pods
- Prometheus thus metrics can be obtained in a time series way to see trends over time
2. Security
- Gives cryptographic identity to each proxy
- Mutual TLS
- On by default
- Transparent to the app (ie. app doesn’t have to opt into this since it’s handled by the proxies)
3. Reliability
- Proxies can do latency aware load balancing (ex: shift traffic to the faster replicas)
- Retry to failed requests
- Timeout when requests taking too long


# DEMO STEPS for running Linkerd on local machine (Windows)

## Setup: Install kind and create k8 cluster
0 - Kubernetes cluster running on local machine (Windows)
Install kind: tool for running local k8s clusters using Docker container nodes (Requires Go and Docker Desktop to be installed)
And check kubernetes version
```
go install sigs.k8s.io/kind@v0.17.0 && kind create cluster
kubectl version --short
```

## Install the CLI to interact with your Linkerd deployment.
Get Linkerd .exe for Windows https://github.com/linkerd/linkerd2/releases (ie. linkerd2-cli-stable-2.12.1-windows.exe) and have location saved. For the following commands, instead of using `linkerd`, this executable path will be used instead (ie. "C:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.1-windows.exe")

## Install linkerd data plane and control plane to your cluster
```
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe version
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe check --pre

c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe install --crds | kubectl apply -f -
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe install | kubectl apply -f -

kubectl -n linkerd get po
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe check
```

## Install viz extension for dashboard 
```
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe viz install | kubectl apply -f -
```
Launch and explore dashboard
```
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe viz dashboard &
```

## Install emojitovo demo app and forward it to port 8080
```
curl --proto "=https" --ssl-no-revoke --tlsv1.2 -sSfL https://run.linkerd.io/emojivoto.yml | kubectl apply -f -
kubectl -n emojivoto get po
```
Explore emojivoto on port 8080
```
kubectl -n emojivoto port-forward svc/web-svc 8080:80
```

## Mesh it with Linkerd
```
kubectl get -n emojivoto deploy -o yaml | c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe inject - | kubectl apply -f -
kubectl -n emojivoto get po
c:\Users\maria\linkerexe\linkerd2-cli-stable-2.12.2-windows.exe -n emojivoto check --proxy
```
Explore dahsboard that was previously launched and see the api calls being done and which ones fail (hint: the doughnut vote fails)

## References
https://linkerd.io/2.10/overview/
https://linkerd.io/what-is-a-service-mesh/
https://buoyant.io/service-mesh-manifesto
