---
title: Gerenciamento de dispositivos Azure IoT c/ Visual Studio Cloud Explorer
description: Use o Cloud Explorer para Visual Studio para p gerenciamento de dispositivo do Hub IoT do Azure, apresentando os métodos Direto e as opções de gerenciamento de propriedades desejadas do Gêmeo.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953191"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usar o Cloud Explorer para Visual Studio para gerenciamento de dispositivo do Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) para é uma extensão útil para Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio. Ela é fornecida com opções de gerenciamento que podem ser usadas para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Leitura de dispositivo gêmeo           | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Atualizar dispositivo gêmeo         | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da nuvem para o dispositivo   | Envie notificações para um dispositivo. Por exemplo, “É muito provável que chova hoje. Não se esqueça de levar um guarda-chuva”.              |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que você aprenderá

Neste artigo, você aprende a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Neste artigo, execute o Cloud Explorer para o Visual Studio com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

Você precisa dos pré-requisitos a seguir:

- Uma assinatura ativa do Azure.

- Um Hub IoT do Azure em sua assinatura.

- Microsoft Visual Studio 2017 Atualização 9 ou posterior. Este artigo usa [o Visual Studio 2017 ou o Visual Studio 2019](https://www.visualstudio.com/vs/).

- Componente cloud explorer do Visual Studio Installer, que selecionou por padrão com a Carga de Trabalho do Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualize o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Visual Studio Installer para o Visual Studio 2017 só suporta o monitoramento de mensagens de dispositivo para nuvem e nuvem para dispositivo. Para usar o Visual Studio 2017, baixe e instale o mais recente [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Faça login para acessar seu hub

1. No Visual Studio, selecione **View** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone Gerenciamento de contas para mostrar suas assinaturas.

    ![Ícone de Gerenciamento de Contas](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se você estiver conectado ao Azure, suas contas aparecerão. Para entrar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as assinaturas do Azure que deseja usar e escolha **Aplicar**.

1. Expanda sua assinatura e expanda os **Hubs de IoT**.  Em cada hub, você pode ver seus dispositivos para esse hub. Clique com o botão direito do mouse em um dispositivo para acessar as opções de gerenciamento.

    ![Opções de gerenciamento](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Métodos diretos

Para usar métodos diretos, faça as seguintes etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Invocar Método Direto de Dispositivo**.

1. Digite o nome do método e a carga no **Método Invocar Direto**e, em seguida, selecione **OK**.

    Os resultados aparecem na **saída**.

## <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

Para editar um dispositivo gêmeo, faça as seguintes etapas:

1. Clique com o botão direito e selecione **Editar Gêmeo de Dispositivo**.

   Um arquivo **azure-iot-device-twin.json** é aberto com o conteúdo do dispositivo twin.

1. Faça algumas edições de **tags** ou **propriedades.campos desejados** para o arquivo **azure-iot-device-twin.json.**

1. Pressione **Ctrl+S** para atualizar o dispositivo gêmeo.

   Os resultados aparecem na **saída**.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D**.

1. Digite a mensagem em **Enviar mensagem C2D** e selecione **OK**.

   Os resultados aparecem na **saída**.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
