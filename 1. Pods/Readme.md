# Pods

Como dito anteriormente, os pods são a unidade mais básica do K8S. Os pods
agregam um ou mais containers que serão executados sempre juntos, no mesmo nó.

Existem duas maneiras de interagir com o K8S, de forma imperativa ou
declarativa. Leia a comparação e explicação do K8S sobre as
diferenças entre elas, suas vantagens e desvantagens [aqui](https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/).

## kubectl run

Para criar nosso primeiro pod, utilizaremos a forma imperativa:

```sh
kubectl run --image=paulbouwer/hello-kubernetes:1.7 hello-kubernetes
```

## kubectl get

Após executar esse comando liste os pods do cluster através do comando `get`
do kubectl.

```sh
kubectl get pods
```

## kubectl describe

Agora, execute o comando `describe` para o pod que foi criado para obtermos
mais informações sobre ele:

```sh
kubectl describe pod $NOME_DO_POD_OBTIDO_NO_COMANDO_GET
```

Através do describe podemos ver várias informações relacionadas ao pod que foi
criado. Vemos, por exemplo, a lista de containers, portas expostas (no caso
nenhuma), a imagem utilizada para a criação do container e, algo que é bem útil
em situações de troubleshooting que é o campo `Events` que no momento aparece
vazio.

Outro comando muito importante é o comando `logs`. Como o próprio nome do
comando, ele é utilizado para vermos os logs gerados por um container.

## kubectl logs

Verifique os logs do container:

```sh
kubectl logs $NOME_DO_POD_OBTIDO_NO_COMANDO_GET
```

Diferente do comando describe e get que precisamos explicitar o recurso que
estamos obtendo, por exemplo `pods`, não precisamos fornecer o recurso para
o comando logs pois só conseguiremos obter logs de containers. InformaçÕes
de erros e estado geralmente são obtidas através do comando describe no campo
`events`.

## kubectl apply

Vamos agora utilizar o modo descritivo do kubernetes. Vamos criar o nosso
arquivo para descrever um pod. O kubernetes aceita YAML e JSON para descrever
configurações. Leia mais sobre arquivos de configurações no K8S [aqui](https://kubernetes.io/docs/concepts/configuration/overview/).

Como o próprio guia sugere vamos utilizar o formato YAML, até porque, é o
mais utilizado. Com o pequeno trecho abaixo crie um arquivo `.yaml`.

```yaml
---
apiVersion: v1
kind: Pod
metadata:
    name: my-first-declarative-pod
spec:
    containers:
    - name: hello-kubernetes
      image: paulbouwer/hello-kubernetes:1.7
```

Tendo o arquivo com as informações acima podemos utilizar o comando `apply`
para aplicar o estado desejado ao cluster.

__OBS__: O kubernetes possui os comandos `create`, `delete` e `replace`,
verifique a diferença em utilizar esses comandos e utilizar `apply` [aqui](https://stackoverflow.com/questions/47369351/kubectl-apply-vs-kubectl-create).

Nós podemos também utilizar o comando `get` já visto anteriormente para
obter a declaração `yaml` dos recursos existes. Para isso precisamos utilizar
a flag `--output yaml`. Execute o comando get para cada pod e verifique que
no geral o conteúdo dos dois pods que criamos até agora são bem similares.

## kubectl delete

Como o próprio nome do comando já indica, o `delete` é utilizado para remover
recursos do cluster. Execute o comando para o pod que foi criado
interativamente e após execute o comando `get` para conferir se o recurso foi
realmente removido.

```sh
kubectl delete pod $NOME_DO_PRIMEIRO_POD_CRIADO
kubectl get pods
```

Então, o pod não foi removido? Vamos tentar remover o pod que criamos através
do comando apply e ver se o mesmo ocorre para ele:

```sh
kubectl delete pod my-first-declarative-pod
kubectl get pods
```

Então o pod declarativo foi deletado e o criado imperativa não foi deletado e
o pod criado declarativamente foi. Mas por quê?

Quando utilizamos o comando run, o K8S cria na verdade um recurso do tipo
`deploy` e não `pod`. O `deploy` fornece mais funcionalidades como por exemplo
garantir que o pod especificado sempre exista. Ou seja, mesmo que alguém
manualmente delete um pod, o deploy perceberá que o pod foi removido e o criará
novamente, mantendo sempre o estado descrito.

Execute o comando `get` para os recursos de `deploy` e utilize a flag
`--output` que vimos anteriormente.

```sh
kubectl get deploy
kubectl get deploy $NOME_DO_DEPLOY -oyaml
```

Note que dentro de um deploy exite uma especificaçào de pod que é utilizada
para definir o pod que deverá ser criado por aquele deploy. Também temos o
campo `replicas` que define o número de pods que devem existir, tente alterar
esse valor para um número diferente e analisar se o número de pods esperados
é criado.

```sh
kubectl edit deploy $NOME_DO_DEPLOY
```

__OBS__: A edição ocorrerá utilizando o editor VI. Após realizar as alterações
basta salvar o arquivo utilizando `:wq`.

Após verificar que o número de pods foi alterado vamos então remover o `deploy`
que acarretará na remoção dos pods. Para isso utilize o comando `delete` como
visto anteriormente.
