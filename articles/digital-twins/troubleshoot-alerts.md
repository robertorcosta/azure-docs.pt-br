---
title: Configurar alertas
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar alertas em métricas de gêmeos digitais do Azure.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1b296cd942e36817da2832467ab603ebd833f825
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908122"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Solução de problemas do Azure digital gêmeos: alertas

O Azure digital gêmeos coleta [métricas](troubleshoot-metrics.md) para sua instância de serviço que fornecem informações sobre o estado de seus recursos. Você pode usar essas métricas para avaliar a integridade geral do serviço de gêmeos digital do Azure e os recursos conectados a ele.

Os **alertas** o notificam proativamente quando condições importantes são encontradas nos dados de métricas. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Você pode ler mais sobre alertas em [*visão geral de alertas no Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Ativar alertas

Aqui está como habilitar alertas para sua instância do gêmeos digital do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **alertas** no menu e **+ nova regra de alerta**.

    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Captura de tela mostrando a página alertas e o botão a ser adicionado. Não há alertas configurados ainda" lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. Na página *criar regra de alerta* a seguir, você pode seguir os prompts para definir condições, ações a serem disparadas e detalhes do alerta.     
    * Os detalhes do **escopo** devem ser preenchidos automaticamente com os detalhes da sua instância.
    * Você definirá os detalhes do **grupo de ações** e da **condição** para personalizar gatilhos e respostas de alerta.

    :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Captura de tela mostrando a página Criar regra de alerta com seções para escopo, condição e grupo de ações" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Para obter uma explicação orientada sobre como preencher esses campos, consulte [*visão geral de alertas no Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Veja abaixo alguns exemplos de como serão as etapas do Azure digital gêmeos.

Aqui está um trecho do processo de *seleção de condição* que ilustra quais tipos de sinais de alerta estão disponíveis para o gêmeos digital do Azure. Nessa página, você pode filtrar o tipo de sinal e selecionar o sinal desejado em uma lista.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Captura de tela mostrando a primeira página de lógica de sinal de configuração. Há um realce na caixa tipo de sinal para selecionar métricas ou logs de atividade e uma lista de métricas abaixo que pode ser selecionada":::

Depois de selecionar um sinal, você será solicitado a configurar a lógica do alerta. Você pode filtrar em uma dimensão, definir um valor de limite para o alerta e definir a frequência de verificações para a condição. Aqui está um exemplo de configuração de um alerta para quando a métrica de taxa média de falhas de roteamento é superior a 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Captura de tela mostrando a segunda página Configurar lógica de sinal. A página mostra o histórico de métricas, tem uma área para filtrar em uma dimensão, como operações de grade de eventos, e uma seção para definir a lógica de alerta como ' média é maior que 5 '":::
 
Após a configuração dos alertas, eles aparecerão de volta na página de *alertas* para sua instância.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Captura de tela mostrando a página alertas e o botão a ser adicionado. Há um alerta configurado" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre alertas com Azure Monitor, consulte [*visão geral de alertas no Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [*solução de problemas: Exibir métricas com Azure monitor*](troubleshoot-metrics.md).
* Para ver como habilitar o log de diagnóstico para suas métricas, consulte [*solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).
