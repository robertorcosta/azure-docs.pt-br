---
title: Use contêineres de serviço de fala com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando Kubernetes e Helm para definir as imagens de contêiner de texto para texto e texto-para-fala, criaremos um pacote Kubernetes. Este pacote será implantado em um cluster Kubernetes no local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474757"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Use contêineres de serviço de fala com Kubernetes e Helm

Uma opção para gerenciar seus contêineres de Fala no local é usar Kubernetes e Helm. Usando Kubernetes e Helm para definir as imagens de contêiner de texto para texto e texto-para-fala, criaremos um pacote Kubernetes. Este pacote será implantado em um cluster Kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados e várias opções de configuração. Para obter mais informações sobre a execução de contêineres Docker sem orquestração Kubernetes, consulte [instalar e executar contêineres de serviço de fala](speech-container-howto.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar recipientes de fala no local:

| Obrigatório | Finalidade |
|----------|---------|
| Conta do Azure | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao Registro de Contêineres | Para que os Kubernetes puxem as imagens do docker para o cluster, ele precisará acessar o registro do contêiner. |
| Kubernetes CLI | O [Kubernetes CLI][kubernetes-cli] é necessário para gerenciar as credenciais compartilhadas do registro de contêineres. Kubernetes também é necessário antes da Helm, que é a gerente de pacotes Kubernetes. |
| CLI do Helm | Instale o [Helm CLI][helm-install], que é usado para instalar um gráfico de leme (definição do pacote de contêineres). |
|Recurso de fala |Para usar esses contêineres, será necessário ter:<br><br>Um recurso _do Speech_ Azure para obter a chave de faturamento associada e o URI ponto final de faturamento. Ambos os valores estão disponíveis nas páginas Visão Geral de **Fala** e Chaves do portal Azure e são necessários para iniciar o contêiner.<br><br>**{API_KEY}**: chave de recursos<br><br>**{ENDPOINT_URI}**: exemplo uri ponto final é:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>A configuração recomendada do computador host

Consulte os detalhes do computador host [do serviço speech][speech-container-host-computer] como referência. Este *gráfico de leme* calcula automaticamente os requisitos de CPU e memória com base em quantos decodificações (solicitações simultâneas) que o usuário especifica. Além disso, ele será ajustado com base em se as `enabled`otimizações para entrada de áudio/texto são configuradas como . O gráfico de leme é padrão, duas solicitações simultâneas e otimização desativada.

| Serviço | CPU / Container | Memória / Recipiente |
|--|--|--|
| **Fala-a-texto** | um decodificador requer um mínimo de 1.150 miligramas. Se `optimizedForAudioFile` o estiver ativado, então são necessários 1.950 miligramas. (padrão: dois decodificadores) | Necessário: 2 GB<br>Limitado: 4 GB |
| **Conversão de texto em fala** | um pedido simultâneo requer um mínimo de 500 miligramas. Se `optimizeForTurboMode` o estiver ativado, então 1.000 milicores são necessários. (padrão: duas solicitações simultâneas) | Necessário: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Conecte-se ao cluster Kubernetes

Espera-se que o computador host tenha um cluster Kubernetes disponível. Veja este tutorial sobre [a implantação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceitual de como implantar um cluster Kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando credenciais do Docker com o cluster Kubernetes

Para permitir que o cluster `docker pull` Kubernetes para as `containerpreview.azurecr.io` imagens configuradas do registro de contêiner, você precisa transferir as credenciais de docker para o cluster. Execute [`kubectl create`][kubectl-create] o comando abaixo para criar um *segredo de registro de docker* com base nas credenciais fornecidas no pré-requisito de acesso ao registro de contêiner.

A partir de sua interface de linha de comando de escolha, execute o seguinte comando. Certifique-se de `<username>` `<password>`substituir `<email-address>` as credenciais de registro de contêiner e com as credenciais de registro de contêiner.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver `containerpreview.azurecr.io` acesso ao registro de contêineres, você pode criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado contra sua configuração Docker JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir é impressa no console quando o segredo foi criado com sucesso.

```console
secret "mcr" created
```

Para verificar se o segredo foi [`kubectl get`][kubectl-get] criado, `secrets` execute o com a bandeira.

```console
kubectl get secrets
```

Executando as `kubectl get secrets` impressões todos os segredos configurados.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configure os valores do gráfico Helm para implantação

Visite o [Microsoft Helm Hub][ms-helm-hub] para obter todos os gráficos de comando disponíveis publicamente oferecidos pela Microsoft. A partir do Microsoft Helm Hub, você encontrará o Gráfico de **Fala de Serviços Cognitivos em Locais**. O **Cognitive Services Speech On-Premises** é o gráfico que vamos `config-values.yaml` instalar, mas primeiro devemos criar um arquivo com configurações explícitas. Vamos começar adicionando o repositório da Microsoft à nossa instância Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, configuraremos nossos valores de gráfico helm. Copie e cole o YAML a `config-values.yaml`seguir em um arquivo chamado . Para obter mais informações sobre como personalizar o Gráfico de Leme de **Fala em Instalações de Serviços Cognitivos,** consulte [os gráficos de comando personalizados](#customize-helm-charts). Substitua `# {ENDPOINT_URI}` `# {API_KEY}` os comentários e comentários por seus próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se `billing` os `apikey` valores e valores não forem fornecidos, os serviços expirarão após 15 min. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de quais `containerpreview.azurecr.io` imagens docker para puxar do registro do contêiner.

> Um [gráfico Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos Kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa de aplicativos web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos helm fornecidos* puxam as imagens docker do serviço Speech, tanto de `containerpreview.azurecr.io` texto para discurso quanto dos serviços de fala para texto do registro de contêiner.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o *gráfico de leme,* precisaremos executar o [`helm install`][helm-install-cmd] comando, substituindo-o `<config-values.yaml>` pelo argumento de caminho e nome do arquivo apropriado. O `microsoft/cognitive-services-speech-onpremise` gráfico Helm abaixo está disponível no [Microsoft Helm Hub aqui][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Aqui está um exemplo de saída que você pode esperar ver a partir de uma execução de instalação bem-sucedida:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

A implantação do Kubernetes pode levar vários minutos para ser concluída. Para confirmar se os pods e os serviços estão devidamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Verifique a implantação do Helm com os testes do Helm

Os gráficos helm instalados definem *testes helm*, que servem como uma conveniência para verificação. Esses testes validam a prontidão do serviço. Para verificar os serviços **de fala-para-texto** e **texto-para-fala,** executaremos o comando [Helm test.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Esses testes falharão se o `Running` status do POD não `AVAILABLE` for ou se a implantação não estiver listada na coluna. Seja paciente, pois isso pode levar mais de dez minutos para ser concluído.

Esses testes produzirão vários resultados de status:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa para executar os *testes de leme,* você pode `kubectl get all` coletar os *endereços IP externos* e as portas correspondentes do comando. Usando o IP e a porta, `http://<external-ip>:<port>:/swagger/index.html` abra um navegador da Web e navegue para visualizar as páginas de swagger da API.

## <a name="customize-helm-charts"></a>Personalizar gráficos de Helm

Os gráficos de leme são hierárquicos. Ser hierárquico permite a herança de gráficos, também atende ao conceito de especificidade, onde as configurações que são mais específicas sobrepõem regras herdadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre a instalação de aplicativos com o Helm no Azure Kubernetes Service (AKS), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de Serviços Cognitivos][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
