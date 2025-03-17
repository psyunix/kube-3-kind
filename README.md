# kube-3-kind
kube 3 kind clusters 

  kind create cluster --config cluster1.yaml
  kind create cluster --config cluster2.yaml
  kind create cluster --config cluster3.yaml
  
  kubectl --context kind-cluster1 apply -f nginx-deployment.yaml
  kubectl --context kind-cluster2 apply -f nginx-deployment.yaml
  kubectl --context kind-cluster3 apply -f nginx-deployment.yaml
  
  kubectl --context kind-cluster1 get pods
  kubectl --context kind-cluster2 get pods
  kubectl --context kind-cluster3 get pods
  
  kubectl --context kind-cluster1 apply -f service.yaml
  kubectl --context kind-cluster2 apply -f service.yaml
  kubectl --context kind-cluster3 apply -f service.yaml
  
  kubectl --context kind-cluster1 get svc
  kubectl --context kind-cluster2 get svc
  kubectl --context kind-cluster3 get svc
