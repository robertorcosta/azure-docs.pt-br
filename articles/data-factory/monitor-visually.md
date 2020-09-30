---
title: Monitorar visualmente o Azure Data Factory
description: Saiba como monitorar visualmente data factories do Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: fcbf48081484941fd93f209bee21eb14c2c38310
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570144"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorar visualmente o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Depois de criar e publicar um pipeline no Azure Data Factory, você pode associá-lo a um gatilho ou iniciar manualmente uma execução ad hoc. Você pode monitorar todas as execuções de pipeline nativamente na experiência de usuário do Azure Data Factory. Para abrir a experiência de monitoramento, selecione o bloco **Monitorar e gerenciar** na folha de data factory do [portal do Azure](https://portal.azure.com/). Se você já estiver na UX do ADF, clique no ícone **Monitorar** na barra lateral esquerda.

Todas as execuções de data factory são exibidas no fuso horário local do navegador. Se você alterar o fuso horário e todos os campos de data e hora serão encaixados no fuso horário selecionado.

## <a name="monitor-pipeline-runs"></a>Monitorar execuções de pipeline

O modo de exibição de monitoramento padrão é a lista de execuções de pipeline no período selecionado. As seguintes colunas são exibidas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline |
| Ações | Ícones que permitem exibir detalhes da atividade, cancelar ou executar novamente o pipeline |
| Início da Execução | Data e hora de início da execução do pipeline (MM/DD/AAAA HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Disparado por | O nome do gatilho que iniciou o pipeline |
| Status | **Falha**, **Bem-sucedida**, **Em andamento**, **Cancelado**ou **Na fila** |
| Anotações | Marcas filtráveis associadas a um pipeline  |
| Parâmetros | Parâmetros de execução de pipeline (pares de nome/valor) |
| Erro | Se o pipeline falhou, o erro de execução |
| ID da execução | ID da execução de pipeline |

![Visualização de lista para monitoramento de execuções de pipeline](media/monitor-visually/pipeline-runs.png)

Você precisará selecionar manualmente o botão **Atualizar** para atualizar a lista de execuções do pipeline e atividades. No momento, não há suporte para atualização automática.

![Botão Atualizar](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorar execuções de atividade

Para visualizar as execuções de atividade para cada execução do pipeline, selecione o ícone de **Visualizar execuções de atividade** na coluna **Ações**. A exibição de lista mostra as execuções de atividade que correspondem a cada execução de pipeline.

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade no pipeline |
| Tipo de atividade | Tipo de atividade, como **Copiar**, **ExecuteDataFlow** ou **AzureMLExecutePipeline** |
| Ações | Ícones que permitem que você veja informações de entrada JSON, informações de saída JSON ou experiências detalhadas de monitoramento específicas da atividade | 
| Início da Execução | Data e hora de início da execução da atividade (MM/DD/AAAA HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Status | **Falha**, **Sucesso**, **Em andamento** ou **Cancelado** |
| Integration Runtime | Em qual Integration Runtime a atividade foi executada |
| Propriedades do Usuário | Propriedades definidas pelo usuário da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| ID da execução | ID da execução de atividade |

![Exibição de lista de execuções de atividade de monitoramento](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Promover as propriedades de usuário para monitorá-las

Promove qualquer propriedade de atividade do pipeline como uma propriedade do usuário para que ela se torne uma entidade que você monitorar. Por exemplo, você pode promover as propriedades de **Fonte** e **Destino** da atividade de Cópia do pipeline como propriedades do usuário. Também é possível selecionar **Gerar Automaticamente** para gerar as propriedades de **Fonte** e **Destino** para uma atividade de cópia.

![Criar propriedades do usuário](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> É possível promover no máximo cinco propriedades de atividade do pipeline como propriedades do usuário.

Após criar as propriedades do usuário, você pode monitorá-las nas exibições de lista de monitoramento. Se a fonte da atividade de Cópia for um nome de tabela, você poderá monitorar o nome de tabela de origem como uma coluna na exibição de lista de execuções de atividade.

![Lista de execuções de atividade sem propriedades de usuário](media/monitor-visually/monitor-user-properties-image2.png)

![Adicionar colunas de propriedades de usuário à lista de execuções de atividade](media/monitor-visually/monitor-user-properties-image3.png)

![Lista de execuções de atividade com colunas de propriedades de usuário](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurar a exibição de lista

### <a name="order-and-filter"></a>Classificar e filtrar

Alterne se as execuções de pipeline serão em ordem decrescente ou crescente, de acordo com a hora de início da execução. Filtrar execuções de pipeline usando as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Filtre o nome do pipeline. |
| Início da Execução |  Determine o intervalo de tempo das execuções de pipeline exibidas. As opções incluem filtros rápidos para **últimas 24 horas**, **Última semana**, **Últimos 30 dias** ou selecione uma data e hora personalizadas. |
| Status da execução | O filtro é executado por status: **Êxito**, **Com falha**, **Enfileiradas**, **Canceladas** ou **Em andamento**. |
| Anotações | Filtrar por marcas aplicadas a cada pipeline |
| Execuções | Filtrar se você deseja ver os pipelines reexecutados |

![Opções de filtragem](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito do mouse no cabeçalho da exibição de lista e escolha as colunas que você deseja que apareçam na exibição de lista.

![Opções para colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar as larguras de colunas
Aumente e diminua as larguras das colunas na exibição de lista simplesmente passando o cursor do mouse sobre o cabeçalho da coluna.

## <a name="rerun-activities-inside-a-pipeline"></a>Executar atividades novamente em um pipeline

Você pode executar atividades novamente em um pipeline. Selecione **Exibir execuções de atividade**, em seguida, a atividade no pipeline de cujo ponto você deseja executar o pipeline novamente.

![Exibir execuções de atividade](media/monitor-visually/rerun-activities-image1.png)

![Selecionar uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Executar novamente a partir da atividade com falha

Se uma atividade falhar, expirar ou for cancelada, você poderá executar novamente o pipeline da atividade com falha selecionando **Executar novamente a partir da atividade com falha**.

![Executar novamente a atividade com falha](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Exibir o histórico de novas execuções

Exiba o histórico de novas execuções para todas as execuções de pipeline na exibição de lista.

![Exibir histórico](media/monitor-visually/rerun-history-image1.png)

Exiba também o histórico de novas execuções para uma execução de pipeline específica.

![Exibir o histórico para uma execução de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorar o consumo

Você pode ver os recursos consumidos por uma execução de pipeline clicando no ícone de consumo ao lado da execução. 

![Captura de tela que mostra onde você pode ver os recursos consumidos por um pipeline.](media/monitor-visually/monitor-consumption-1.png)

Clicar no ícone abre um relatório de consumo dos recursos usados por essa execução de pipeline. 

![Monitorar o consumo](media/monitor-visually/monitor-consumption-2.png)

Você pode conectar esses valores à [calculadora de preços do Azure](https://azure.microsoft.com/pricing/details/data-factory/) para estimar o custo da execução do pipeline. Para obter mais informações sobre preços de Azure Data Factory, consulte [Noções básicas sobre preços](pricing-concepts.md).

> [!NOTE]
> Esses valores retornados pela calculadora de preços são uma estimativa. Eles não refletem o valor exato que você será cobrado por Azure Data Factory 

## <a name="gantt-views"></a>Exibições de Gantt

Use exibições de Gantt para visualizar rapidamente os pipelines e as execuções de atividade.

![Exemplo de um gráfico de Gantt](media/monitor-visually/gantt1.png)

Você pode examinar o modo de exibição de Gantt por pipeline ou por anotações/marcas que você criou em seus pipelines.

![Anotações do gráfico de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do pipeline. Você também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Passeios guiados
Selecione o ícone de **Informações** no canto inferior esquerdo. Clique em **Passeios guiados** no canto inferior esquerdo para obter instruções passo a passo sobre como monitorar as execuções de atividade e o pipeline.

![Passeios guiados](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas em métricas com suporte no Data Factory. Selecione o **Monitor** > **Alertas e métricas** na página do Monitor do Data Factory para começar a usar.

![Página Monitorar Data Factory](media/monitor-visually/start-page.png)

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **Nova regra de alerta** para criar um novo alerta.

    ![Botão Nova regra de alerta](media/monitor-visually/new-alerts.png)

1.  Especifique o nome da regra e selecione a gravidade do alerta.

    ![Caixas para nome da regra e gravidade](media/monitor-visually/name-and-severity.png)

1.  Selecione os critérios de alerta.

    ![Caixa para os critérios de destino](media/monitor-visually/add-criteria-1.png)

    ![Captura de tela que mostra onde você seleciona uma métrica para configurar a condição de alerta.](media/monitor-visually/add-criteria-2.png)

    ![Lista de critérios](media/monitor-visually/add-criteria-3.png)

    Você pode criar alertas em várias métricas, incluindo aquelas para contagem/tamanho de entidade do ADF, execuções de atividade/pipeline/gatilho, execução de CPU de Integration Runtime (IR)/memória/contagem de nós/fila, bem como para execuções de pacote SSIS e operações de início/parada do SSIS.

1.  Configure a lógica de alerta. Crie um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Também é possível selecionar um tipo de atividade em particular, o nome da atividade, o nome do pipeline ou um tipo de falha.

    ![Opções para configurar a lógica de alerta](media/monitor-visually/alert-logic.png)

1.  Configure as notificações de email, SMS, push e voz para o alerta. Crie um grupo de ações ou escolha um existente para as notificações de alerta.

    ![Opções para configurar notificações para alertas](media/monitor-visually/configure-notification-1.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/configure-notification-2.png)

1.  Criar a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, veja o artigo [Monitorar e gerenciar os pipelines programaticamente](https://docs.microsoft.com/azure/data-factory/monitor-programmatically).
