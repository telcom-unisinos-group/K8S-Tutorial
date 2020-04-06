# Nós

Podemos limitar um Pod de maneira que ele só possa ser executado em um nó em
particular, ou que preferencialmente, ele seja executado em um nó.
Os mecanismos para atribuir Pods para nós são os seguintes:

1. nodeSelector
2. Afinidade e anti-afinidade

## nodeSelector

É a maneira mais simples e recomendada de fazer a seleção de nó. `nodeSelector`
é um campo na `.spec.` de pods. Ele recebe valores no formato chave-valor. Para
que o pod possa ser atribuído à algum nó, o nó precisa ter as labels ali
informadas.

O primeiro passo é atribuir uma label para o nó ou verificar se o nó já possui
uma label qualquer pela qual possamos filtrá-lo.

Para adicionar uma label utilize o comando `label`:

```sh
kubectl get nodes # para listar os nós
kubectl label nodes <node-name> <label-key>=<label-value>
```

No exemplo abaixo vou utilizar a label `kubernetes.io/hostname: whitebox01`,
substitua essa label pela label que você atribuiu ao nó.

Após termos o nó com uma label que nos permita filtrá-lo, vamos criar um deploy
e utilizar o campo `nodeSelector` na especificação do pod. Para isso, edite o
arquivo de deploy que você possui e adicione o campo na especificação do pod,
após, utilize o comando `apply -f` para criar o deploy e consequentemente os
pods.
__Obs__: Crie o deployment com mais de uma réplica e observe que todas devem ir
para o nó especificado.

[Aqui](deploy-example.yaml) temos o exemplo do arquivo `.yaml` que utilizarei
para criar o deploy.

```sh
kubectl apply -f <deploy.yaml>
```

Após o deploy estar criado, observe se as réplcas foram todas atribuídas para o
nó especificado.

```sh
kubectl get nodes
kubectl describe nodes <nome-do-nó>
```

## Afinidade e anti-afinidade

Além da utilização do campo `nodeSelector`, que garante que um pod só será
executado em um nó se este possuir a(s) label(s) especificadas, também podemos
utilizar a afinidade de nó que permite a utilização de uma linguagem mais
abrangente para realizar a seleção, podemos por exemplo, fazer uma seleção não
mandatória (soft), onde indicamos a preferência, mas que se não existir nenhum
nó com recursos e com as labels selecionadas, o pod pode ser atribuído a outro
nó.

Mais detalhes sobre as opções podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/configuration/assign-pod-node).
