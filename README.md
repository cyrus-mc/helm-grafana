# helm-grafana

## Prerequisites
If running against a new or local environement then make sure you have initialized first.
```
helm init
```

## Install
```
git clone git@github.com:Smarsh/helm-grafana.git
cd helm-grafana
helm install . --set Service.Type=NodePort 
```
