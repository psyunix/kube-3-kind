# kube-3-kind
kube 3 kind clusters 

  324  kind create cluster --config cluster1.yaml
  326  kind create cluster --config cluster2.yaml
  327  kind create cluster --config cluster3.yaml
  
  328  kubectl --context kind-cluster1 apply -f nginx-deployment.yaml
  329  kubectl --context kind-cluster2 apply -f nginx-deployment.yaml
  330  kubectl --context kind-cluster3 apply -f nginx-deployment.yaml
  
  331  kubectl --context kind-cluster1 get pods
  332  kubectl --context kind-cluster2 get pods
  333* kubectl --context kind-cluster3 get pods
  
  334  kubectl --context kind-cluster1 apply -f service.yaml
  335  kubectl --context kind-cluster2 apply -f service.yaml
  336  kubectl --context kind-cluster3 apply -f service.yaml
  
  337  kubectl --context kind-cluster1 get svc
  338  kubectl --context kind-cluster2 get svc
  339  kubectl --context kind-cluster3 get svc
