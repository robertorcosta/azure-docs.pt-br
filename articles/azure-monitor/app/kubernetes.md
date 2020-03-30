---
title: Use o Application Insights para monitorar seu Azure Kubernetes Service (AKS) ou outros aplicativos hospedados do Kubernetes - Azure Monitor | Microsoft Docs
description: O Azure Monitor usa a tecnologia de malha de serviço, Istio, em seu cluster Kubernetes para fornecer monitoramento de aplicativos para qualquer aplicativo hospedado no Kubernetes. Isso permite que você colete a telemetria application insights relativa sustais referentes a solicitações de entrada e saída de e para pods em execução em seu cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132355"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Monitoramento de aplicativos de instrumentação zero para aplicativos hospedados do Kubernetes

> [!IMPORTANT]
> Esta funcionalidade está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Monitor agora aproveita a tecnologia de malha de serviço no cluster Kubernetes para fornecer monitoramento de aplicativos fora da caixa para qualquer aplicativo hospedado do Kubernetes. Com recursos padrão do Application Insight, como [o Mapa de Aplicativos](../../azure-monitor/app/app-map.md) para modelar suas dependências, fluxo de [métricas ao vivo](../../azure-monitor/app/live-stream.md) para monitoramento em tempo real, visualizações poderosas com o painel de controle [padrão,](../../azure-monitor/app/overview-dashboard.md) [Metric Explorer](../../azure-monitor/platform/metrics-getting-started.md)e [Workbooks.](../../azure-monitor/app/usage-workbooks.md) Esse recurso ajudará os usuários a identificar gargalos de desempenho e pontos de falha em todas as suas cargas de trabalho kubernetes dentro de um namespace kubernetes selecionado. Ao capitalizar os investimentos existentes em malha de serviços com tecnologias como a Istio, o Azure Monitor permite o monitoramento automático de aplicativos com instrumentos automáticos sem qualquer modificação no código do seu aplicativo.

> [!NOTE]
> Esta é uma das muitas maneiras de realizar o monitoramento de aplicativos no Kubernetes.Você também pode instrumentar qualquer aplicativo hospedado em Kubernetes usando o [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) sem a necessidade de uma malha de serviço. Para monitorar kubernetes sem instrumentar o aplicativo com um SDK você pode usar o método abaixo.

## <a name="prerequisites"></a>Pré-requisitos

- Um [aglomerado Kubernetes.](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)
- Acesso do console ao cluster para executar *kubectl*.
- Um [recurso de criterioso de aplicativos](create-new-resource.md)
- Tenha uma malha de serviço. Se o seu cluster não tiver istio implantado, você pode aprender como [instalar e usar istio no Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funcionalidades

Usando o monitoramento de aplicativos de instrumentação zero para aplicativos hospedados kubernetes, você poderá usar:

- [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
- [Métricas de transmissão ao vivo](../../azure-monitor/app/live-stream.md)
- [Painéis](../../azure-monitor/app/overview-dashboard.md)
- [Explorador de métricas](../../azure-monitor/platform/metrics-getting-started.md)
- [Rastreamento distribuído](../../azure-monitor/app/distributed-tracing.md)
- [Monitoramento de transações de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Etapas de instalação

Para habilitar a solução, estaremos executando as seguintes etapas:
- Implantar o aplicativo (se ainda não estiver implantado).
- Certifique-se de que a aplicação faz parte da malha de serviço.
- Observe a telemetria coletada.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configure seu aplicativo para trabalhar com uma malha de serviço

Istio suporta duas maneiras de [instrumentar uma cápsula](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Na maioria dos casos, é mais fácil marcar o namespace kubernetes contendo sua aplicação com a etiqueta *de injeção de istio:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Uma vez que a malha de serviço retira dados do fio, não podemos interceptar o tráfego criptografado. Para tráfego que não saia do cluster, use um protocolo não criptografado (por exemplo, HTTP). Para tráfego externo que deve ser criptografado, considere configurar a [terminação TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de ingress.

Os aplicativos que correm fora da malha de serviço não são afetados.

### <a name="deploy-your-application"></a>Implantar seu aplicativo

- Implante seu aplicativo no *namespace do meu aplicativo.namespace.* Se o aplicativo já estiver implantado e você tiver seguido o método automático de injeção de sidecar descrito acima, você precisa recriar pods para garantir que istio injete seu sidecar; iniciar uma atualização de rolamento ou excluir pods individuais e esperar que eles sejam recriados.
- Certifique-se de que sua aplicação está em conformidade com [os requisitos da Istio.](https://istio.io/docs/setup/kubernetes/prepare/requirements/)

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implantar monitoramento de aplicativos de instrumentação zero para aplicativos hospedados do Kubernetes

1. Baixe e extraia uma versão [ *do adaptador Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue até */src/kubernetes/* dentro da pasta de versão.
3. Editar *aplicativo-insights-istio-mixer-adapter-deployment.yaml*
    - editar o valor de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente para conter a chave de instrumentação do recurso Application Insights no portal Azure para conter a telemetria.
    - Se necessário, edite o valor da variável de ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* para conter uma lista separada por comuma de namespaces para os quais você gostaria de ativar o monitoramento. Deixe em branco para monitorar todos os namespaces.
4. Aplique *todos os* arquivos YAML encontrados em *src/kubernetes/* executando o seguinte (você ainda deve estar dentro *de /src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar implantação

- Certifique-se de que o adaptador Application Insights foi implantado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessário ajuste fino. Para incluir ou excluir a telemetria para que um pod individual seja coletado, use *o rótulo appinsights/monitoring.enabled* nesse pod. Isso terá prioridade sobre todas as configurações baseadas em namespace. Defina *appinsights/monitoring.habilitado* para *incluir* o pod e *falso* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Exibir telemetria do Application Insights

- Gerar uma solicitação de amostra contra seu aplicativo para confirmar que o monitoramento está funcionando corretamente.
- Dentro de 3-5 minutos, você deve começar a ver a telemetria aparecer no portal Azure. Certifique-se de verificar a seção Mapa de *Aplicativos* do seu recurso Application Insights no Portal.

## <a name="troubleshooting"></a>Solução de problemas

Abaixo está o fluxo de solução de problemas para usar quando a telemetria não aparecer no portal Azure como esperado.

1. Certifique-se de que o aplicativo está em carga e está enviando/recebendo solicitações em HTTP simples. Uma vez que a telemetria é retirada do fio, o tráfego criptografado não é suportado. Se não houver solicitações de entrada ou saída, também não haverá telemetria.
2. Certifique-se de que a chave de instrumentação correta seja fornecida na variável *ambiente ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* em *application-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação é encontrada na guia *Visão geral* do recurso Application Insights no portal Azure.
3. Certifique-se de que o namespace kubernetes correto é fornecido na variável *ambiente ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* em *application-insights-istio-mixer-adapter-deployment.yaml*. Deixe em branco para monitorar todos os namespaces.
4. Certifique-se de que os pods do seu aplicativo foram injetados por Istio. Verifique se o sidecar de Istio existe em cada cápsula.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se há um contêiner chamado *istio-proxy* em execução no pod.

5. Veja os traços do adaptador Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de itens de telemetria recebidos é atualizada uma vez por minuto. Se não crescer minuto a minuto, nenhuma telemetria será enviada ao adaptador por Istio.
   Procure por quaisquer erros no registro.
6. Se foi estabelecido que o adaptador *Application Insight for Kubernetes* não está sendo alimentado com telemetria, verifique os registros do Mixer da Istio para descobrir por que ele não está enviando dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure por quaisquer erros, especialmente relativos às comunicações com o adaptador *de adaptador applicationinsights.*

## <a name="faq"></a>Perguntas frequentes

Para obter as informações mais recentes sobre o progresso deste projeto, visite o [adaptador Application Insights para o GitHub do projeto Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* arquivo YAML encontrado em *src/kubernetes/* executar:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure Monitor e os contêineres trabalham juntos, visite [o Azure Monitor para ver como os contêineres são visualizados](../../azure-monitor/insights/container-insights-overview.md)
