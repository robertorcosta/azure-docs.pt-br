---
title: Controle de acesso de dados de Monitoramento Remoto – Azure | Microsoft Docs
description: Este artigo fornece informações sobre como é possível configurar controles de acesso para o explorador de telemetria do Time Series Insights no acelerador de solução de monitoramento remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005955"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Configurar controles de acesso para o explorador de telemetria do Time Series Insights

Este artigo fornece informações sobre como configurar controles de acesso para o explorador de Insights do Time Series no acelerador de solução de monitoramento remoto. Para permitir que os usuários do acelerador de solução acessem o explorador do Time Series Insights, é necessário conceder acesso a cada usuário.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e salvar consultas compartilhadas e perspectivas associadas ao ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights. Digite **Time Series** na caixa **pesquisar**. Selecione **Ambiente do Time Series** nos resultados da pesquisa. 

3. Selecione o seu ambiente de Análise de Séries Temporais na lista.

4. Selecione **Políticas de Acesso a Dados** e depois **+Adicionar**.
    ![Gerenciar a fonte do Time Series Insights – ambiente](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Selecione **Selecionar usuário**.  Pesquise o nome de usuário ou endereço de email para localizar o usuário que deseja adicionar. Clique em **Selecionar** para confirmar a seleção. 

    ![Gerenciar a fonte das Análise de Séries Temporais - adicionar](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Selecione **Selecionar função**. Escolha a função de acesso apropriada para o usuário:
   - Selecione **colaborador** se desejar permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente. 
   - Caso contrário, selecione **leitor** para permitir que os dados de consulta do usuário no ambiente e salve as consultas pessoais (não compartilhadas) no ambiente.

     Selecione **OK** para confirmar a escolha da função.

     ![Gerenciar a fonte das Análise de Séries Temporais - selecionar usuário](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Selecione **Ok** na página **Selecionar Função do Usuário**.

    ![Gerenciar a fonte das Análise de Séries Temporais - selecionar função](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. A página **Políticas de Acesso de Dados** lista os usuários e suas funções.

    ![Gerenciar a fonte das Análise de Séries Temporais - resultados](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como os controles de acesso são concedidos para o explorador de Insights do Time Series no acelerador de solução de monitoramento remoto.

Para obter mais informações conceituais sobre o acelerador da solução de Monitoramento Remoto, veja [Arquitetura de Monitoramento Remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para saber mais sobre como personalizar a solução de Monitoramento Remoto, veja [Personalizar e reimplantar um microsserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->