---
title: Monitore o Azure Cosmos DB com o Monitor Azure para Cosmos DB (visualização)| Microsoft Docs
description: Este artigo descreve o recurso Azure Monitor for Cosmos DB que fornece aos proprietários do Cosmos DB uma rápida compreensão dos problemas de desempenho e utilização com suas contas do CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250669"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Explore o Monitor Azure para Azure Cosmos DB (visualização)

O Azure Monitor for Azure Cosmos DB (preview) fornece uma visão do desempenho geral, falhas, capacidade e saúde operacional de todos os seus recursos do Azure Cosmos DB em uma experiência interativa unificada. Este artigo irá ajudá-lo a entender os benefícios desta nova experiência de monitoramento, e como você pode modificar e adaptar a experiência para atender às necessidades únicas de sua organização.   

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, você deve entender como ela apresenta e visualiza informações. 

Ele entrega:

* **Na perspectiva em escala** de seus recursos do Azure Cosmos DB em todas as suas assinaturas em um único local, com a capacidade de escopo seletivo apenas para as assinaturas e recursos que você está interessado em avaliar.

* **Aprofunde a análise** de um determinado recurso do Azure CosmosDB para ajudar a diagnosticar problemas ou realizar análises detalhadas por categoria - utilização, falhas, capacidade e operações. Selecionar qualquer uma dessas opções fornece uma visão aprofundada das métricas relevantes do Azure Cosmos DB.  

* **Personalizável** - Essa experiência é construída em cima dos modelos da pasta de trabalho do Azure Monitor, permitindo que você altere quais métricas são exibidas, modifique ou defina limites que se alinhem com seus limites e, em seguida, salve em uma pasta de trabalho personalizada. Os gráficos nas cadernetas podem então ser fixados nos painéis do Azure.  

Esse recurso não exige que você habilite ou configure nada, essas métricas do Azure Cosmos DB são coletadas por padrão.

>[!NOTE]
>Não há custo para acessar esse recurso e você só será cobrado pelos recursos essenciais do Azure Monitor que você configura ou habilita, conforme descrito na página de detalhes de preços do [Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Ver métricas de utilização e desempenho para Azure Cosmos DB

Para visualizar a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as seguintes etapas.

1. Faça login no [portal Azure](https://portal.azure.com).

2. Procure **o Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e um dropdown que diz Serviços "Monitor" com uma imagem de estilo velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cosmos DB (visualização)**.

    ![Captura de tela da agenda de trabalho do Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Visão geral

Em **Visão Geral,** a tabela exibe métricas interativas do Azure Cosmos DB. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas de baixa:

* **Assinaturas** - apenas assinaturas que possuem um recurso Azure Cosmos DB são listadas.  

* **Cosmos DB** - Você pode selecionar todos, um subconjunto ou um único recurso Azure Cosmos DB.

* **Intervalo de tempo** - por padrão, exibe as últimas 4 horas de informações com base nas seleções correspondentes feitas.

O bloco de balcão as listas de baixa rola-up o número total de recursos do Azure Cosmos DB estão nas assinaturas selecionadas. Há codificação de cores condicional ou mapas de calor para colunas na caderneta de trabalho que relatam métricas de transação. A cor mais profunda tem o maior valor e uma cor mais clara é baseada nos valores mais baixos. 

Selecionar uma seta para dada ao lado de um dos recursos do Azure Cosmos DB revelará uma divisão das métricas de desempenho no nível de contêiner de banco de dados individual:

![Down expandido revelando contêineres individuais de banco de dados e divisão de desempenho associado](./media/cosmosdb-insights-overview/container-view.png)

Selecionar o nome de recurso Azure Cosmos DB destacado em azul irá levá-lo à **visão geral** padrão para a conta Azure Cosmos DB associada. 

### <a name="failures"></a>Falhas

Selecione **Falhas** na parte superior da página e a parte **Falhas** do modelo da carteira de trabalho é aberta. Ele mostra o total de solicitações com a distribuição das respostas que compõem esses pedidos:

![Captura de tela de falhas com discriminação por tipo de solicitação HTTP](./media/cosmosdb-insights-overview/failures.png)

| Código      |  Descrição       | 
|-----------|:--------------------|
| `200 OK`  | Uma das seguintes operações REST foram bem-sucedidas: </br>- Pegue um recurso. </br> - Coloque um recurso. </br> - POST em um recurso. </br> - POST em um recurso de procedimento armazenado para executar o procedimento armazenado.|
| `201 Created` | Uma operação POST para criar um recurso foi bem-sucedida. |
| `404 Not Found` | A operação está tentando atuar em um recurso que não existe mais. Por exemplo, o recurso pode já ter sido excluído. |

Para obter uma lista completa de códigos de status, consulte o artigo de código de [status Azure Cosmos DB HTTP](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacity

Selecione **Capacidade** na parte superior da página e a parte **Capacidade** do modelo da carteira de trabalho é aberta. Ele mostra quantos documentos você tem, seu crescimento de documentos ao longo do tempo, o uso de dados e a quantidade total de armazenamento disponível que você deixou.  Isso pode ser usado para ajudar a identificar possíveis problemas de armazenamento e utilização de dados.

![Caderneta de trabalho de capacidade](./media/cosmosdb-insights-overview/capacity.png) 

Assim como na pasta de trabalho da visão geral, selecionar o drop-down ao lado de um recurso Do Azure Cosmos DB na coluna **Assinatura** revelará uma divisão pelos contêineres individuais que compõem o banco de dados.

### <a name="operations"></a>Operações 

Selecione **Operações** na parte superior da página e a parte **de Operações** do modelo da carteira de trabalho é aberta. Ele lhe dá a capacidade de ver seus pedidos discriminados pelo tipo de solicitações feitas. 

Então, no exemplo abaixo `eastus-billingint` você vê que está recebendo predominantemente solicitações de leitura, mas com um pequeno número de upsert e criar solicitações. Considerando `westeurope-billingint` que é leitura apenas a partir de uma perspectiva de solicitação, pelo menos nas últimas quatro horas que a pasta de trabalho é atualmente escopo através de seu parâmetro de intervalo de tempo.

![Manual de operações](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Pino, exportação e expansão

Você pode fixar qualquer uma das seções métricas em [um Painel Do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone pushpin no canto superior direito da seção.

![Pino de seção métrica para exemplo de painel](./media/cosmosdb-insights-overview/pin.png)

Para exportar seus dados para o formato Excel, selecione o ícone de seta para baixo à esquerda do ícone pushpin.

![Ícone da carteira de trabalho de exportação](./media/cosmosdb-insights-overview/export.png)

Para expandir ou colapsar todas as exibições paradas na caderneta de trabalho, selecione o ícone expandir à esquerda do ícone de exportação:

![Expandir o ícone da agenda de trabalho](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Personalizar o Monitor Azure para Azure Cosmos DB (visualização)

Uma vez que essa experiência é construída em cima dos modelos da pasta de trabalho do Azure Monitor, você tem a capacidade de **personalizar** > **editar** e **salvar** uma cópia de sua versão modificada em uma pasta de trabalho personalizada. 

![Personalizar barra](./media/cosmosdb-insights-overview/customize.png)

As apostões de trabalho são salvas dentro de um grupo de recursos, seja na seção **Meus relatórios** que é privada para você ou na seção **Relatórios Compartilhados** que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a carteira de trabalho personalizada, você precisa ir à galeria da carteira de trabalho para lançá-la.

![Lançar galeria de livros de trabalho da barra de comando](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Próximas etapas

* Configure [alertas métricos](../platform/alerts-metric.md) e [notificações de saúde de serviços](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados para ajudar na detecção de problemas.

* Saiba os cenários que as cadernetas de trabalho são projetadas para suportar, como criar relatórios novos e personalizar relatórios existentes e muito mais, revisando [Criar relatórios interativos com as pasta de trabalho do Azure Monitor.](../app/usage-workbooks.md)
