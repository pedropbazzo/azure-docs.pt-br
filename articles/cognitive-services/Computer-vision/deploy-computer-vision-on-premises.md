---
title: Usar Pesquisa Visual Computacional contêiner com kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Saiba como implantar o contêiner de Pesquisa Visual Computacional usando kubernetes e Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9a8e0dde8b24c39180a584c26af725ab82ea0176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907112"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usar Pesquisa Visual Computacional contêiner com kubernetes e Helm

Uma opção para gerenciar seus contêineres de Pesquisa Visual Computacional local é usar kubernetes e Helm. Usando kubernetes e Helm para definir uma Pesquisa Visual Computacional imagem de contêiner, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local. Por fim, exploraremos como testar os serviços implantados. Para obter mais informações sobre como executar contêineres do Docker sem orquestração kubernetes, consulte [instalar e executar pesquisa Visual computacional contêineres](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar Pesquisa Visual Computacional contêineres locais:

| Obrigatório | Finalidade |
|----------|---------|
| Conta do Azure | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| CLI do kubernetes | A [CLI do kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O kubernetes também é necessário antes de Helm, que é o Gerenciador de pacotes do kubernetes. |
| CLI do Helm | Instale a [CLI do Helm][helm-install], que é usada para instalar um gráfico do Helm (definição de pacote de contêiner). |
| Pesquisa Visual Computacional recurso |Para usar o contêiner, você precisará ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster kubernetes

Espera-se que o computador host tenha um cluster kubernetes disponível. Consulte este tutorial sobre como [implantar um cluster kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster kubernetes em um computador host. Você pode encontrar mais informações sobre implantações na [documentação do kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando as credenciais do Docker com o cluster kubernetes

Para permitir que o cluster kubernetes para `docker pull` as imagens configuradas do `containerpreview.azurecr.io` registro de contêiner, você precisa transferir as credenciais do Docker para o cluster. Execute o [`kubectl create`][kubectl-create] comando a seguir para criar um *segredo do registro do Docker* com base nas credenciais fornecidas do pré-requisito de acesso ao registro de contêiner.

Na sua interface de linha de comando de escolha, execute o comando a seguir. Certifique-se de substituir o `<username>` , `<password>` e `<email-address>` pelas credenciais de registro de contêiner.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver acesso ao `containerpreview.azurecr.io` registro de contêiner, poderá criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado em seu JSON de configuração do Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir será impressa no console quando o segredo tiver sido criado com êxito.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com o `secrets` sinalizador.

```console
kubectl get secrets
```

Executar o `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores do gráfico Helm para implantação

Comece criando uma pasta chamada *Read*. Em seguida, Cole o seguinte conteúdo de YAML em um novo arquivo chamado `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Para configurar os valores padrão do gráfico de Helm, copie e cole o seguinte YAML em um arquivo chamado `values.yaml` . Substitua os `# {ENDPOINT_URI}` `# {API_KEY}` comentários e pelos seus próprios valores. Configure resultExpirationPeriod, Redis e RabbitMQ, se necessário.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Se os `billing` `apikey` valores e não forem fornecidos, os serviços expirarão após 15 minutos. Da mesma forma, a verificação falha porque os serviços não estão disponíveis.
> 
> - Se você implantar vários contêineres de leitura atrás de um balanceador de carga, por exemplo, em Docker Compose ou kubernetes, você deverá ter um cache externo. Como o contêiner de processamento e o contêiner de solicitação GET podem não ser iguais, um cache externo armazena os resultados e os compartilha entre contêineres. Para obter detalhes sobre as configurações de cache, consulte [configurar pesquisa Visual computacional contêineres do Docker](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Crie uma pasta de *modelos* no diretório de *leitura* . Copie e cole o YAML a seguir em um arquivo chamado `deployment.yaml` . O `deployment.yaml` arquivo funcionará como um modelo Helm.

> Os modelos geram arquivos de manifesto, que são descrições de recursos formatados por YAML que o kubernetes pode entender. [-Guia de modelo de gráfico de Helm][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Na mesma pasta *modelos* , copie e cole as seguintes funções auxiliares no `helpers.tpl` . `helpers.tpl` define funções úteis para ajudar a gerar o modelo Helm.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
O modelo especifica um serviço de balanceador de carga e a implantação do seu contêiner/imagem para leitura.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração da imagem (s) do Docker a ser extraída do `containerpreview.azurecr.io` registro de contêiner.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa do aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos Helm* fornecidos recebem as imagens do Docker do serviço pesquisa Visual computacional e o serviço correspondente do registro de `containerpreview.azurecr.io` contêiner.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico Helm no cluster kubernetes

Para instalar o *gráfico do Helm*, precisaremos executar o [`helm install`][helm-install-cmd] comando. Certifique-se de executar o comando instalar do diretório acima da `read` pasta.

```console
helm install read ./read
```

Aqui está um exemplo de saída que você pode esperar ver em uma execução de instalação bem-sucedida:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A implantação do kubernetes pode levar mais de alguns minutos para ser concluída. Para confirmar se os pods e os serviços estão adequadamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre como instalar aplicativos com o Helm no AKS (serviço kubernetes do Azure), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
