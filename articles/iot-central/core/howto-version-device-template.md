---
title: Noções básicas sobre o controle de versão de modelo de dispositivo para aplicativos Azure IoT Central | Microsoft Docs
description: Iterar modelos de dispositivo criando novas versões e sem afetar os dispositivos conectados em tempo real
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8d4e3b304000113e7ecbf748767780a3fcf17bb3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952596"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo de dispositivo

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

A Microsoft IoT Central permite o rápido desenvolvimento de Aplicativos IoT. É possível iterar rapidamente nos designs de modelos de dispositivos adicionando, editando ou excluindo medidas, configurações ou propriedades. Algumas dessas alterações podem ser intrusivas para os dispositivos conectados no momento. O Azure IoT Central identifica essas alterações importantes e fornece uma maneira de implantar com segurança essas atualizações nos dispositivos.

Ao criar um modelo de dispositivo, ele conterá um número de versão. Por padrão, o número de versão é 1.0.0. Se você editar um modelo de dispositivo e essa alteração puder afetar os dispositivos conectados em tempo real, o Azure IoT Central solicitará a criação de uma nova versão de modelo de dispositivo.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, consulte [Configurar um modelo de dispositivo](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Alterações que solicitam uma alteração de versão

Em geral, as alterações nas configurações ou nas propriedades do modelo de dispositivo solicitam uma alteração de versão.

> [!NOTE]
> As alterações feitas no modelo de dispositivo não solicitam a criação de uma nova versão quando nenhum dispositivo ou no máximo um dispositivo estiver conectado.

A lista a seguir descreve as ações do usuário que podem exigir uma nova versão:

* Propriedades (obrigatório)
    * Adicionar ou excluir uma propriedade obrigatória
    * Alterar o nome de campo de uma propriedade, nome de campo usado pelos dispositivos para enviar mensagens.
*  Propriedades (opcional)
    * Excluir uma propriedade opcional
    * Alterar o nome de campo de uma propriedade, nome de campo usado pelos dispositivos para enviar mensagens.
    * Alterar uma propriedade opcional para uma propriedade obrigatória
*  Configurações
    * Adicionar ou excluir uma configuração
    * Alterar o nome de campo de uma configuração, o nome de campo usado pelos dispositivos para enviar e receber mensagens.

## <a name="what-happens-on-version-change"></a>O que acontece na alteração da versão?

O que acontece com as regras e os painéis de dispositivos quando há uma alteração da versão?

**As regras** na versão anterior do modelo de dispositivo continuam a funcionar inalteradas. As regras não são migradas automaticamente para a nova versão de modelo de dispositivo. Você pode criar regras na nova versão do modelo como de costume. Para obter mais informações, consulte o artigo como [criar uma regra de telemetria e configurar notificações no seu aplicativo IOT central do Azure](howto-create-telemetry-rules.md) .

Os **painéis do dispositivo** podem conter vários tipos de blocos. Algumas blocos podem conter configurações e propriedades. Quando uma propriedade ou configuração usada em um bloco é removida, o bloco é desfeito total ou parcialmente. Você pode acessar o bloco e corrigir o problema, removendo o bloco ou atualizando o conteúdo do bloco.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrar um dispositivo nas versões de modelos de dispositivos

É possível criar várias versões do modelo de dispositivo. Com o tempo, você terá vários dispositivos conectados usando esses modelos de dispositivo. Você pode migrar dispositivos de uma versão do modelo de dispositivo para outra. As etapas a seguir descrevem como migrar um dispositivo:

1. Vá para a página de **Device Explorer** .
1. Selecione o dispositivo que você precisa migrar para outra versão.
1. Escolha **Migrar Dispositivo**.
1. Selecione o número de versão para o qual você deseja migrar o dispositivo e escolha **Migrar**.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a usar as versões de modelos de dispositivo no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules.md)