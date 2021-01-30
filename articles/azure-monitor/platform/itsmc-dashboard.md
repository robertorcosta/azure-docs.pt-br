---
title: Investigar erros usando o painel
description: Este documento contém informações sobre erros no painel do ITSMC.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089268"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Investigar erros usando o painel do ITSMC

Este artigo contém informações sobre o painel de Conector de Gerenciamento de Serviços de TI (ITSMC). O painel ajuda você a investigar o status de ITSMC.

## <a name="view-the-dashboard"></a>Exibir o painel

Siga estas etapas para abrir o painel.

1. Selecione **todos os recursos** e, em seguida, localize **a assistência (*seu nome de espaço de trabalho*)**.

   ![Captura de tela que mostra os recursos nos serviços do Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. No painel esquerdo, selecione **fontes de dados de espaço de trabalho** e, em seguida, selecione conexões de **ITSM**.

   ![Captura de tela que mostra a seleção de conexões ITSM em fontes de dados do local de trabalho.](media/itsmc-overview/add-new-itsm-connection.png)

1. Na seção **Resumo** , selecione **Exibir Resumo** para exibir um gráfico de resumo.

    ![Captura de tela que mostra a opção Exibir Resumo na seção Resumo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Selecione o grafo na seção **Resumo** para abrir o painel.

    ![Captura de tela que mostra a seleção do grafo de resumo.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Examine o painel para ver o status e os erros em seu conector.
    ![Captura de tela que mostra o painel.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Entender os elementos do Dashboard

O painel contém informações sobre os alertas que foram enviados para a ferramenta de ITSM usando esse conector.

O painel é dividido em quatro seções:

- **Itens de trabalho criados**: o grafo e a tabela mostram o número de itens de trabalho por tipo. Selecione o grafo ou a tabela para saber mais sobre seus itens de trabalho.
      ![Captura de tela que mostra a seção itens de trabalho criados.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **Computadores afetados**: a tabela contém detalhes sobre os itens de configuração que criaram itens de trabalho.
      Selecione linhas nas tabelas para obter mais detalhes sobre os itens de configuração.
      A tabela contém um número limitado de linhas. Para ver a lista inteira, selecione **ver tudo**.
      ![Captura de tela que mostra a seção computadores afetados.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Status do conector**: o grafo e a tabela mostram informações sobre o status do conector. Selecione o grafo ou as mensagens na tabela para obter mais detalhes. A tabela mostra um número limitado de linhas. Para ver a lista inteira, selecione **ver tudo**.
      ![Captura de tela que mostra a seção status do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Regras de alerta**: Esta seção mostra informações sobre o número de regras de alerta que foram detectadas. Selecione linhas nas tabelas para obter mais detalhes sobre as regras que foram detectadas. A tabela tem um número limitado de linhas. Para ver a lista inteira, selecione **ver tudo**.
      ![Captura de tela que mostra a seção regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Próximas etapas

Confira [erros comuns de status do conector](itsmc-dashboard-errors.md).
