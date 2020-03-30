---
title: Use o VS Cloud Explorer para gerenciar mensagens de dispositivos Azure IoT Hub
description: Saiba como usar o Cloud Explorer para Visual Studio para monitorar o dispositivo para mensagens de nuvem e enviar mensagens da nuvem para o dispositivo no Hub IoT do Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079488"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usar o Cloud Explorer para Visual Studio para enviar e receber mensagens entre seu dispositivo e o Hub IoT

![Diagrama de ponta a ponta](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) para é uma extensão útil para Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio. Este artigo se concentra em como usar o Cloud Explorer para enviar e receber mensagens entre seu dispositivo e seu hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Neste artigo, você aprende a usar o Cloud Explorer para o Visual Studio para monitorar mensagens de dispositivo para nuvem e enviar mensagens nuvem-para-dispositivo. Mensagens do dispositivo para a nuvem podem ser dados de sensor que o dispositivo coleta e envia para o Hub IoT. Mensagens da nuvem para dispositivo podem ser comandos que o Hub IoT envia para seu dispositivo. Por exemplo, pisca um LED conectado ao seu dispositivo.

## <a name="what-you-do"></a>O que fazer

Neste artigo, você faz as seguintes tarefas:

- Use o Cloud Explorer para Visual Studio para monitorar mensagens do dispositivo para a nuvem.

- Use o Cloud Explorer para Visual Studio para enviar mensagens da nuvem para dispositivo.

## <a name="what-you-need"></a>O que você precisa

Você precisa dos pré-requisitos a seguir:

- Uma assinatura ativa do Azure.

- Um Hub IoT do Azure em sua assinatura.

- Microsoft Visual Studio 2017 Atualização 9 ou posterior. Este artigo usa [o Visual Studio 2019](https://www.visualstudio.com/vs/).

- O componente Cloud Explorer do Visual Studio Installer, que é selecionado por padrão com a Carga de Trabalho do Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualize o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Visual Studio Installer para o Visual Studio 2017 só suporta o monitoramento de mensagens de dispositivo para nuvem e nuvem para dispositivo. Para usar o Visual Studio 2017, baixe e instale o mais recente [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Faça login para acessar seu hub

Para acessar seu hub, siga estas etapas:

1. No Visual Studio, selecione **View** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone Gerenciamento de contas para mostrar suas assinaturas.

    ![Ícone de Gerenciamento de Contas](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se você estiver conectado ao Azure, suas contas aparecerão. Para entrar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as assinaturas do Azure que deseja usar e escolha **Aplicar**.

1. Expanda sua assinatura e expanda os **Hubs de IoT**.  Em cada hub, você pode ver seus dispositivos para esse hub.

    ![Lista de dispositivos](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorar mensagens do dispositivo para a nuvem

Para monitorar as mensagens enviadas do seu dispositivo ao seu Hub IoT, siga estas etapas:

1. Clique com o botão direito do mouse no Hub IoT ou no dispositivo e selecione **Iniciar Monitoramento de Mensagem de D2C**.

    ![Iniciar o monitoramento de mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. As mensagens monitoradas aparecem em **Saída**.

    ![Monitorando o resultado da mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Para deixar de monitorar, clique com o botão direito do mouse em qualquer dispositivo ou o Hub IoT e selecione **Parar de monitorar mensagem de D2C**.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D**.

1. Insira a mensagem na caixa de entrada.

    ![Enviar mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Os resultados aparecem em **Saída**.

    ![Enviar resultado de uma mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]