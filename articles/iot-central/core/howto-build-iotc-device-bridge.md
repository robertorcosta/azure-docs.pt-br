---
title: Compilar a ponte de dispositivo do Azure IoT Central | Microsoft Docs
description: Compile a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT (Sigfox, Particle, The Things Network etc.) ao aplicativo IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fb6029a91714ed0229876bbaf6bb0790565be29c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427841"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Compilar a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT ao IoT Central

*Este tópico aplica-se aos administradores.*

A ponte de dispositivo do IoT Central é uma solução de software livre que conecta o Sigfox, Particle, The Things Network e outras nuvens ao aplicativo IoT Central. Se você estiver usando dispositivos de acompanhamento de ativos conectados à rede de área de baixo consumo de energia do Sigfox ou usando dispositivos de monitoramento de qualidade de ar na nuvem do dispositivo de partículas ou usando dispositivos de monitoramento de umidade de solo no TTN, você pode aproveitar diretamente o poder de IoT Central usando a ponte de dispositivo IoT Central. A ponte de dispositivo conecta outras nuvens de IoT com o IoT Central, enviando os dados que os dispositivos enviam para outras nuvens até o aplicativo IoT Central. No aplicativo IoT Central, é possível compilar regras e executar análises nesses dados, criar fluxos de trabalho no Microsoft Flow e Aplicativos Lógicos do Azure, exportar esses dados, e muito mais. Obter a [ponte de dispositivo IOT central](https://aka.ms/iotcentralgithubdevicebridge) do github

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como funciona?
A ponte de dispositivo do IoT Central é uma solução de software livre no GitHub. Ele está pronto para começar com um botão "implantar no Azure" que implanta um modelo de Azure Resource Manager personalizado com vários recursos do Azure em sua assinatura do Azure. Os recursos incluem:
-    Aplicativo Azure Functions
-    Conta de Armazenamento do Azure
-    Plano de consumo
-    Azure Key Vault

O aplicativo de funções é a parte crucial da ponte do dispositivo. Ele recebe solicitações HTTP POST de outras plataformas de IoT ou de qualquer plataforma personalizada por meio de uma integração de webhook simples. Nós fornecemos exemplos que mostram como conectar as nuvens Sigfox, Particle e TTN. É possível estender facilmente essa solução para conectar-se à sua nuvem de IoT personalizada, caso sua plataforma possa enviar solicitações de HTTP POST ao aplicativo de funções.
O aplicativo de funções transforma os dados em um formato aceito pelo IoT Central e os encaminha via APIs de DPS.

![Captura de tela do Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Se o aplicativo IoT Central reconhecer o dispositivo por ID de dispositivo na mensagem encaminhada, uma nova medida será exibida para esse dispositivo. Se a ID do dispositivo nunca foi vista pelo seu aplicativo IoT Central, seu aplicativo de funções tentará registrar um novo dispositivo com essa ID de dispositivo e ele será exibido como um "dispositivo não associado" em seu aplicativo IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como fazer para configurar?
As instruções estão listadas em detalhes no arquivo LEIAME no repositório do GitHub. 

## <a name="pricing"></a>Preços
Os recursos do Azure serão hospedados em sua assinatura do Azure. Obtenha mais informações sobre preços no [aquivo LEIAME](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como compilar a ponte de dispositivo do IoT Central, a seguir está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Gerenciar seus dispositivos](howto-manage-devices.md)
