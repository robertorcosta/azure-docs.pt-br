---
title: Monitore visualmente a fábrica de dados do Azure
description: Saiba como monitorar visualmente as fábricas de dados do Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 40b1b8d040c4b3ea76372920f88551fba35c5f26
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419435"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitore visualmente a fábrica de dados do Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Depois de criar e publicar um pipeline na Azure Data Factory, você pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Você pode monitorar todas as suas operações de pipeline nativamente na experiência do usuário da Fábrica de Dados Do Azure. Para abrir a experiência de monitoramento, selecione o **Monitor & Gerenciar** ladrilhos na lâmina de fábrica de dados do portal [Azure](https://portal.azure.com/). Se você já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda.

Todas as corridas de fábrica de dados são exibidas no fuso horário local do navegador. Se você alterar o fuso horário, todos os campos de data/hora serão instantâneos para o selecionado.

## <a name="monitor-pipeline-runs"></a>Monitorar execuções de pipeline

A exibição padrão de monitoramento é a lista de executões do pipeline no período de tempo selecionado. As seguintes colunas são exibidas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline |
| Ações | Ícones que permitem visualizar detalhes da atividade, cancelar ou executar o pipeline |
| Início da Execução | Data de início e hora para a execução do gasoduto (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Disparado por | O nome do gatilho que iniciou o oleoduto |
| Status | **Falha,** **sucesso,** **em progresso,** **cancelado**ou **enfileirado** |
| Anotações | Tags filtradas associadas a um pipeline  |
| Parâmetros | Parâmetros para a execução do pipeline (pares de nome/valor) |
| Erro | Se o pipeline falhou, o erro de execução |
| ID da execução | ID da execução de pipeline |

![Exibição de lista para monitoramento de executões de pipeline](media/monitor-visually/pipeline-runs.png)

Você precisa selecionar manualmente o botão **Atualizar** para atualizar a lista de executões de pipeline e atividades. Atualmente, a atualização automática não é suportada.

![Botão Atualizar](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorar execuções de atividade

Para exibir a execução das atividades em cada execução do pipeline, selecione o ícone **Exibir executa** a atividade na coluna **Ações.** A exibição da lista mostra as atividades executadas que correspondem a cada execução do pipeline.

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade dentro do gasoduto |
| Tipo de atividade | Tipo de atividade, como **Copiar,** **ExecutarDataFlow**ou **AzureMLExecutePipeline** |
| Ações | Ícones que permitem ver informações de entrada do JSON, informações de saída do JSON ou experiências detalhadas de monitoramento específicas da atividade | 
| Início da Execução | Data de início e hora para a execução da atividade (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Status | **Falhou,** **teve sucesso,** **em progresso**ou **cancelou** |
| Tempo de execução de integração | Qual tempo de execução de integração a atividade foi executada em |
| Propriedades do Usuário | Propriedades definidas pelo usuário da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| ID da execução | ID da execução de atividade |

![Exibição de lista para executá-lo de atividades de monitoramento](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Promover as propriedades de usuário para monitorá-las

Promova qualquer propriedade de atividade de pipeline como propriedade do usuário para que ela se torne uma entidade que você monitore. Por exemplo, você pode promover as propriedades **de Origem** e **Destino** da atividade de cópia em seu pipeline como propriedades do usuário. Selecione **Gerar automático** para gerar as propriedades do usuário **Origem** e **Destino** para uma atividade de cópia.

![Criar propriedades do usuário](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Você só pode promover até cinco propriedades de atividade de pipeline como propriedades do usuário.

Depois de criar as propriedades do usuário, você pode monitorá-las nas exibições da lista de monitoramento. Se a origem da atividade de cópia for um nome de tabela, você poderá monitorar o nome da tabela de origem como uma coluna na exibição de lista para executá-lo.

![Lista de execuções de atividade sem propriedades de usuário](media/monitor-visually/monitor-user-properties-image2.png)

![Adicionar colunas para propriedades do usuário à lista de executações de atividades](media/monitor-visually/monitor-user-properties-image3.png)

![Lista de execuções de atividade com colunas de propriedades de usuário](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurar a exibição de lista

### <a name="order-and-filter"></a>Ordem e filtro

Alternar se as corridas de gasodutos serão em descida ou ascensão de acordo com o tempo de início da corrida. O pipeline de filtro é executado usando as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Filtrar pelo nome do oleoduto. |
| Início da Execução |  Determine o intervalo de tempo exibido pelo pipeline. As opções incluem filtros rápidos para **as últimas 24 horas**, **na semana passada**e **últimos 30 dias** ou para selecionar uma data e hora personalizadas. |
| Status da execução | O filtro é executado por status: **Bem sucedido,** **falho,** **enfileirado,** **cancelado**ou **em andamento**. |
| Anotações | Filtrar por tags aplicadas a cada pipeline |
| Execuções | Filtrar se você quer ver os pipelines rerandos |

![Opções para filtragem](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito do mouse no cabeçalho de exibição da lista e escolha as colunas que deseja aparecer na exibição da lista.

![Opções para colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar as larguras de colunas
Aumente e diminua as larguras da coluna na exibição da lista, pairando sobre o cabeçalho da coluna.

## <a name="rerun-activities-inside-a-pipeline"></a>Executar atividades novamente em um pipeline

Você pode reexecutar atividades dentro de um oleoduto. Selecione **Exibir execução de atividades**e, em seguida, selecione a atividade em seu pipeline a partir do ponto em que deseja executar novamente seu pipeline.

![Exibir execuções de atividade](media/monitor-visually/rerun-activities-image1.png)

![Selecionar uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Reprise de atividade falha

Se uma atividade falhar, ficar sem tempo ou for cancelada, você poderá executar o pipeline dessa atividade com falha selecionando **Reexecuçã o da atividade com falha**.

![Atividade falha de reexecução](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Exibir o histórico de novas execuções

Exiba o histórico de novas execuções para todas as execuções de pipeline na exibição de lista.

![Exibir histórico](media/monitor-visually/rerun-history-image1.png)

Exiba também o histórico de novas execuções para uma execução de pipeline específica.

![Exibir o histórico para uma execução de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Pontos de vista de Gantt

Use as visualizações do Gantt para visualizar rapidamente seus pipelines e corridas de atividades.

![Exemplo de um gráfico gantt](media/monitor-visually/gantt1.png)

Você pode olhar para a exibição de Gantt por pipeline ou grupo por anotações/tags que você criou em seus pipelines.

![Anotações do gráfico de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do gasoduto. Você também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Passeios guiados
Selecione o ícone **Informação** no lado inferior esquerdo. Em seguida, **selecione Visitas Guiadas** para obter instruções passo a passo sobre como monitorar o pipeline e as corridas de atividades.

![Passeios guiados](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas em métricas com suporte no Data Factory. Selecione **Alertas** > **de & Métricas** na página de monitoramento da Fábrica de Dados para começar.

![Página do Monitor de fábrica de dados](media/monitor-visually/alerts01.png)

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **Nova regra de alerta** para criar um novo alerta.

    ![Novo botão de regra de alerta](media/monitor-visually/alerts02.png)

1.  Especifique o nome da regra e selecione a gravidade do alerta.

    ![Caixas para nome de regra e gravidade](media/monitor-visually/alerts03.png)

1.  Selecione os critérios de alerta.

    ![Caixa para critérios-alvo](media/monitor-visually/alerts04.png)

    ![Lista de critérios](media/monitor-visually/alerts05.png)

1.  Configure a lógica de alerta. Crie um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Você também pode selecionar um tipo de atividade específico, nome da atividade, nome do pipeline ou tipo de falha.

    ![Opções para configurar a lógica de alerta](media/monitor-visually/alerts06.png)

1.  Configure notificações de e-mail, SMS, push e voz para o alerta. Crie um grupo de ação ou escolha um já existente para as notificações de alerta.

    ![Opções para configurar notificações](media/monitor-visually/alerts07.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/alerts08.png)

1.  Criar a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre monitoramento e gerenciamento de pipelines, consulte o [Monitor e gerencie os pipelines de forma programática.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
