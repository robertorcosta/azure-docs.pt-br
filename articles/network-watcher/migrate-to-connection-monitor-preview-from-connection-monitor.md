---
title: Migrar para o monitor de conexão (versão prévia) do monitor de conexão
titleSuffix: Azure Network Watcher
description: Saiba como migrar para o monitor de conexão (versão prévia) do monitor de conexão.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701410"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrar para o monitor de conexão (versão prévia) do monitor de conexão

Você pode migrar os monitores de conexão existentes para o monitor de conexão novo e aprimorado (versão prévia) em um único clique e sem tempo de inatividade. Para saber mais sobre os benefícios, você pode ler o [Monitor de conexão (versão prévia)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Pontos-chave a serem observados

* Os agentes e as configurações de firewall funcionarão como estão (sem toque necessário) 
* Os monitores de conexão existentes serão mapeados para o monitor de conexão (visualização) – > grupo de teste – > formato de teste. Os usuários podem clicar em *Editar* para exibir e modificar as propriedades do novo monitor de conexão e baixar o modelo para fazer alterações no monitor de conexão e enviá-lo por meio de Azure Resource Manager. 
* As máquinas virtuais do Azure com extensão do observador de rede enviam dados para o espaço de trabalho e métricas. Os monitores de conexão disponibilizarão os dados por meio das novas métricas (ChecksFailedPercent (visualização) e RoundTripTimeMs (visualização)) em vez das métricas de parada antiga (ProbesFailedPercent e AverageRoundtripMs) 
* Dados de monitoramento
    * Alertas – serão migrados para novas métricas como parte da migração
    * Painéis e integrações – você precisará editar manualmente o conjunto de métricas. 
    
## <a name="prerequisites"></a>Pré-requisitos

Se estiver usando espaço de trabalho personalizado, verifique se o observador de rede está habilitado na assinatura e na região do espaço de trabalho Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Etapas para migrar do monitor de conexão para o monitor de conexão (versão prévia)

1. Clique em "Monitor de conexão", navegue até "migrar monitores de conexão" para migrar monitores de conexão de uma solução mais antiga para a mais recente.

    ![Captura de tela mostrando migrar monitores de conexão para a visualização do monitor de conexão](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecione monitores de assinatura e conexão e clique em "migrar selecionados". Em um clique em migrar monitores de conexão existentes para o monitor de conexão (versão prévia) 
1. Você pode personalizar as propriedades do monitor de conexão, alterar o espaço de trabalho padrão, baixar o modelo e verificar o status da migração. 
1. Após o início da migração, ocorrem as seguintes alterações: 
    1. Azure Resource Manager alterações de recurso para o monitor de conexão mais recente
        1. O nome, a região e a assinatura do monitor de conexão permanecem inalterados. Portanto, não há nenhum impacto na ID do recurso.
        1. A menos que personalizado, um espaço de trabalho padrão do Log Analytics é criado na região e na assinatura do monitor de conexão. Esse espaço de trabalho é onde os dados de monitoramento serão armazenados. Os dados de resultado do teste também serão armazenados em métricas.
        1. Cada teste é migrado para um grupo de teste chamado * defaulttesto *
        1.  Os pontos de extremidade de origem e destino são criados e usados no grupo de teste criado. Os nomes padrão são *defaultSourceEndpoint* e *defaultDestinationEndpoint*
        1. A porta de destino e o intervalo de investigação são movidos para a configuração de teste chamada *defaultTestConfiguration*. Com base nos valores de porta, o protocolo é definido. Os limites de êxito e outras propriedades opcionais são deixados em branco.
    1. Os alertas de métrica são migrados para alertas de métrica do monitor de conexão (versão prévia). As métricas são diferentes <link to metric section in the doc> , portanto, a alteração
    1. Os monitores de conexão migrados não aparecerão na solução de monitor de conexão mais antiga, agora só estarão disponíveis para uso no monitor de conexão (versão prévia)
    1. Todas as integrações externas, como painéis em Power BI, Grafana, integrações com sistemas SIEM, precisarão ser migradas diretamente pelo usuário. Essa é a única etapa manual que o usuário precisa executar para migrar sua configuração.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como migrar de monitor de desempenho de rede para o monitor de conexão (versão prévia)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Saiba [como criar um monitor de conexão (versão prévia) usando portal do Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
