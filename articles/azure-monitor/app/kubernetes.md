---
title: Use o Application Insights para monitorar o Serviço de Kubernetes do Azure (AKS) ou outros aplicativos hospedados do Kubernetes – Azure Monitor | Microsoft Docs
description: O Azure Monitor usa a tecnologia de malha de serviço, Istio, no cluster do Kubernetes para fornecer monitoramento de aplicativo para qualquer aplicativo Kubernetes hospedado. Isso permite que você colete telemetria do Application Insights referente a solicitações de entrada e saída de e para os pods em execução no cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a5e73039db541023b1fd4a9b75e7c14030c8e219
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797895"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Monitoramento de aplicativo de instrumentação zero para aplicativos hospedados Kubernetes com Istio – Preterido

> [!IMPORTANT]
> Essa funcionalidade está sendo preterida no momento e não será mais compatível após 1º de agosto de 2020.
> Atualmente, o monitoramento sem código só pode ser habilitado para [Java por meio de agente autônomo](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Para outros idiomas, use os SDKs para monitorar seus aplicativos no AKS: [ASP.Net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.Net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) e [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

O Azure Monitor agora usa a tecnologia de malha de serviço em seu cluster do Kubernetes para fornecer monitoramento de aplicativo pronto para qualquer aplicativo Kubernetes hospedado. Com os recursos padrão do Application insights, como [Mapa de Aplicativos](../../azure-monitor/app/app-map.md) para modelar suas dependências, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) para monitoramento em tempo real, visualizações avançadas com o [painel padrão](../../azure-monitor/app/overview-dashboard.md), [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) e [Workbooks](../../azure-monitor/platform/workbooks-overview.md). Esse recurso ajudará os usuários a identificar gargalos de desempenho e pontos de acesso de falha em todas as suas cargas de trabalho Kubernetes dentro de um namespace Kubernetes selecionado. Ao aproveitar os investimentos existentes em malha de serviço com tecnologias como o Istio, o Azure Monitor habilita o monitoramento de aplicativo instrumentado automaticamente sem nenhuma mudança no código do aplicativo.

> [!NOTE]
> Essa é uma das várias maneiras de executar o monitoramento de aplicativos no Kubernetes. Também é possível instrumentar qualquer aplicativo hospedado no Kubernetes usando o [SDK do Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) sem a necessidade de uma malha de serviço. Para monitorar o Kubernetes sem instrumentar o aplicativo com um SDK, é possível usar o método abaixo.

## <a name="prerequisites"></a>Pré-requisitos

- Um [cluster do Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Acesso do console para o cluster para executar *kubectl*.
- Um [recurso do Application Insight](create-new-resource.md)
- Ter uma malha de serviço. Se o cluster não tiver o Istio implantado, você poderá aprender a [instalar e usar o Istio no Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funcionalidades

Ao usar o monitoramento de aplicativo de instrumentação zero para aplicativos hospedados do Kubernetes, você poderá usar:

- [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
- [Métricas de Live Stream](../../azure-monitor/app/live-stream.md)
- [Painéis](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Rastreamento distribuído](../../azure-monitor/app/distributed-tracing.md)
- [Monitoramento de transação de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Etapas de instalação

Para habilitar a solução, executaremos as seguintes etapas:
- Implantar o aplicativo (se ainda não tiver sido implantado).
- Verificar se o aplicativo faz parte da malha de serviço.
- Observar a telemetria coletada.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurar seu aplicativo para trabalhar com uma malha de serviço

O Istio oferece duas maneiras de [instrumentar um pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Na maioria dos casos, é mais fácil marcar o namespace do Kubernetes que contém seu aplicativo com o rótulo *istio-injection*:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Como a malha de serviço levanta os dados fora da conexão, não podemos interceptar o tráfego criptografado. Para o tráfego que não sai do cluster, use um protocolo não criptografado (por exemplo, HTTP). Para o tráfego externo que deve ser criptografado, considere [configurar a terminação TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

Aplicativos em execução fora da malha de serviço não são afetados.

### <a name="deploy-your-application"></a>Implantar seu aplicativo

- Implante seu aplicativo para o namespace *my-app-namespace*. Se o aplicativo já estiver implantado e você tiver seguido o método de injeção de sidecar automática descrito acima, será preciso recriar pods para garantir que o Istio insira seu sidecar. Inicie uma atualização sem interrupção ou exclua os pods individuais e aguarde até que eles sejam recriados.
- Verifique se seu aplicativo está em conformidade com os [Requisitos do Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Monitoramento de aplicativo de instrumentação zero para aplicativos hospedados Kubernetes

1. Baixe e extraia uma versão do [*adaptador do Application Insights*](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue até */src/kubernetes/* dentro da pasta da versão.
3. Editar *application-insights-istio-mixer-adapter-deployment.yaml*
    - Edite o valor da variável de ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* para conter a chave de instrumentação do recurso Application Insights no portal do Azure para conter a telemetria.
    - Se necessário, edite o valor da variável de ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* para conter uma lista separada por vírgulas de namespaces para os quais você gostaria de habilitar o monitoramento. Deixe em branco para monitorar todos os namespaces.
4. Aplique *cada* arquivo YAML encontrado em *src/kubernetes/* executando o seguinte (é preciso estar em */src/kubernetes/* ):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar implantação

- Verifique se o adaptador do Application Insights foi implantado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessário ajustar. Para incluir ou excluir a telemetria de um pod individual da coleta, use o rótulo *appinsights/monitoring.enabled* nesse pod. Isso terá prioridade sobre todas as configurações baseadas em namespace. Defina *appinsights/monitoring.enabled* como *verdadeiro* para incluir o pod e como *falso* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Visualizar telemetria de Application Insights

- Gere uma solicitação de exemplo em seu aplicativo para confirmar se o monitoramento está funcionando corretamente.
- Em 3–5 minutos, você deve começar a ver a telemetria aparecer no portal do Azure. Confira a seção *Mapa de Aplicativos* do recurso Application Insights no Portal.

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está o fluxo de solução de problemas a ser usado quando a telemetria não aparece na portal do Azure conforme esperado.

1. Verifique se o aplicativo está sob carga e se está enviando/recebendo solicitações HTTP simples. Como a telemetria é retirada da conexão, não há compatibilidade com o tráfego criptografado. Se não houver nenhuma solicitação de entrada ou saída, também não haverá telemetria.
2. Verifique se a chave de instrumentação correta foi fornecida na variável de ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* em *application-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação é encontrada na guia *Visão Geral* do recurso Application Insights no portal do Azure.
3. Verifique se o namespace do Kubernetes correto foi fornecido na variável de ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* em *application-insights-istio-mixer-adapter-deployment.yaml*. Deixe em branco para monitorar todos os namespaces.
4. Verifique se os pods do seu aplicativo foram injetados por sidecar pelo Istio. Verifique se o sidecar do Istio existe em cada pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se há um contêiner chamado *istio-proxy* em execução no pod.

5. Veja os rastreamentos do adaptador do Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de itens de telemetria recebidos é atualizada uma vez por minuto. Se não aumentar minuto a minuto: nenhuma telemetria será enviada ao adaptador por Istio.
   Procure erros no log.
6. Se tiver sido estabelecido que o adaptador do *Application Insight para Kubernetes* não está sendo alimentado por telemetria, verifique os logs do Mixer do Istio para descobrir por que ele não está enviando dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure erros, principalmente os pertencentes a comunicações com o adaptador *applicationinsightsadapter*.

## <a name="faq"></a>Perguntas frequentes

Para obter as informações mais recentes sobre o progresso deste projeto, visite o [GitHub sobre o adaptador do Application Insights para o projeto do Mixer do Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, por *todos os* arquivo YAML encontrados na execução *src/kubernetes/* :

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure Monitor e os contêineres funcionam juntos, visite [Visão geral do Azure Monitor para contêineres](../../azure-monitor/insights/container-insights-overview.md)
