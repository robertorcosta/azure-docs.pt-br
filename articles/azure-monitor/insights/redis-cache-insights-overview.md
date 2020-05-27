---
title: Azure Monitor para Cache do Azure para Redis (versão prévia) | Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor para Cache do Azure para Redis, que fornece aos proprietários do Cache do Azure para Redis uma rápida compreensão dos problemas de desempenho e utilização.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210968"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Explorar o Azure Monitor para Cache do Azure para Redis (versão prévia)

O Azure Monitor para Cache do Azure para Redis (versão prévia) fornece uma exibição do desempenho geral, das falhas, da capacidade e da integridade operacional de todos os seus recursos do Cache do Azure para Redis em uma experiência interativa unificada. Este artigo o ajudará a entender os benefícios dessa nova experiência de monitoramento e como você pode modificar e adaptar a experiência para atender às necessidades exclusivas de sua organização.

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, você deve entender como ela apresenta e visualiza informações.

Ela oferece:

* **Perspectiva em escala** de seus recursos do Cache do Azure para Redis em todas as suas assinaturas em um único local, com a capacidade de definir o escopo seletivamente apenas com as assinaturas e os recursos que você está interessado em avaliar.

* **Análise de busca detalhada** de um recurso específico do Cache do Azure para Redis para ajudar a diagnosticar problemas ou executar uma análise detalhada por categoria – utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções fornece uma visão detalhada das métricas relevantes.  

* **Personalizável** – essa experiência é criada sobre os modelos de pasta de trabalho do Azure Monitor, permitindo que você altere quais métricas são exibidas, modifique ou defina os limites que se alinham com seus limites e, em seguida, salve em uma pasta de trabalho personalizada. Os gráficos nas pastas de trabalho podem ser fixados a painéis do Azure.  

Esse recurso não exige que você habilite nem configure nada, essas métricas do Cache do Azure para Redis são coletadas por padrão.

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você será cobrado somente pelos recursos essenciais do Azure Monitor que você configurar ou habilitar, conforme descrito na página [Detalhes de preços de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Exibir métricas de desempenho e utilização para o Cache do Azure para Redis

Para exibir a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra “Monitor” e uma lista suspensa com os dizeres “Monitor” de Serviços com uma imagem semelhante a um velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cache do Azure para Redis (versão prévia)** .

Se essa opção não estiver presente, clique em **Mais** e selecione **Cache do Azure para Redis**.

### <a name="overview"></a>Visão geral

Em **Visão geral**, a tabela exibe métricas interativas do Cache do Azure para Redis. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas** – somente assinaturas que têm um recurso do Cache do Azure para Redis são listadas.  

* **Cache do Azure para Redis** – você pode selecionar todos, um subconjunto ou um único cache do recurso Cache do Azure para Redis.

* **Intervalo de tempo** – por padrão, exibe as últimas quatro horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de recursos do Cache do Azure para Redis que estão nas assinaturas selecionadas. Há codificação de cor condicional ou mapas de calor para colunas na pasta de trabalho que relatam métricas de transação. A cor mais intensa tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos.

A seleção de uma seta suspensa ao lado de um dos recursos do Cache do Azure para Redis revelará uma análise das métricas de desempenho no nível de recurso individual:

![Captura da experiência de Visão geral](./media/redis-cache-insights-overview/overview.png)

Selecionar o nome do recurso do Cache do Azure para Redis realçado em azul levará você para a **Visão geral** padrão para a conta associada. Ela mostra `Used Memory`, `Used Memory Percentage`, `Server Load`, `Server Load Timeline`, `CPU`, `Connected Clients`, `Cache Misses`, `Errors (Max)`.

### <a name="operations"></a>Operações

Selecione **Operações** na parte superior da página e a parte **Operações** do modelo da pasta de trabalho é aberta. Ela mostra `Total Operations`, `Total Operations Timeline`, `Operations Per Second`, `Gets`, `Sets`.

![Captura da experiência de Visão geral](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Uso

Selecione **Uso** na parte superior da página e a parte **Uso** do modelo da pasta de trabalho é aberta. Ela mostra `Cache Read`, `Cache Read Timeline`, `CacheWrite`, `CacheHits`, `Cache Misses`.

![Captura da experiência de Visão geral](./media/redis-cache-insights-overview/usage.png)

Selecione **Falhas** na parte superior da página e a parte **Falhas** do modelo da pasta de trabalho é aberta. Ela mostra `Total Errors`, `Failover/Errors`, `UnresponsiveClient/Errors`, `RDB/Errors`, `AOF/Errors`, `Export/Errors`, `Dataloss/Errors`, `Import/Errors`.

![Captura de tela de falhas com divisão por tipo de solicitação HTTP](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Definições de Métrica

Para obter uma lista completa das definições de métrica que compõem essas pastas de trabalho, confira o [artigo de métricas disponíveis e intervalos de relatório](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Fixar, exportar e expandir

Você pode fixar qualquer uma das seções de métrica em um [Painel do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone de alfinete na parte superior direita da seção.

![Exemplo de fixação no painel na seção de métrica](./media/cosmosdb-insights-overview/pin.png)

Para exportar os dados para o formato do Excel, selecione o ícone de seta para baixo à esquerda do ícone de alfinete.

![Exportar ícone de pasta de trabalho](./media/cosmosdb-insights-overview/export.png)

Para expandir ou recolher todas as exibições suspensas na pasta de trabalho, selecione o ícone de expansão à esquerda do ícone de exportação:

![Ícone expandir pasta de trabalho](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Personalizar o Azure Monitor para Cache do Azure para Redis (versão prévia)

Já que essa experiência foi criada sobre os modelos de pasta de trabalho Azure Monitor, você tem a capacidade de **Personalizar** > **Editar** e **Salvar** uma cópia da sua versão modificada em uma pasta de trabalho personalizada. 

![Personalizar barra](./media/cosmosdb-insights-overview/customize.png)

As pastas de trabalho são salvas em um grupo de recursos, seja na seção **Meus Relatórios**, que é particular a você, ou na seção **Relatórios Compartilhados**, que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a galeria de pastas de trabalho para iniciá-la.

![Iniciar galeria de pastas de trabalho na barra de comandos](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados a fim de auxiliar na detecção de problemas.

* Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../app/usage-workbooks.md).
