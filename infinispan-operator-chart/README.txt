Installation :

On site 1

kubectl create ns infinispan-operator
kubectl create serviceaccount infinispan-operator --namespace infinispan-operator
kubectl create clusterrolebinding infinispan-operator -n infinispan-operator  --clusterrole=infinispan-operator-admin --serviceaccount=infinispan-operator:infinispan-operator

command 1 : kubectl get secret -n infinispan-operator $(kubectl get serviceaccount infinispan-operator  -n infinispan-operator  -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode

On site 2

kubectl create ns infinispan-operator
kubectl create serviceaccount infinispan-operator --namespace infinispan-operator
kubectl create clusterrolebinding infinispan-operator -n infinispan-operator  --clusterrole=infinispan-operator-admin --serviceaccount=infinispan-operator:infinispan-operator

command 2 : kubectl get secret -n infinispan-operator $(kubectl get serviceaccount infinispan-operator  -n infinispan-operator  -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode


On both sites

kubectl -n infinispan-operator create secret generic siteatoken --from-literal=token=$command1_output
kubectl -n infinispan-operator create secret generic siteatoken --from-literal=token=$command2_output

On both sites : < with proper values file > 

helm install infinispan-operator infinispan-operator-chart/ -n infinispan-operator --create-namespace -f values_site.yaml


DELETE : 

helm delete infinispan-operator -n infinispan-operator 

kubectl delete "$(kubectl api-resources --namespaced=true --verbs=delete -o name | tr "\n" "," | sed -e 's/,$//')" --all "$@" -n infinispan-operator