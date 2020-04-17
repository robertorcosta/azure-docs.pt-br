---
title: Configure o Monitor Azure para contêineres Prometheus Integration | Microsoft Docs
description: Este artigo descreve como você pode configurar o Monitor Azure para agente de contêineres para raspar métricas do Prometheus com o cluster Kubernetes.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 7fcf52cceb69834f68f8e4ce7a2674972a6430fd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537365"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Configure a raspagem das métricas prometheus com o Monitor Azure para contêineres

[Prometheus](https://prometheus.io/) é uma popular solução de monitoramento métrico de código aberto e faz parte da [Cloud Native Compute Foundation](https://www.cncf.io/). O Monitor Azure para contêineres fornece uma experiência de onboarding perfeita para coletar métricas Prometheus. Normalmente, para usar o Prometheus, você precisa configurar e gerenciar um servidor Prometheus com uma loja. Ao se integrar ao Azure Monitor, um servidor Prometheus não é necessário. Você só precisa expor o ponto final das métricas Prometheus através de seus exportadores ou pods (aplicativo), e o agente containerizado do Azure Monitor para contêineres pode raspar as métricas para você. 

![Arquitetura de monitoramento de contêineres para Prometeu](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A versão mínima do agente suportada para raspar métricas Prometheus é ciprod07092019 ou posterior, `KubeMonAgentEvents` e a versão do agente suportada para a configuração de gravação e erros de agente na tabela é ciprod10112019. Para obter mais informações sobre as versões do agente e o que está incluído em cada versão, consulte [notas de liberação do agente](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Para verificar a versão do agente, na guia **Nó,** selecione um nó e no painel de propriedades o valor da nota da propriedade **Agent Image Tag.**

A raspagem das métricas prometheus é suportada com clusters Kubernetes hospedados em:

- AKS (Serviço de Kubernetes do Azure)
- Azure Stack ou no local
- Red Hat OpenShift no Azure

>[!NOTE]
>Para o Azure Red Hat OpenShift, um arquivo ConfigMap de modelo é criado no espaço *de nome de registro de openshift-azure.* Ele não está configurado para raspar ativamente métricas ou coleta de dados do agente.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Pré-requisitos do Azure Red Hat OpenShift

Antes de começar, confirme se você é um membro da função administrador do cluster de clientes do cluster Azure Red Hat OpenShift para configurar as configurações de raspagem do agente contêiner e prometheus. Para verificar se você é um membro do grupo *de admins osa-cliente,* execute o seguinte comando:

``` bash
  oc get groups
```

A saída será semelhante à seguinte:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Se você é membro do grupo *de admins osa-cliente,* você deve ser capaz de listar o `container-azm-ms-agentconfig` ConfigMap usando o seguinte comando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A saída será semelhante à seguinte:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Configurações de raspagem de Prometeu

A raspagem ativa de métricas de Prometeu é realizada a partir de uma das duas perspectivas:

* URL http em todo o cluster - e descobrir alvos a partir de pontos finais listados de um serviço. Por exemplo, serviços k8s, como kube-dns e kube-state-metrics, e anotações de pod específicas para um aplicativo. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus data_collection_settings.cluster]*.
* EM todo o nó - URL HTTP e descubra alvos a partir de pontos finais listados de um serviço. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus_data_collection_settings.node]*.

| Ponto de extremidade | Escopo | Exemplo |
|----------|-------|---------|
| Anotação de pod | Em todo o cluster | Anotações: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Serviço do Kubernetes | Em todo o cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/endpoint | Por nó e/ou em todo o cluster | `http://myurl:9101/metrics` |

Quando uma URL é especificada, o Azure Monitor para contêineres apenas raspa o ponto final. Quando o serviço Kubernetes é especificado, o nome do serviço é resolvido com o servidor DNS do cluster para obter o endereço IP e, em seguida, o serviço resolvido é raspado.

|Escopo | Chave | Tipo de dados | Valor | Descrição |
|------|-----|-----------|-------|-------------|
| Em todo o cluster | | | | Especifique qualquer um dos três métodos a seguir para raspar pontos finais para métricas. |
| | `urls` | String | Matriz separada por comma | Ponto final HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um monitor Azure específico para parâmetros de contêineres e pode ser usado em vez de endereço IP do nó. Deve ser tudo maiúsculo.) |
| | `kubernetes_services` | String | Matriz separada por comma | Uma matriz de serviços Kubernetes para raspar métricas de kube-state-metrics. Por exemplo, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true ou false | Quando definido `true` nas configurações de cluster, o Azure Monitor para agente de contêineres raspará as cápsulas Kubernetes em todo o cluster para as seguintes anotações prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true ou false | Permite raspar a cápsula. `monitor_kubernetes_pods` deve ser definido como `true`. |
| | `prometheus.io/scheme` | String | http ou https | Padrão para sucateamento sobre HTTP. Se necessário, `https`definido para . | 
| | `prometheus.io/path` | String | Matriz separada por comma | O caminho de recurso HTTP para obter métricas. Se o caminho das `/metrics`métricas não for, defina-o com esta anotação. |
| | `prometheus.io/port` | String | 9102 | Especifique uma porta para raspar. Se a porta não estiver definida, ela será padrão para 9102. |
| | `monitor_kubernetes_pods_namespaces` | String | Matriz separada por comma | Uma lista de espaços de nome para raspar métricas de pods Kubernetes.<br> Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Em todo o nó | `urls` | String | Matriz separada por comma | Ponto final HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um monitor Azure específico para parâmetros de contêineres e pode ser usado em vez de endereço IP do nó. Deve ser tudo maiúsculo.) |
| Em toda a parte do nó ou em cluster | `interval` | String | Anos 60 | O padrão do intervalo de coleta é de um minuto (60 segundos). Você pode modificar a coleta para o *[prometheus_data_collection_settings.node]* e/ou *[prometheus_data_collection_settings.cluster]* para unidades de tempo como s, m, h. |
| Em toda a parte do nó ou em cluster | `fieldpass`<br> `fielddrop`| String | Matriz separada por comma | Você pode especificar certas métricas a serem coletadas ou`fieldpass`não a partir`fielddrop`do ponto final, definindo a listagem de permitir ( ) e não permitir ( ) . Você deve definir a lista de persesões primeiro. |

ConfigMaps é uma lista global e só pode haver um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMaps anulando as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configure e implante o ConfigMaps

Execute as seguintes etapas para configurar o arquivo de configuração ConfigMap para clusters Kubernetes.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo de yaml ConfigMap e salve-o como container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Essa etapa não é necessária ao trabalhar com o Azure Red Hat OpenShift, uma vez que o modelo ConfigMap já existe no cluster.

2. Edite o arquivo de yaml ConfigMap com suas personalizações para raspar as métricas do Prometheus. Se você estiver editando o arquivo de yaml ConfigMap para `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Azure Red Hat OpenShift, primeiro execute o comando para abrir o arquivo em um editor de texto.

    >[!NOTE]
    >A anotação `openshift.io/reconcile-protect: "true"` a seguir deve ser adicionada sob os metadados do *container-azm-ms-agentconfig* ConfigMap para evitar a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para coletar os serviços do Kubernetes em todo o cluster, configure o arquivo ConfigMap usando o exemplo a seguir.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a raspagem de métricas Prometheus a partir de uma URL específica através do cluster, configure o arquivo ConfigMap usando o exemplo a seguir.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a raspagem das métricas prometheus a partir do DaemonSet de um agente para cada nó individual no cluster, configure o seguinte no ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP é um monitor Azure específico para parâmetros de contêineres e pode ser usado em vez de endereço IP do nó. Deve ser tudo maiúsculo. 

    - Para configurar a raspagem das métricas do Prometeu especificando uma anotação de pod, execute as seguintes etapas:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especifique a seguinte configuração para anotações de pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se você quiser restringir o monitoramento a espaços de nome específicos para pods que tenham anotações, `monitor_kubernetes_pod` `true` por exemplo, inclua apenas pods `monitor_kubernetes_pods_namespaces` dedicados para cargas de trabalho de produção, defina o para configurá-lo em ConfigMap e adicione o filtro namespace especificando os namespaces a serem raspados. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Para clusters diferentes do Azure Red Hat OpenShift, `kubectl apply -f <configmap_yaml_file.yaml>`execute o seguinte comando kubectl: .
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Para o Azure Red Hat OpenShift, salve suas alterações no editor.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todas as cápsulas omsagent no cluster serão reiniciadas. A reinicialização é uma reinicialização para todas as cápsulas omsagent, nem todas reiniciadas ao mesmo tempo. Quando as reinicializações são concluídas, uma mensagem é exibida semelhante `configmap "container-azm-ms-agentconfig" created`à seguinte e inclui o resultado: .

Você pode visualizar o ConfigMap atualizado para O Azure `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Red Hat OpenShift executando o comando, . 

## <a name="applying-updated-configmap"></a>Aplicando configMap atualizado

Se você já implantou um ConfigMap no seu cluster e deseja atualizá-lo com uma configuração mais nova, você pode editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando os mesmos comandos de antes.

Para clusters Kubernetes que não o Azure `kubectl apply -f <configmap_yaml_file.yaml`Red Hat OpenShift, execute o comando . 

Para o cluster Azure Red Hat `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` OpenShift, execute o comando para abrir o arquivo no seu editor padrão para modificar e, em seguida, salvá-lo.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todas as cápsulas omsagent no cluster serão reiniciadas. A reinicialização é uma reinicialização para todas as cápsulas omsagent, nem todas reiniciadas ao mesmo tempo. Quando as reinicializações são concluídas, uma mensagem é exibida semelhante `configmap "container-azm-ms-agentconfig" updated`à seguinte e inclui o resultado: .

## <a name="verify-configuration"></a>Verificar a configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster, `kubectl logs omsagent-fdf58 -n=kube-system`use o seguinte comando para revisar os registros de uma cápsula de agente: . 

>[!NOTE]
>Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
> 

Se houver erros de configuração dos pods omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Erros relacionados à aplicação de alterações de configuração também estão disponíveis para revisão. As seguintes opções estão disponíveis para executar a solução adicional de alterações de configuração e raspagem de métricas prometheus:

- A partir de um agente `kubectl logs` pod logs usando o mesmo comando 
    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- De Dados Ao Vivo (visualização). Os logs de dados ao vivo (visualização) mostram erros semelhantes aos seguintes:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- A partir da tabela **KubeMonAgentEvents** em seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com a gravidade do *aviso* para erros de raspagem e gravidade de *erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informações*de gravidade, que não reportam erros. A propriedade **Tags** contém mais informações sobre o pod e o ID do contêiner em que ocorreu o erro e também a primeira ocorrência, última ocorrência, e contagem na última hora.

- Para o Azure Red Hat OpenShift, verifique os registros omsagent pesquisando a tabela **ContainerLog** para verificar se a coleta de log de registro openshift-azure está ativada.

Erros impedem o omsagent de analisar o arquivo, fazendo com que ele reinicie e use a configuração padrão. Depois de corrigir os erros no ConfigMap em clusters diferentes do Azure Red Hat OpenShift, salve o arquivo `kubectl apply -f <configmap_yaml_file.yaml`yaml e aplique o ConfigMaps atualizado executando o comando: . 

Para o Azure Red Hat OpenShift, edite e salve `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`o ConfigMaps atualizado executando o comando: .

## <a name="query-prometheus-metrics-data"></a>Dados de métricas do Query Prometheus

Para visualizar as métricas prometheus raspadas pelo Azure Monitor e quaisquer erros de configuração/raspagem relatados pelo agente, revise os [dados das métricas do Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) e a [configuração de consulta ou erros de raspagem](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Ver métricas de Prometeu em Grafana

O Azure Monitor para contêineres suporta métricas de visualização armazenadas no espaço de trabalho do Log Analytics nos painéis Grafana. Nós fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do painel da Grafana para que você comece e faça referência para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em dashboards grafana personalizados. 

## <a name="review-prometheus-data-usage"></a>Revisar o uso de dados prometheus

Para identificar o volume de ingestão de cada tamanho de métrica em GB por dia para entender se é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A saída mostrará resultados semelhantes aos seguintes:

![Resultados de consulta de log do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Para estimar qual é o tamanho de cada métrica em GB para entender se o volume de dados ingeridos recebidos no espaço de trabalho é alto, a seguinte consulta é fornecida.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A saída mostrará resultados semelhantes aos seguintes:

![Resultados de consulta de log do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Mais informações sobre como monitorar o uso de dados e analisar o custo estão disponíveis no [Manage use and costs with Azure Monitor Logs](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a configuração das configurações de coleta de agentes para variáveis stdout, stderr e ambientais a partir de cargas de trabalho de contêineres [aqui](container-insights-agent-config.md). 
