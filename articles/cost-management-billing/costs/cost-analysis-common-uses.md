---
title: Usos comuns da análise de custo no Gerenciamento de Custos do Azure
description: Este artigo explica como você pode obter resultados para tarefas comuns de análise de custo no Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 901f271ac401cb985e59c434b9e6d7f8db03889f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79203093"
---
# <a name="common-cost-analysis-uses"></a>Usos comuns da análise de custo

Os usuários do Gerenciamento de Custos do Azure muitas vezes desejam respostas a perguntas que muitas outras pessoas também fazem. Este artigo explica como obter resultados para tarefas comuns de análise de custo no Gerenciamento de Custos.

## <a name="view-cost-breakdown-by-azure-service"></a>Exibir detalhamento de custos por serviço do Azure

A exibição de custos por um serviço do Azure pode ajudá-lo a entender melhor as partes de sua infraestrutura que custam mais. Por exemplo, os custos de computação de VMs podem ser pequenos. Ainda assim, você pode acumular custos de rede significativos devido à quantidade de informações emitidas por meio das VMs. Entender os principais elementos geradores de custo de seus serviços do Azure é essencial para que você possa ajustar, conforme necessário, a utilização do serviço.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Custo por serviço** e, em seguida, agrupe por **Camada de serviço**.
1. Altere a exibição para **Tabela**.

![Detalhamento de custos por serviço do Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Exibir detalhamento de custos por recurso do Azure

Seus serviços são criados com recursos do Azure. Examinar os custos com base nos recursos pode ajudá-lo a identificar rapidamente os elementos que mais contribuem para seus custos. Se um serviço tiver recursos muito caros, considere fazer alterações para reduzir os custos.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Custo por recurso**.
1. Altere a exibição para **Tabela**.

![Exibir detalhamento de custos por recurso do Azure](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Exibir detalhamento de custos pelas dimensões selecionadas

As dimensões permitem que você organize seus custos com base em vários valores de metadados mostrados em seus encargos. Por exemplo, você pode agrupar seus custos por localização.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione o filtro **Agrupar por**.  
    ![Selecionar um item de Agrupar por](./media/cost-analysis-common-uses/group-by.png)
1. Opcionalmente, você salva a exibição para uso posterior.
1. Clique em um gráfico de pizza abaixo do gráfico para exibir dados mais detalhados.  
    ![Exibir detalhamento de custos pelas dimensões selecionadas](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Exibir custos por dia ou por mês

A observação dos custos diários e mensais pode ajudá-lo a entender melhor se há uma hora da semana ou do ano em que os custos são maiores. Se você tiver mais tráfego de clientes em um período de Natal, isso levará a um aumento correspondente nos custos do Azure? A sexta-feira é um dia mais dispendioso do que a segunda?

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Defina a **Granularidade** como **Mensal** ou **Diária**.

![Exibir os custos por dia](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Exibir os custos de uma marca específica

Muitos usuários do Azure aplicam marcas a seus recursos, como um centro de custo ou um ambiente de desenvolvimento (produção e teste) para categorizar melhor os encargos. As marcas aparecem como uma dimensão na análise de custo. Você pode usar a dimensão para obter informações sobre suas categorizações de marcação personalizadas.

O suporte para marcas aplica-se ao uso relatado *depois* da aplicação da marca ao recurso. As marcas não são aplicadas retroativamente para rollups de custo.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione **Agrupar por** para sua marca.

![Exibir os custos de uma marca específica](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Faça o download dos seus detalhes de uso

O arquivo de relatório de detalhes de uso, no formato CSV, fornece um detalhamento de todos os encargos acumulados para uma fatura. Você pode usar o relatório para compará-los àqueles em sua fatura e, assim, entendê-los melhor. Cada encargo cobrado em sua fatura corresponde aos encargos detalhados no relatório de uso.

1. Na portal do Azure, navegue até a guia **Uso e Encargos** para uma conta de cobrança ou assinatura para cobrança. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Cobrança** > **Uso + encargos**.
1. Selecione o item de linha do qual baixar e clique no símbolo de download.  
    ![Baixar uso e cobranças](./media/cost-analysis-common-uses/download1.png)
1.  Selecione o arquivo de uso para baixar.  
    ![Escolher um arquivo de uso para baixar](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Exibir o detalhamento de custos do EA

Seu registro no EA acumula custos para toda a sua organização. A compreensão de como os custos são acumulados e faturados ao longo do tempo ajuda a envolver os stakeholders apropriados para garantir que os custos sejam gerenciados com responsabilidade.

Os custos são mostrados apenas para seu registro ativo. Se você tiver transferido um registro (inativo) para um novo (ativo), os custos do registro anterior não serão mostrados no Gerenciamento de Custos.


1. No portal do Azure, navegue até **Gerenciamento de Custos + Cobrança** > **Visão geral**.
1. Clique em **Detalhamento** para ver o mês atual e o seu compromisso monetário ao longo do tempo.  
    ![Visão geral dos custos de EA – resumo do detalhamento](./media/cost-analysis-common-uses/breakdown1.png)
1.  Clique na guia **Uso e Encargos** e veja o detalhamento do mês anterior no período de tempo escolhido.  
    ![Guia Uso e encargos](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Exibir o custo mensal do registro por período

Use uma exibição gráfica dos custos mensais do seu registro para entender as tendências de custo e os valores faturados para um período determinado.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione seu registro e defina o prazo do registro.
1. Defina a granularidade como mensal e, em seguida, defina a exibição como **Coluna (empilhada)** .

Você pode agrupar e filtrar seus dados para uma análise mais detalhada.

![Custo de registro mensal por período](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Exibir custos acumulados de registro em EA

Exiba os encargos líquidos acumulados ao longo do tempo para entender as despesas gerais da sua organização em um período determinado.

1. No portal do Azure, navegue até a análise de custo para o seu escopo. Por exemplo:  **Gerenciamento de Custos + Cobrança** > **Gerenciamento de Custos** > **Análise de custo**.
1. Selecione seu registro e exiba os custos acumulados atuais.

![Custos acumulados de registro](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Próximas etapas
- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
- Leia a [Documentação do Gerenciamento de Custos](../index.yml).
