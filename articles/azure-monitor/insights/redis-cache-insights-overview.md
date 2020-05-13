---
title: Azure Monitor para o cache do Azure para Redis (visualização) | Microsoft Docs
description: Este artigo descreve o Azure Monitor para o recurso de cache Redis que fornece o cache do Azure para proprietários do Redis com uma rápida compreensão dos problemas de desempenho e utilização.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210968"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Explorar Azure Monitor para o cache do Azure para Redis (versão prévia)

Azure Monitor para o cache do Azure para Redis (versão prévia) fornece uma visão do desempenho geral, falhas, capacidade e integridade operacional de todo o cache do Azure para recursos do Redis em uma experiência interativa unificada. Este artigo o ajudará a entender os benefícios dessa nova experiência de monitoramento e como você pode modificar e adaptar a experiência para atender às necessidades exclusivas de sua organização.

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, você deve entender como ele apresenta e visualiza informações.

Ele fornece:

* **Na perspectiva da escala** do cache do Azure para recursos de Redis em todas as suas assinaturas em um único local, com a capacidade de fazer o escopo seletivo somente para as assinaturas e os recursos que você está interessado em avaliar.

* **Análise de busca detalhada** de um determinado cache do Azure para o recurso Redis para ajudar a diagnosticar problemas ou executar uma análise detalhada por categoria-utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções fornece uma visão detalhada das relevantes.  

* **Personalizável** – essa experiência é criada sobre Azure monitor modelos de pasta de trabalho, permitindo que você altere quais métricas são exibidas, modifique ou defina os limites que se alinham com seus limites e, em seguida, salve em uma pasta de trabalho personalizada. Os gráficos nas pastas de trabalho podem ser fixados nos painéis do Azure.  

Esse recurso não exige que você habilite ou configure nada, esses cache do Azure para Redis são coletados por padrão.

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você só será cobrado pelos recursos essenciais Azure Monitor que você configurar ou habilitar, conforme descrito na página de [detalhes de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Exibir métricas de desempenho e utilização para o cache do Azure para Redis

Para exibir a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Procure **Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e uma lista suspensa que diz aos serviços "Monitor" com uma imagem de estilo velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **cache do Azure para Redis (versão prévia)**.

Se essa opção não estiver presente, clique em **mais** e selecione **cache do Azure para Redis**.

### <a name="overview"></a>Visão geral

Em **visão geral**, a tabela exibe o cache interativo do Azure para métricas Redis. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas-somente** as assinaturas que têm um recurso de cache do Azure para Redis estão listadas.  

* **Cache do Azure para Redis** -você pode selecionar todos, um subconjunto ou um único cache do Azure para o recurso Redis.

* **Intervalo de tempo** – por padrão, exibe as últimas 4 horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de cache do Azure para recursos Redis estão nas assinaturas selecionadas. Há codificação de cor condicional ou calor para colunas na pasta de trabalho que relatam métricas de transação. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A seleção de uma seta suspensa ao lado de um dos recursos de cache do Azure para Redis revelará uma análise das métricas de desempenho no nível de recurso individual:

![Captura de tela da experiência de visão geral](./media/redis-cache-insights-overview/overview.png)

Selecionar o nome do recurso de cache do Azure para Redis realçado em azul levará você para a **visão geral** padrão da conta associada. Ele mostra,,,,,, `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` `CPU` `Connected Clients` `Cache Misses` , `Errors (Max)` .

### <a name="operations"></a>Operações

Selecione **operações** na parte superior da página e a parte de **operações** do modelo da pasta de trabalho será aberta. Ele mostra `Total Operations` , `Total Operations Timeline` ,,, `Operations Per Second` `Gets` `Sets` .

![Captura de tela da experiência de visão geral](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Uso

Selecione **uso** na parte superior da página e a parte de **uso** do modelo da pasta de trabalho será aberta. Ele mostra `Cache Read` , `Cache Read Timeline` ,,, `CacheWrite` `CacheHits` `Cache Misses` .

![Captura de tela da experiência de visão geral](./media/redis-cache-insights-overview/usage.png)

Selecione **falhas** na parte superior da página e a parte de **falhas** do modelo da pasta de trabalho é aberta. Ele mostra,,,,,, `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` `AOF/Errors` `Export/Errors` `Dataloss/Errors` , `Import/Errors` .

![Captura de tela de falhas com divisão por tipo de solicitação HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições de métrica

Para obter uma lista completa das definições de métrica que compõem essas pastas de trabalho, confira o [artigo métricas disponíveis e intervalos de relatório](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Fixar, exportar e expandir

Você pode fixar qualquer uma das seções de métrica em um [painel do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone de pino na parte superior direita da seção.

![Exemplo de PIN da seção de métrica para painel](./media/cosmosdb-insights-overview/pin.png)

Para exportar os dados para o formato do Excel, selecione o ícone de seta para baixo à esquerda do ícone de pino.

![Ícone exportar pasta de trabalho](./media/cosmosdb-insights-overview/export.png)

Para expandir ou recolher todas as exibições suspensas na pasta de trabalho, selecione o ícone de expansão à esquerda do ícone de exportação:

![Ícone expandir pasta de trabalho](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Personalizar Azure Monitor para o cache do Azure para Redis (versão prévia)

Como essa experiência é criada sobre os modelos de pasta de trabalho Azure monitor, você tem a capacidade de **Personalizar**  >  **Editar** e **salvar** uma cópia da sua versão modificada em uma pasta de trabalho personalizada. 

![Personalizar barra](./media/cosmosdb-insights-overview/customize.png)

As pastas de trabalho são salvas em um grupo de recursos, na seção **meus relatórios** , que é particular para você ou na seção **relatórios compartilhados** , que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a Galeria de pastas de trabalho para iniciá-la.

![Iniciar Galeria de pastas de trabalho na barra de comandos](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados para auxiliar na detecção de problemas.

* Conheça os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar novos e personalizar relatórios existentes e muito mais examinando [criar relatórios interativos com pastas de trabalho do Azure monitor](../app/usage-workbooks.md).
