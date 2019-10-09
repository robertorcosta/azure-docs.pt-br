---
title: Monitorar visualmente as data factories do Azure | Microsoft Docs
description: Saiba como monitorar visualmente as fábricas de dados do Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: f7c27bde3806684045bc43f8ff99eefb14c8d04a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029149"
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorar visualmente data factories do Azure
O Azure Data Factory é um serviço de integração de dados baseado em nuvem. Você pode usá-lo para criar fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados. Usando Azure Data Factory, você pode:

- Criar e agendar fluxos de trabalhos orientados a dados (chamados pipelines) que podem ingerir dados provenientes de repositórios de dados diferentes.
- Processe/transforme os dados usando serviços de computação, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning.
- Publicar dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para que estes sejam consumidos por aplicativos de business intelligence (BI).

Neste início rápido, você aprenderá a monitorar visualmente os pipelines do Data Factory sem escrever uma única linha de código.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="monitor-data-factory-pipelines"></a>Monitorar pipelines de Data Factory

Monitore execuções de pipeline e de atividade com uma interface de exibição de lista simples. Todas as execuções são exibidas no fuso horário local do navegador. Se você alterar o fuso horário, todos os campos de data/hora se ajustarão para aquele que você selecionou.  

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário Data Factory tem suporte apenas nesses dois navegadores da Web.
2. Entre no [portal do Azure](https://portal.azure.com/).
3. Vá para a folha do data factory criado no portal do Azure. Selecione o bloco **monitorar & gerenciar** para iniciar a experiência de monitoramento do data Factory Visual.

## <a name="monitor-pipeline-runs"></a>Monitorar execuções de pipeline
A exibição de lista mostra cada execução de pipeline para seus pipelines de Data Factory. Ele inclui estas colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline |
| Ações | Ação única disponível para exibir execuções de atividade |
| Início da Execução | Data e hora de início para a execução do pipeline (MM/DD/AAAA, HH: MM: SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Disparado por | Gatilho manual ou gatilho agendado |
| Status | **Com falha, com** **êxito**ou **em andamento** |
| parâmetros | Parâmetros para a execução do pipeline (pares de nome/valor) |
| Erro | Erro de execução de pipeline (se houver) |
| ID da execução | ID da execução de pipeline |

![Exibição de lista para monitoramento de execuções de pipeline](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorar execuções de atividade
A exibição de lista mostra as execuções de atividade que correspondem a cada execução de pipeline. Para exibir as execuções de atividade para cada execução de pipeline, selecione o ícone de **execuções de atividade** na coluna **ações** . A exibição de lista inclui estas colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade dentro do pipeline |
| Tipo de atividade | Tipo de atividade, como **Copy**, **HDInsightSpark**ou **HDInsightHive** |
| Início da Execução | Data e hora de início para a execução da atividade (MM/DD/AAAA, HH: MM: SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Status | **Com falha, com** **êxito**ou **em andamento** |
| Entrada | Matriz JSON que descreve as entradas de atividade |
| Saída | Matriz JSON que descreve as saídas de atividade |
| Erro | Erro de execução da atividade (se houver) |

![Exibição de lista para monitoramento de execuções de atividade](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Você precisa selecionar o botão **Atualizar** na parte superior para atualizar a lista de execuções de pipeline e de atividade. No momento, não há suporte para atualização automática.

![Botão atualizar](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Selecione um data factory a ser monitorado
Passe o mouse sobre o ícone de **Data Factory** no canto superior esquerdo. Selecione o ícone de seta para ver uma lista de assinaturas e data factories do Azure que você pode monitorar.

![Selecione o data factory](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurar a exibição de lista

### <a name="apply-rich-ordering-and-filtering"></a>Aplicar ordenação e filtragem avançadas

Order pipeline é executado em DESC/ASC de acordo com a hora de início da execução. Filtrar execuções de pipeline usando as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline. As opções incluem filtros rápidos para as **últimas 24 horas**, **última semana**e **últimos 30 dias**. Ou selecione uma data e hora personalizadas. |
| Início da Execução | Data e hora de início para a execução do pipeline. |
| Status da execução | O filtro é executado por status: Com **êxito**, **com falha**ou **em andamento**. |

![Opções de filtragem](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito do mouse no cabeçalho da exibição de lista e escolha as colunas que você deseja exibir na exibição de lista.

![Opções para colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar as larguras de colunas
Aumente e diminua as larguras de coluna no modo de exibição de lista passando o mouse sobre o cabeçalho da coluna.

## <a name="promote-user-properties-to-monitor"></a>Promover as propriedades de usuário para monitorá-las

É possível promover qualquer propriedade de atividade do pipeline como uma propriedade do usuário para que ela se torne uma entidade que você pode monitorar. Por exemplo, você pode promover as propriedades de **origem** e **destino** da atividade de cópia em seu pipeline como propriedades do usuário. Você também pode selecionar **gerar automaticamente** para gerar as propriedades de usuário de **origem** e de **destino** para uma atividade de cópia.

![Criar propriedades do usuário](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Você só pode promover até cinco Propriedades de atividade de pipeline como propriedades do usuário.

Depois de criar as propriedades do usuário, você pode monitorá-las nos modos de exibição da lista de monitoramento. Se a origem da atividade de cópia for um nome de tabela, você poderá monitorar o nome da tabela de origem como uma coluna no modo de exibição de lista para execuções de atividade.

![Lista de execuções de atividade sem propriedades de usuário](media/monitor-visually/monitor-user-properties-image2.png)

![Adicionar colunas para propriedades de usuário à lista de execuções de atividade](media/monitor-visually/monitor-user-properties-image3.png)

![Lista de execuções de atividade com colunas de propriedades de usuário](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Executar atividades novamente em um pipeline

Agora você pode executar atividades novamente em um pipeline. Selecione **Exibir execuções de atividade**e, em seguida, selecione a atividade no pipeline da qual ponto você deseja executar novamente o pipeline.

![Exibir execuções de atividade](media/monitor-visually/rerun-activities-image1.png)

![Selecionar uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Exibir o histórico de novas execuções

Exiba o histórico de novas execuções para todas as execuções de pipeline na exibição de lista.

![Exibir histórico](media/monitor-visually/rerun-history-image1.png)

Exiba também o histórico de novas execuções para uma execução de pipeline específica.

![Exibir o histórico para uma execução de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Exibições de Gantt

Use exibições de Gantt para visualizar rapidamente os pipelines e as execuções de atividade. Você pode examinar o modo de exibição de Gantt por pipeline ou por anotações/marcas que você criou em seus pipelines.

![Exemplo de um gráfico de Gantt](media/monitor-visually/gantt1.png)

![Anotações do gráfico de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do pipeline. Você também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Passeios guiados
Selecione o ícone de **informações** no canto inferior esquerdo. Em seguida, selecione **viagens guiadas** para obter instruções passo a passo sobre como monitorar suas execuções de pipeline e de atividade.

![Passeios guiados](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Comentários
Selecione o ícone de **comentários** para nos enviar comentários sobre vários recursos ou problemas que você possa estar enfrentando.

![Comentários](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas em métricas com suporte no Data Factory. Selecione **monitorar** > **alerta & métricas** na página monitoramento de data Factory para começar.

![Página monitor do data Factory](media/monitor-visually/alerts01.png)

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **nova regra de alerta** para criar um novo alerta.

    ![Botão nova regra de alerta](media/monitor-visually/alerts02.png)

1.  Especifique o nome da regra e selecione a severidade do alerta.

    ![Caixas para nome da regra e severidade](media/monitor-visually/alerts03.png)

1.  Selecione os critérios de alerta.

    ![Caixa para critérios de destino](media/monitor-visually/alerts04.png)

    ![Lista de critérios](media/monitor-visually/alerts05.png)

1.  Configure a lógica de alerta. Crie um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Você também pode selecionar um tipo específico de atividade, nome da atividade, nome do pipeline ou tipo de falha.

    ![Opções para configurar a lógica de alerta](media/monitor-visually/alerts06.png)

1.  Configure notificações por email, SMS, push e voz para o alerta. Crie um grupo de ações ou escolha um existente para as notificações de alerta.

    ![Opções para configurar notificações](media/monitor-visually/alerts07.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/alerts08.png)

1.  Criar a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como monitorar e gerenciar pipelines, confira o artigo [monitorar e gerenciar pipelines programaticamente](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
