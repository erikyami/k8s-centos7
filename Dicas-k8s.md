# Dicas Kubernetes

## API Server

### Ver informações do contexto atual, saber se está logado no cluster correto:
```
kubectl config get-contexts
```

### Alterar o contexto caso seja necessário:
```
kubectl config use-context kubernetes-admin@kubernetes
```

### Pegar informações sobre a API Server do contexto atual
```
kubectl cluster info
```

### Pegar uma lista de Recursos API disponíveis no cluster
```
kubectl api-resources | less
```

### Usando kubectl explain
```
kubectl explain pods | less
kubectl explain pods.spec | less
kubectl explain pods.spec.containers | less
```


### Criando um pod a partir de um YAML
```
kubectl apply -f pod.yaml
```