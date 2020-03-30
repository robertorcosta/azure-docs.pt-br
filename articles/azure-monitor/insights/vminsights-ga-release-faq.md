---
title: Monitor Azure para VMs (GA) perguntas frequentes | Microsoft Docs
description: O Azure Monitor para VMs é uma solução do Azure que combina integridade e monitoramento de desempenho do sistema operacional da VM do Azure, bem como a descoberta automática dos componentes de aplicativo e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo responde a perguntas comuns sobre a liberação da GA.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283881"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Monitor azure para VMs geralmente disponíveis (GA) perguntas frequentes
Esta FAQ de Disponibilidade Geral abrange as mudanças que foram feitas no quarto trimestre de 2019 e no primeiro trimestre de 2020, conforme nos preparamos para o GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Atualizações para Monitor Azure para VMs
Lançamos uma nova versão do Azure Monitor para VMs em janeiro de 2020 antes do nosso anúncio ga. Os clientes que habilitarem o Monitor Azure para VMs agora receberão a versão GA, mas os clientes existentes que usam a versão do Monitor Azure para VMs a partir do quarto trimestre de 2019 e anteriormente serão solicitados a atualizar. Esta FAQ oferece orientação para executar um upgrade em escala se você tiver grandes implantações em vários espaços de trabalho.


Com essa atualização, os dados de desempenho do Azure Monitor for VMs são armazenados na mesma tabela *InsightsMetrics* [do Azure Monitor para contêineres,](container-insights-overview.md)o que torna mais fácil para você consultar os dois conjuntos de dados. Além disso, você é capaz de armazenar conjuntos de dados mais diversos que não poderíamos armazenar na tabela usada anteriormente. 

Nossas visualizações de desempenho estão agora usando os dados que armazenamos na tabela *InsightsMetrics.*  Se você ainda não atualizou para usar a solução VMInsights mais recente em seu espaço de trabalho, seus gráficos não exibirão mais informações.  Você pode atualizar a partir da nossa página **Get Started** conforme descrito abaixo.


## <a name="what-is-changing"></a>O que está mudando?
Lançamos uma nova solução, chamada VMInsights, que inclui recursos adicionais para coleta de dados, juntamente com um novo local para armazenar esses dados em seu espaço de trabalho do Log Analytics. 

No passado, habilitamos a solução ServiceMap em seu espaço de trabalho e contadores de desempenho de configuração em seu espaço de trabalho do Log Analytics para enviar os dados para a tabela *Perf.* Esta nova solução envia os dados para uma tabela chamada *InsightsMetrics* que também é usada pelo Azure Monitor para contêineres. Este esquema de tabela nos permite armazenar métricas adicionais e conjuntos de dados de serviço que não são compatíveis com o formato de tabela *Perf.*

Atualizamos nossos gráficos de desempenho para usar os dados que armazenamos na tabela *InsightsMetrics.* Você pode atualizar para usar a tabela *InsightsMetrics* da nossa página **Get Started** conforme descrito abaixo.


## <a name="how-do-i-upgrade"></a>Como faço para atualizar?
Quando um espaço de trabalho do Log Analytics é atualizado para a versão mais recente do Azure Monitor para VMs, ele atualizará o agente de dependência em cada uma das VMs anexadas a esse espaço de trabalho. Cada VM que necessita de atualização será identificada na guia **Get Started** no Azure Monitor para VMs no portal Azure. Quando você optar por atualizar uma VM, ele atualizará o espaço de trabalho para aquela VM, juntamente com quaisquer outras VMs anexadas a esse espaço de trabalho. Você pode selecionar uma única VM ou várias VMs, grupos de recursos ou assinaturas. 

Use o seguinte comando para atualizar um espaço de trabalho usando o PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>O que devo fazer sobre os contadores performance no meu espaço de trabalho se eu instalar a solução VMInsights?

O método anterior de habilitar o Monitor Azure para VMs usava contadores de desempenho em seu espaço de trabalho. A versão atual armazena esses `InsightsMetrics`dados em uma tabela chamada . Você pode optar por desativar esses contadores de desempenho em seu espaço de trabalho se você não precisar mais usá-los. 

>[!NOTE]
>Se você tiver Regras de alerta `Perf` que fazem referência a esses contadores na `InsightsMetrics` tabela, você precisa atualizá-los para referenciar novos dados armazenados na tabela. Consulte nossa documentação, por exemplo, consultas de log que você pode usar que se referem a esta tabela.
>

Se você decidir manter os contadores de desempenho ativados, você será cobrado pelos `Perf` dados ingeridos e armazenadoshttps://azure.microsoft.com/pricing/details/monitor/)na tabela com base nos preços [Log Analytics[.

## <a name="how-will-this-change-affect-my-alert-rules"></a>Como essa mudança afetará minhas regras de alerta?

Se você criou [alertas de](../platform/alerts-unified-log.md) log `Perf` que consultam os contadores de desempenho de segmentação de tabela que `InsightsMetrics` foram habilitados no espaço de trabalho, você deve atualizar essas regras para se referir à tabela. Essa orientação também se aplica `ServiceMapComputer_CL` a `ServiceMapProcess_CL`quaisquer regras de pesquisa `VMComputer` `VMProcess` de log usando e , porque esses conjuntos de dados estão se movendo para e tabelas.

Atualizaremos esta FAQ e nossa documentação para incluir regras de alerta de pesquisa de log de registro para os conjuntos de dados que coletamos.

## <a name="how-will-this-affect-my-bill"></a>Como isso afetará minha conta?

O faturamento ainda é baseado em dados ingeridos e retidos no espaço de trabalho do Log Analytics.

Os dados de desempenho do nível da máquina que coletamos são os `Perf` mesmos, são de tamanho semelhante aos dados que armazenamos na tabela, e custarão aproximadamente o mesmo valor.

## <a name="what-if-i-only-want-to-use-service-map"></a>E se eu só quiser usar o Mapa de Serviço?

Isso é bom. Você verá solicitações no portal Azure ao visualizar o Azure Monitor para VMs sobre a próxima atualização. Uma vez liberado, você receberá um prompt solicitando que você atualize para a nova versão. Se você preferir usar apenas o recurso [Mapas,](vminsights-maps.md) você pode optar por não atualizar e continuar a usar o recurso Mapas no Monitor Azure para VMs e a solução Mapa de Serviço acessada a partir de seu espaço de trabalho ou telha de painel.

Se você optou por ativar manualmente os contadores de desempenho em seu espaço de trabalho, então você poderá ver dados em alguns de nossos gráficos de desempenho visualizados no Azure Monitor. Assim que a nova solução for lançada, atualizaremos nossos `InsightsMetrics` gráficos de desempenho para consultar os dados armazenados na tabela. Se você quiser ver os dados dessa tabela nestes gráficos, você precisará atualizar para a nova versão do Azure Monitor para VMs.

As alterações para `ServiceMapComputer_CL` `ServiceMapProcess_CL` mover dados e afetarão tanto o Mapa de Serviço quanto o Monitor Azure para VMs, então você ainda precisa planejar essa atualização.

Se você optou por não atualizar para a solução **VMInsights,** continuaremos a fornecer versões herdadas de nossas cadernetas de desempenho que se referem a dados na `Perf` tabela.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Os conjuntos de dados do Mapa de Serviço também serão armazenados no InsightsMetrics?

Os conjuntos de dados não serão duplicados se você usar ambas as soluções. Ambas as ofertas compartilham os conjuntos de dados `VMProcess` que serão `VMConnection`armazenados `VMBoundPort` em `VMComputer` (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), e tabelas para armazenar os conjuntos de dados do mapa que coletamos.  

A `InsightsMetrics` tabela armazenará conjuntos de dados de VM, processo e serviço que coletamos e só serão preenchidos se você estiver usando o Monitor Azure para VMs e a solução VM Insights. A solução Mapa de Serviço não coletará ou armazenará dados na `InsightsMetrics` tabela.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Serei cobrado duas vezes se tiver as soluções Mapa de Serviço e VMInsights no meu espaço de trabalho?

Não, as duas soluções compartilham os `VMComputer` conjuntos de `VMProcess` dados do mapa `VMConnection`em `VMBoundPort`que armazenamos (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), e . Você não será cobrado duas vezes por esses dados se tiver ambas as soluções em seu espaço de trabalho.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Se eu remover a solução Mapa de Serviço ou VMInsights, ele removerá meus dados?

Não, as duas soluções compartilham os `VMComputer` conjuntos de `VMProcess` dados do mapa `VMConnection`em `VMBoundPort`que armazenamos (anteriormente ServiceMapComputer_CL), (anteriormente ServiceMapProcess_CL), e . Se você remover uma das soluções, esses conjuntos de dados notam que ainda há uma solução em vigor que usa os dados e ele permanece no espaço de trabalho do Log Analytics. Você precisa remover ambas as soluções do seu espaço de trabalho para que os dados sejam removidos dele.

## <a name="health-feature-is-in-limited-public-preview"></a>Recurso de saúde está em pré-visualização pública limitada

Recebemos um grande feedback dos clientes sobre nosso conjunto de recursos da VM Health. Há muito interesse em torno desse recurso e excitação sobre seu potencial para apoiar o monitoramento de fluxos de trabalho. Estamos planejando fazer uma série de mudanças para adicionar funcionalidade e abordar o feedback que recebemos. 

Para minimizar o impacto dessas mudanças para novos clientes, mudamos esse recurso para uma **pré-visualização pública limitada.** Esta atualização aconteceu em outubro de 2019.

Planejamos relançar este recurso Health em 2020, depois que o Azure Monitor for VMs estiver em GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Como os clientes existentes acessam o recurso Saúde?

Os clientes existentes que estão usando o recurso Saúde continuarão a ter acesso a ele, mas não serão oferecidos a novos clientes.  

Para acessar o recurso, você pode `feature.vmhealth=true` adicionar o seguinte [https://portal.azure.com](https://portal.azure.com)sinalizador de recurso à URL do portal Azure . Exemplo `https://portal.azure.com/?feature.vmhealth=true`.

Você também pode usar esta url curta, que [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)define o sinalizador de recurso automaticamente: .

Como cliente existente, você pode continuar a usar o recurso Saúde em VMs que estão conectados a uma configuração de espaço de trabalho existente com a funcionalidade de saúde.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Eu uso VM Health agora com um ambiente e gostaria de implantá-lo para um novo

Se você é um cliente existente que está usando o recurso Saúde e deseja usá-lo para um novo lançamento, entre em vminsights@microsoft.com contato conosco para solicitar instruções.

## <a name="next-steps"></a>Próximas etapas

Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-enable-overview.md).
