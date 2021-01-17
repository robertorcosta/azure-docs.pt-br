---
title: Investigação de erro usando o painel
description: Este documento contém informações sobre a investigação de erros usando o painel
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540483"
---
# <a name="error-investigation-using-the-dashboard"></a>Investigação de erro usando o painel

Esta página contém informações sobre o painel do conector ITSM. Este painel ajudará você a investigar o status do seu conector ITSM.

## <a name="how-to-view-the-dashboard"></a>Como exibir o painel

Para exibir os erros no painel, você deve seguir as próximas etapas:

1. Em **todos os recursos**, procure por **assistência técnica (*seu nome de espaço de trabalho*)**:

   ![Captura de tela que mostra os recursos recentes no portal do Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Em **fontes de dados do espaço de trabalho** no painel esquerdo, selecione **conexões de ITSM**:

   ![Captura de tela que mostra o item de menu de conexões de ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

3. Em **Resumo** na caixa à esquerda **conector de gerenciamento de serviços de ti**, selecione **Exibir Resumo**:

    ![Captura de tela que mostra o resumo da exibição.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Em **Resumo** na caixa à esquerda **conector de gerenciamento de serviços de ti**, clique no grafo:

    ![Captura de tela que mostra o clique do grafo.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Usando este painel, você poderá examinar o status e os erros em seu conector.
    ![Captura de tela que mostra o status do conector.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Elementos do painel

O painel contém informações sobre os alertas que foram enviados para a ferramenta de ITSM usando esse conector.
O painel é dividido em quatro partes:

1. Item de trabalho criado: o grafo e a tabela abaixo contêm a contagem do item de trabalho por tipo. Se você clicar no grafo ou na tabela, poderá ver mais detalhes sobre os itens de trabalho.
    ![Captura de tela que mostra o item de trabalho criado.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Computadores afetados: as tabelas contêm detalhes sobre os itens de configuração que criaram itens de configuração.
    Ao clicar em linhas nas tabelas, você poderá obter mais detalhes sobre os itens de configuração.
    A tabela contém um número limitado de linhas se você quiser ver toda a lista em que você pode clicar em "ver tudo".
    ![Captura de tela que mostra os computadores afetados.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Status do conector: o grafo e a tabela abaixo contêm mensagens sobre o status do conector. Ao clicar no gráfico em linhas na tabela, você pode obter mais detalhes sobre as mensagens do status do conector.
    A tabela contém um número limitado de linhas se você quiser ver toda a lista em que você pode clicar em "ver tudo".
    ![Captura de tela que mostra o status do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Regras de alerta: as tabelas contêm as informações sobre o número de regras de alerta que foram detectadas.
    Ao clicar em linhas nas tabelas, você poderá obter mais detalhes sobre as regras que foram detectadas.
    A tabela contém um número limitado de linhas se você quiser ver toda a lista em que você pode clicar em "ver tudo".
    ![Captura de tela que mostra as regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)