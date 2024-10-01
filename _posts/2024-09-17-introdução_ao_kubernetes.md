---
title: 01. Kubernetes, primeiros passos e primeiras informações
author: 01
date: 2024-04-12
categories: [kubernetes, docker]
tags: [containers, kubernetes, docker, linux]
media_subpath: /assets/media_posts/002-kubernetes01
publisehd: true
---

# 1. Antes de tudo, contêineres

Da forma mais resumida possível, Kubernetes nada mais é do que um **"orquestrador de contêineres"**. Olhando dessa maneira, fica claro que precisamos entender e dominar esses objetos para que a orquestra flua bem.

Por vezes, esse conceito pode se confundir com o [Docker](https://www.docker.com), a mais famosa ferramenta no mercado para criar e manuseá-los, porém *containers* são bem mais simples, eles nada mais são que **uma técnica de isolamente de recursos**.

Usando contêineres podemos fazer com que uma aplicação isole seus recursos computacionais (CPU, memória, *namespaces* etc.) mesmo que divida o kernel da máquina com outras aplicações. Fazendo, assim, com que tenhamos um ambiente facilmente reprodutível para rodar códigos, praticamente elimenando o clássico *"mas na minha máquina rodou direito"*, que todo Dev um dia já lançou.

A título de informação, podemos gerar uma única imagem, a qual será a base, o espelho para que inúmeros contêineres sejam executados. Para informações específicas sobre o assunto, procure um material específico.

## 1.1. *Container Engine*

Mergulhando um pouco mais fundo no mundo da conteinerização das aplicações, chegamos nos ***Container Engines***.

Eles é que são responsáveis por gerenciar imagens e volumes dos contêineres, além de garantir o isolamento adequado dos recursos de cada unidade sendo executada.

O mais famoso desses é o Docker, como citado na primeira sessão desse artigo, o qual foi a única opção por muitos anos; entretanto, hoje temos novas opções para a comunidade utilizar, algumas delas:
- [CRIO-O](https://crio-o.io);
- [Podman](https://podman.io);


## 1.2. *Container Runtime*

Já essas *Engines* que citamos, utilizam o ***container runtime*** para executar o contêiner, o Docker, por exemplo, utiliza o *containerd*

No geral, temos três tipos de *runtime*:
- ***Low level:*** são executados diretamente pelo kernel da máquina host.
    - [runc](https://github.com/opencontainers/runc);
    - [crun](https://github.com/containers/cruncon);
    - [runsc](https://github.com/google/gvisor);

- ***High level:*** são executados pelas *engines* que vimos anteriormente.
    - [containerd](https://containerd.io/)

- ***Sandbox:*** são *runtimes* executados pelas *engines* de forma segura. Esse tipo é executado em UniKernel ou em algum proxy que se comunique com o kernel.
    - [gVisor](https://gvisor.dev/);
        
- ***Virtualized:*** é executado em máquinas virtuais. Oferece uma performance menor, apesar de compensar com a segurança da virtualização da máquina.
    - [Kata Containers](https://katacontainers.io/);


## 1.3. *Open Container Initiative*

Um leitor desavisado, de primeira viagem, pode se sentir muito confuso tentando identificar os padrões desse mundo do isolamento de recursos. 

Pode ficar se perguntando como e quem define como um contêiner e seus *runtimes* e *engines* devem se comportar para que outras tecnologias se acoplem com sucesso.

A resposta desse questionamento está na **OCI** - ***Open Container Initiative*** -, uma organização sem fins lucrativos que hoje pertece ao corpo da ***The Linux Foundation***, mas foi fundada em 2015 por gigantes do ramo (Docker, CoreOS, IBM, Microsoft, RedHat e VMWare) com o objetivo de padronizar a criação de contêires e assim garantir que estes sejam compatíveis com qualquer ambiente proposto.

O principal projeto da OCI, por exemplo, é ***runc***, que já comentamos. Ele é um *container runtime* de baixo nível, de código fonte aberto escrito em Go.

&nbsp;
# 2. Finalmente Kubernetes

Diferente de outros orquestradores populares no mercado, o Kubernetes não gerencia contêineres diretamente, mas sim *pods*. Essas e outras estruturas que falaremos sobre agora tornam essa ferramenta única e completa.

Através desses elementos temos a possibilidade de criar contêineres e replicas, criar redes de conexão, padronizar IPs, liberar portas etc. Por isso e bem mais, é impossível falar de Kubernetes sem esmiuçar esses conceitos.

Por agora apenas pincelaremos os conceitos. Ao longo da leitura cara um dos elementos aqui citados será debatido com mais calma

## 2.1. Pods

São a menor unidade do kubernetes. Como dito anteriormente, o K8s não gerencia diretamente os contêineres, mas sim os pods. Podendo, estes, conter um ou mais contêineres.

*Pods* nada mais são que uma outra abstração de isolamento. Contêineres que dividem o mesmo podem também dividem o mesmo volume, endereços, ciclos de CPU e memória e outros.


## 2.2. Deployment

É um dos principais controladores utilizados pelo Kubernetes. Ele em conjunto com os *ReplicaSet* garantem o desejado número de réplicas em execução dos pods nos *workers*.

É também através dele que gerenciamos o ciclo de vida das aplicações, como imagens, portas, volumes, *labels* e variáveis de ambiente.

## 2.3. ReplicaSets

É um objeto criado no instante de criação de um *Deployment*. Ele é o braço do *Deployment* que garantirá que o número de pods em execução no nó é o desejado

## 2.4. Service

Podem ser de quatro tipos:

- *ClusterIP*
- *NodePort*
- *LoadBalancer*
- *ExternalName*

*Services* são formas de criarmos, ou expormos, a comunicaçãoatravés do cluster.

## 2.5. DaemonSet

## 2.6. StatefulSet

## 2.7. Namespaces

Tudo que opera dentro do Kuberentes opera dentro dos *namespaces*. Essas são organizações (ou separações) lógicas dentro da ferramente que nos permitem realizar até isolamento das unidades de isolamente.

Por meio deles, podemos limitar recursos, impor normas de segurança e afins.


&nbsp;
# 3. Finalmente Kubernetes (Arquitetura e Distribuições)

Como dito antes, Kubernetes, também apelidade de *k8s*, é um orquestrador de contêineres, ou seja, ele é responsável por todo o controle, gerenciamento, organização e saúde das unidades de isolamento criadas no nosso cluster computacional.

E assim como mencionamos nos contêineres, o projeto Kubernetes é soberano no mercado, mas existem alternativas a ele na orquestração:
- [Docker Swarm](https://docs.docker.com/engine/swarm/) (descontinuado);
- [Docker Compose](https://docs.docker.com/compose/) (apenas para desenvolvimento local);
- [RedHat OpenShift](https://www.redhat.com/pt-br/technologies/cloud-computing/openshift/container-platform);
- [HashiCorp Nomad](https://www.nomadproject.io/);
- [Apache Mesos](https://mesos.apache.org);
- [Cloud Foundry](https://https://www.cloudfoundry.org);


## 3.1. História e parentes não tão distantes

Apenas para contextualizar e marcar no tempo, o projeto que hoje chamamos de Kubernetes, surge, por volta de 2014, dentro da Google, como *Borg*, para orquestrar os contêineres de serviços internos da empresa.

O **Borg** deu origem ao **Omega**, o qual, por sua vez, originou o **Kubernetes**. Sendo este último diferenciado principalmente por ser de código aberto.

Ainda hoje o Borg continua como o principal sistema de gerenciamento de contêineres da Google devido sua capacidade de escalabilidade, robustez externa e variedade de recursos. Esses e outros motivos menores fizeram com que esse orquestrador seja a base não só do Kubernetes, mas de outras ferramentas no mercado, como o **Apache Mesos** e o **Cloud Foundry**.


## 3.2. Arquitetura do Kubernetes

Apesar das diferenças, os orquestradores concordam bastante quando o assunto é a arquitetura da solução. Sendo assim, no Kubernetes temos:

- **Cluster Kubernetes:** um aglomerado de nós/*nodes* computacionais trabalhando juntos para resolver problemas usando contêineres.
  - **Nodes:** são unidades computacionais distribuídas controladas pelo kubernetes que podem ser de dois tipos, cada um com seus componentes, responsabilidades e funcionalidades.
    
    > <u>Nota<sup>1</sup>:</u> Para garantir o bom funcionamento da aplicação como um todo, é recomendado trabalhar com, no mínimo, 3 nodes: 1 *control-plane* e 2 *workers*;

    > <u>Nota<sup>2</sup>:</u> É possível criar cluster Kubernetes com menos unidades, inclusive com apenas um nó, onde as aplicações rodem dentro do próprio *control-plane*, mas isso é extramente arriscado
    
    -  **Control Plane:** É o gerente do cluster, tem como responsabilidade garantir a saúde, a disponibilidade e armazenar o estado do cluster como um todo. Por *default* não rodam aplicações;
    
    -  **Worker:** são as unidades responsáveis por executar os contêineres, eles que fazem a mágica acontecer.

Cada um desses elementos tem seus componentes internos próprios e específicos que realizam funções de monitoramento, comunicação, execução etc. Mais abaixo falaremos mais sobre esses componentes.

![Arquitetura do Kubernetes](/arquitetura_cluster_kubernetes.png)


## 3.3. Componentes internos do Kubernetes

Para que esse belissimo orquestrador funcione, temos diversos componentes trabalhando por trás dos panos.

Na maioria das vezes, nem iremos interagir com eles diretamente, mas a simples queda de qualquer um fará com que o cluster começe a se arrastar ou até mesmo cair.


### 3.3.1. **API Server:** 
É um dos principais componentes do kubernetes. É uma API REST de JSON sobre HTTP que utiliza o famigerado *kubectl* como utilitário para administrar a comunicação entre os nós.

### 3.3.2. **etcd:** 
é um banco de dados; ou para ser mais preciso, um *datastore*; do tipo chave-valor que armazena as especificações, o status e as configurações do *cluster* como um todo. Apesar de armazenar os dados do conjunto inteiro, apenas o *API Server* tem permissão de gravar e ler dados do *etcd*.

Por padrão, ele é executado apenas no *control-plane*, entretanto há casos onde podemos designar clusters apenas para rodar o *etcd*

### 3.3.3. **Scheduler:**

É esse elemento que tem a responsabilidade de selecionar o determinado *node* que o hospedará determinado *pod*.

Essa seleção é feita baseando-se na quantidade de recursos computacionais disponíveis e no estado de cada nó. Tudo isso para garantir que os recursos estejam bem distribuídos no *cluster*.

Além disso, a seleção do nó para a execução de um pod também pode levar em consideração políticas definidas pelo usuário; afinidade e localização dos dados que serão lidos, por exemplo.


### 3.3.4. **Controller manager:**

É este *controller* quem garante que o *cluster* está rodando no último estado dado pelo *etcd*. Se no banco de dados temos um *deployment* pedindo 10 réplicas, é o *controller manager* quem verifica constantemente se o estado atual do *cluster* corresponde ao pedido, se os estados não baterem é ele quem buscará atualizar o estado atual.

### 3.3.5. **Kubelet:**

Este é o agente do Kubernetes nos *workers*. Em cada *node* deste tipo há um *kubelet* em execução encarregado de gerenciar os *pods* fornecidos pelo *controller* do *cluster*.

Por isso, ele pode iniciar, parar, pausar, resumir e manter os contêineres e *pods*, sempre seguindo as instruções fornecidas pelo controlador do *cluster*.

### 3.3.6. **Kube proxy:**

Age como *proxy* e *load balancer*, ficando responsável por efetuar o roteamento de requisições para os devidos pods e também por estabelecer a rede de cada nó.

### 3.3.7. Portas que estes elementos usam:

#### 3.3.7.1. *Control-plane*:

| Protocol | Direction | Port Range | Purpose | Used By |
|:---:|:-------:|:---------:|:------------------------|:---------------------|
| TCP |	Inbound | 6443*     | Kubernetes API server   | All                  |
| TCP |	Inbound | 2379-2380 | etcd server client API  | kube-apiserver, etcd |
| TCP |	Inbound | 10250     | Kubelet API             | Self, Control plane  |
| TCP |	Inbound | 10259     | kube-scheduler          | Self                 |
| TCP | Inbound | 10257     | kube-controller-manager |	Self                 |

* Porta customizável

#### 3.3.7.2. *Workers*:

| Protocol | Direction | Port Range | Purpose | Used By |
|:---:|:-------:|:-----------:|-------------------------|----------------------|
| TCP | Inbound | 10250       | Kubelet                 | Self, control-plane  |
| TCP | Inbound | 30000-32767 | NodePort                | Services All         |

## 3.4. Distribuições de Kubernetes para execução local:

- [Kind](https://kind.sigs.k8s.io/): sigla para "*Kubernetes in Docker*". É uma ferramenta que simula a execução de um cluster kubernetes usando contêires Docker. É excelente para desenvolvimento e testes, podendo criar até 1 *control-plane* e 3 *workers*, mas nunca deve ser usado em produção;

- [Minikube](https://minikube.sigs.k8s.io/docs/): é uma ferramenta que executa um cluster kubernetes com apenas um nó. É muito bom para fins didáticos, testes, desenvolvimento, mas nunca para produção;

- [MicroK8s](https://microk8s.io/): é uma distribuição Kubernetes desenvolvida pela **Canonical** que pode ser utilizada em ambientes de produção, especialmente em **IoT** (*Internet of Things*);

- [k3s](https://k3s.io/): é também uma distribuição bem leve de Kubernetes, muito empregada em IoT, Raspberry PI e outros;

- [k0s](https://k0sproject.io/): desenvolvido pela Mirantis, é uma distribuição construída para conter todos os recursos necessários para funcionar com um único binário. Pode até ser utilizado em ambientes de produção em casos específicos.

&nbsp;
# 4. Finalmente instalando o Kubernetes (localmente)


## 4.1. Kubectl

O primeiro passo para interagir com um cluster Kubernetes é dispor do `kubectl`. Para instalá-lo, basta executar no shell da sua máquina:

```bash
$ curl -LO "https://dl.k8s.io/release/$(\
    curl -L -s https://dl.k8s.io/release/stable.txt\
    )/bin/linux/amd64/kubectl"
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

E verificar a instalação com:
```bash
$ kubectl version --client
```

Esse utilitário funciona de forma bem simples e intuitiva: `kubectl` + \<verbo> + \<recurso>. Então, por exemplo, 
- se queremos criar um pod: `kubectl create pod`; 
- se queremos pegar as informações de um *deployment*: `kubectl get deployment`; 
- se queremos deletar um *service*: `kubectl delete service`. 

E por aí vai.

## 4.2. O bendito cluster

Há diversas formas diferentes de se criar um cluster kubernetes local. Na sessão #1 desse artigo listamos várias, a titulo de exemplo. 

E aqui repetiremos algumas para efetivamente instalar e usar alguns comandos. 

### 4.2.1. Minikube

Esta distribuição funciona na base da virtualização, ou seja, ela cria uma máquina virtual e dentro dela o cluster Kuberentes. Para isso, é necessário garantir que a sua máquina suporte essa operação; no GNU/Linux podemos fazer a checagem com o seguinte comando:

```bash
$ grep -E --color 'vmx|svm' /proc/cpuinfo
```

Qualquer saída diferente de vazio é uma resposta positiva.

Então instalamos o Minikube, em si:

```bash
$ curl -Lo \
    minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

$ chmod +x ./minikube

$ sudo mv ./minikube /usr/local/bin/minikube
```

E verificamos a instalação:
```bash
$ minikube version
```

Feito isso, podemos iniciar o Cluster Local:
```bash
minikube start
```
Ou ainda, `minikube start --nodes <NUM_NODES> -p multinode-cluste`, se quisermos um cluster com mais de um nó. Se tudo correr bem, teremos uma resposta do tipo:
```bash
😄  minikube v1.26.0 on Debian bookworm/sid
✨  Using the qemu2 (experimental) driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
🔥  Creating qemu2 VM (CPUs=2, Memory=6000MB, Disk=20000MB) ...
🐳  Preparing Kubernetes v1.24.1 on Docker 20.10.16 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

```

A partir daí, podemos descobrir as funcionalidades do `kubectl` como bem quisermos:
```bash
$ kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   20s   v1.25.3
```

O Minikube tem a regalia de nos fornecer diversos comandos que facilitaram o ambiente de testes e desenvolvimento:
- `minikube dashboard`; para habilitar o dashboard web;
- `minikube delete`: para deletar o cluster;
- `minikube delete`: para deletar o cluster e todos os arquivos referentes a ele;
- `minikube ip`: para fornecer o endereço da máquina virtual;
- `minikube logs`: para verificar os logs do cluster;
- `minikube ssh`: para acessar a máquina virtual criada;
- `minikube status`: para pegar informações sobre o cluster;


### 4.2.2. Kind

Essa é a minha distribuição favorita para testes por dois motivos. 

Primeiramente, por pura predileção, foi a primeira que botei a mão, então acaba que é a que mais tenho intimidade. 

Segundamente, ela me permite passar um arquivo `.yaml`
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
EOF
```
descrevendo como quero esse cluster, o que permite mais organização e clareza no desenvolvimento.

Para instalar é bem simples:
```bash
$ curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64

$ chmod +x ./kind

$ sudo mv ./kind /usr/local/bin/kind
```

Feito isso, podemos criar o cluster:
```bash
kind create cluster
```
Ou
```bash
kind create cluster --name kind-multinodes --config ./kind-3nodes.yaml
```
E teremos uma resposta similar a:
```bash
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.24.0) 🖼
 ✓ Preparing nodes 📦  
 ✓ Writing configuration 📜 
 ✓ Starting control-plane 🕹️ 
 ✓ Installing CNI 🔌 
 ✓ Installing StorageClass 💾 
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
```

&nbsp;
# 5. O Kubernetes no mundo real

Hoje o kubernetes é tão consolidado no ecossistema DevOps que algumas pessoas costumam dizer que ele é "o novo linux", isso porque há dezenas de diferentes distribuições operando em diferentes mercados e para diferentes funções e um ecossistema gigantesco de ferramentas que operam nele e com ele. 

Grandes empresas, como Rancher e RedHat, por exemplo, criando seu próprio *superset* de funcionalidades para oferecer aos cliente um *starter* pré-fabricado.

Há ainda, Cloud Providers, como AWS, GCP e Azure criando suas "máquinas kubernetes" desde o provisionamento.

Você pode ver mais sobre as ferramentas que operam na Cloud e interagem muito bem entre si na [landscape da CNCF](https://landscape.cncf.io/).


&nbsp;
# 6. Brincando com Kuberentes

## 6.1. Verificando nos namespace:

Nativamente alguns pods já vem em execução. Podemos visualizá-los de duas formas:

1. Listando todos os pods com a tag `-A`: `kubectl get pods -A`
2. Verificando direto no *namespace* `kube-system`: `kubernetes get pods -n kube-system`

De ambas as formas temos resultados similares.

## 6.2. Criando o primeiro pod

Podemos executar um pod com um contêiner nginx com o seguinte comando:
```bash
$ kubectl run nginx --image nginx
pod/nginx created
```

E verificar seu estado:
```bash
$ kubectl get pods -A
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          66s
```

Deletá-lo:
```bash
$ kubectl delete pod nginx
pod "nginx" deleted
```

## 6.3. Templates com DryRun

Como é de se esperar, não é uma boa prática criar alterar objetos no geral através da linha de comando. A melhor opção sempre será documentar as versões através de arquivos manifestos descrevendo detalhe a detalhe.

E a melhor e mais rápida opção para criar esses arquivos provalmente é o `dry-run`. 

Utilizando o exemplo do nginx,faríamos da seguinte maneira:
```bash
$ kubectl run nginx --image nginx --port 80 --dry-run=client -o yaml > pod-template.yaml
```

E a saída, o arquivo `pod-template.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Pronto para uso, ou para modificações. Se fizermos, `kubectl apply -f pod-template.yaml`, por exemplo, teremos um pod rodando um contêiner nginx.

A partir desse pod, poderíamos executar também,
```bash
$ kubectl expose pod nginx
```

Para expô-lo a rede. Daí teríamos os services `kubernetes`, padrão, e `nginx`:
```bash
$ kubectl get services

NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   8d
nginx        ClusterIP   10.105.41.192   <none>        80/TCP    2m30s
```

## 6.4. Visualizando recursos

Há outras opções bem interessante dentro do leque de funções que o `kubectl` nos permite:
- `kubectl get all -A`: para listar todos os objetos em todos os *namespaces*;
- `kubectl get pod, service`: para listar *pods* e *services* no namespace *default*;
- `kubectl delete -f pod-template.yaml `: para deletar os objetos relacionados àquele arquivo de configurações;
- `kubectl delete service nginx`: para deletar o service de nome nginx no *namespace* *default*;


# 7. Referências

Minha maior referência para esse artigo é o [primeiro dia do Descomplicando Kuberentes da Linux Tips](https://github.com/badtuxx/DescomplicandoKubernetes/blob/main/pt/day-1/README.md#arquitetura-do-k8s), mas além disso posso citar umas boas aulas que tive a oportunidade de ganhar com o Ruan, cabeça da JackExperts.

Fora isso, a internet é um mundão e esse artigo estará sempre em expansão.
