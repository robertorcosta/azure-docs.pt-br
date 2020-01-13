---
title: Perguntas frequentes sobre o Azure Monitor para contêineres | Microsoft Docs
description: O Azure Monitor para contêineres é uma solução que monitora a integridade dos clusters AKS e Instâncias de Contêiner no Azure. Este artigo responde a perguntas comuns.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0984de51221c506bb1824e4dcfd93eef56453a4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405077"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Monitor para contêineres

Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para contêineres. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo os valores de Propriedade Image e Name preenchidos quando eu consulta a tabela ContainerLog.

Para a versão do agente ciprod12042019 e posteriores, por padrão, essas duas propriedades não são preenchidas para cada linha de log para minimizar o custo incorrido nos dados de log coletados. Há duas opções para consultar a tabela que inclui essas propriedades com seus valores:

### <a name="option-1"></a>Opção 1 

Ingresse outras tabelas para incluir esses valores de propriedade nos resultados.

Modifique suas consultas para incluir as propriedades Image e ImageTag da tabela ```ContainerInventory``` unindo a propriedade ContainerId. Você pode incluir a propriedade Name (como apareceu anteriormente na tabela ```ContainerLog```) do campo ContaineName da tabela KubepodInventory unindo a propriedade ContainerId. Essa é a opção recomendada.

O exemplo a seguir é uma consulta detalhada de exemplo que explica como obter esses valores de campo com junções.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>Opção 2

Reabilitar a coleção para essas propriedades para cada linha de log de contêiner.

Se a primeira opção não for conveniente devido a alterações de consulta envolvidas, você poderá reabilitar a coleta desses campos habilitando a configuração ```log_collection_settings.enrich_container_logs``` no mapa de configuração do agente, conforme descrito nas [definições de configuração de coleta de dados](./container-insights-agent-config.md).

> [!NOTE]
> A segunda opção não é recomendável com clusters grandes que têm mais de 50 nós, pois ele gera chamadas de servidor de API de cada nó > no cluster para executar esse enriquecimento. Essa opção também aumenta o tamanho dos dados para cada linha de log coletada.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Posso exibir as métricas coletadas no Grafana?

Azure Monitor para contêineres dá suporte à exibição de métricas armazenadas em seu espaço de trabalho Log Analytics em painéis do Grafana. Fornecemos um modelo que você pode baixar do [repositório do painel](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do Grafana para começar e fazer referência para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em painéis de Grafana personalizados. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorar meu cluster do AKS-Engine com Azure Monitor para contêineres?

Azure Monitor para contêineres dá suporte ao monitoramento de cargas de trabalho de contêiner implantadas em cluster (s) AKS (anteriormente conhecido como ACS-Engine) hospedados no Azure. Para obter mais detalhes e uma visão geral das etapas necessárias para habilitar o monitoramento para esse cenário, consulte [usando Azure monitor para contêineres para AKs-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados em meu espaço de trabalho do Log Analytics?

Se não for possível ver dados no espaço de trabalho Log Analytics em um determinado horário, você pode ter atingido o limite de 500 MB padrão ou o limite diário especificado para controlar a quantidade de dados a serem coletados diariamente. Quando o limite for atingido para o dia, a coleta de dados será interrompida e retomada somente no dia seguinte. Para examinar o uso de dados e atualizar para um tipo de preço diferente com base nos padrões de uso previstos, consulte [uso e custo de dados de log](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os Estados do contêiner especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres parados e em execução. A tabela é preenchida por um fluxo de trabalho dentro do agente que consulta o docker por todos os contêineres (em execução e parados) e encaminha esses dados ao espaço de trabalho do Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como fazer resolver erro de *registro de assinatura ausente* ?

Se você receber o erro **registro de assinatura ausente para Microsoft. OperationsManagement**, você poderá resolvê-lo registrando o provedor de recursos **Microsoft. OperationsManagement** na assinatura em que o espaço de trabalho está definido. A documentação para saber como fazer isso pode ser encontrada [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para clusters AKS habilitados para RBAC?

A solução de monitoramento de contêiner não dá suporte a RBAC, mas tem suporte com Azure Monitor para contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados desses clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como habilito a coleta de log para contêineres no namespace kube-system por meio do Helm?

A coleta de log de contêineres no namespace kube-system está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para obter mais informações, consulte a página [Azure monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) github. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como posso atualizar o omsagent para a versão mais recente?

Para saber como atualizar o agente, confira [Gerenciamento de agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Como habilito o registro em log de várias linhas?

No momento o Azure Monitor para contêineres não é compatível com registro em log de várias linhas, mas há soluções alternativas disponíveis. Você pode configurar todos os serviços para gravar em formato JSON e, em seguida, o Docker/Moby vai gravá-los como uma única linha.

Por exemplo, você pode encapsular seu log como um objeto JSON, conforme mostrado no exemplo de um aplicativo do Node.js abaixo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados serão parecidos com o exemplo a seguir no Azure Monitor para logs quando você fizer a consulta:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obter uma visão detalhada do problema, examine o [link do GitHub](https://github.com/moby/moby/issues/22920)a seguir.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como fazer resolver erros do Azure AD quando habilito logs dinâmicos? 

Você pode ver o seguinte erro: **a URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: ' < ID do aplicativo\>'** . A solução para solucioná-lo pode ser encontrada no artigo [como exibir dados de contêiner em tempo real com Azure monitor para contêineres](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster após a integração?

Se, depois de habilitar Azure Monitor para contêineres para um cluster AKS, você excluirá o espaço de trabalho Log Analytics ao qual o cluster estava enviando seus dados, ao tentar atualizar o cluster, ele falhará. Para contornar isso, você precisará desabilitar o monitoramento e reabilitá-lo fazendo referência a um espaço de trabalho válido diferente em sua assinatura. Quando você tenta executar a atualização do cluster novamente, ele deve processar e concluir com êxito.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quais portas e domínios eu preciso abrir/Adicionar à lista de permissões para o agente?

Consulte os [requisitos de firewall de rede](container-insights-onboard.md#network-firewall-requirements) para as informações de configuração de proxy e firewall necessárias para o agente em contêineres com o Azure, o governo dos EUA do Azure e as nuvens do Azure China.

## <a name="next-steps"></a>Próximos passos

Para começar a monitorar seu cluster do AKS, veja [Como integrar o Azure Monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento. 
