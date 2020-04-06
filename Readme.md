# Hello World de Kubernetes

A ideia desse passo a passo é entender na prática os tipos de recursos básicos disponíveis no K8S e como eles funcionam.
Vamos falar de:

1. Pods
2. Deployments
3. Config Maps e Secrets
4. Nodes
5. Services
6. Operator Patern

## Antes de começar

Antes de irmos direto ao K8S é importante que estejamos familiarizados com a
forma como vamos nos comunicar com o cluster. Toda comunicação com o K8S é
feita através de uma API, portanto, podemos utilizar linguagens de programação
para nos comunicar com o cluster programaticamente e automatizar a operação,
porém, interações manuais com o cluster também são necessárias, como por
exemplo, para fazer troubleshooting do cluster. Para nos comunicarmos com o
cluster utilizaremos o cliente que é fornecido pelo próprio K8S, o `kubectl`.

Siga [esses passos](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
para configurar o kubectl.

Após termos o kubectl instalado na máquina que utilizaremos para interagir com
o K8S, precisamos ter o `kubeconfig` do cluster. Mas afinal, o que é o
kubeconfig?

O kubeconfig é o arquivo que contém o endereço para acessar o cluster e também
as informações de autenticação.

Mais detalhes sobre o kubeconfig e kubectl podem ser encontrados
[aqui](https://kubernetes.io/docs/reference/kubectl/overview/).

Tendo portanto o kubectl configurado e também o kubeconfig do cluster podemos
prosseguir.

## Pods

Pods são a unidade mais básica do K8S, eles podem conter um ou mais containers.
Precisamos ficar atentos a esse detalhe pois o Pod é visto pelo cluster como
uma unidade única e todos os seus containers serão sempre atribuídos ao mesmo
nó, além disso, quando o K8S escala uma aplicação ele está aumentando o número
de pods, o que significa que containers em um mesmo pod serão escalados juntos.

Agora, vamos prosseguir para os exercícios relacionados a Pods e entender como
eles funcionam na prática. Os exercícios podem ser encontrados [aqui](./1.%20Pods/Readme.md).

## Nós

Um cluster K8S é composto por nós, onde os pods do usuário são executados, e um
(ou mais) mestres, onde pods do sistema são executados. Por default, nenhum pod
de usuário é executado em mestres. Quando um novo pod é criado no K8S, é
passado para o cluster um estado desejado, então, se torna responsabilidade do
cluster criar os pods e distribuílos em seus nós.

Por padrão  o K8S se responsabiliza pro definir onde os pods serão alocados,
porém, nós também podemos interferir nesse processo.

Vamos ver na prática como podemos interferir na alocação de pods. Os exercícios
podem ser encontrados [aqui](./2.Nodes/Readme.md)