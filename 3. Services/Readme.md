# Serviços

Existem diferentes tipos de serviços disponíveis no K8S com diferentes
objetivos. Os principais são:

1. ClusterIP: Expoem o serviço internamente, ou seja, só é possível acessa-lo
de dentro do cluster. Este é o tipo default de serviço.
2. NodePort: Expoem o serviço em cada nó do cluster em uma porta estática
(NodePort). Será possível contatar esse serviço de fora do cluster através de
uma requisição para `<NodeIP>:<NodePort>`.
3. LoadBalancer: Expoem o serviço externamente (na internet) através da
utilização de um fornecedor de nuvem. Ou seja, só funciona para clusters
gerenciados por hyperscalers (aws, azure, google).

## NodePort

Para ver na prática como um serviço do tipo `NodePort` funciona vamos criar um
deploy que execute uma aplicação simples que escuta na porta 80 do container.

O nosso deploy vai ser o seguinte:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

A única diferença para os deploys que já declaramos até aqui é a utilização do
campo `ports` na especificação do pod. Através desse campo podemos especificar
quais portas do container devem ser expostas para que possamos acessar a
aplicação de fora do container. No exemplo acima estamos expondo a porta 80 do
container.

Após termos o deploy vamos expor a aplicação para fora do cluster. Para isso
utilizaremos o serviço de `NodePort`. Abaixo temos um exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
```

Tendo o serviço e o deploy criados no cluster utilizaremos o comando `get` para
listar os serviços do cluster e obter o número da porta que foi selecionado
para expor o serviço:

```sh
kubectl get svc
```

O resultado parecerá o seguinte:

```sh
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        45d
my-nginx     NodePort    10.110.135.91   <none>        80:31849/TCP   4s
```

Agora, se você fizer uma requisição para qualquer IP do cluster na porta acima
(31849) o K8S escutará a requisição e fará o load balancer para os pods do
nginx criados.

Dessa forma temos acesso externamente as aplicações do cluster, balanceamento
de carga e service discovery.
