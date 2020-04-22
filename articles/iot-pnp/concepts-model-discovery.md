---
title: Implementar a descoberta do modelo IoT Plug and Play Preview | Microsoft Docs
description: Como desenvolvedor de soluções, saiba como você pode implementar a descoberta do modelo IoT Plug and Play em sua solução.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 74eb38269a3c7fbdc6d95554a8a8cef14eb0b787
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770471"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementar a descoberta do modelo IoT Plug and Play Preview em uma solução de IoT

Este artigo descreve como, como um desenvolvedor de soluções, você pode implementar a descoberta do modelo IoT Plug and Play Preview em uma solução IoT.  A descoberta do modelo IoT Plug and Play é como os dispositivos IoT Plug and Play identificam seus modelos e interfaces de capacidade suportados e como uma solução de IoT recupera esses modelos e interfaces de capacidade.

Existem duas grandes categorias de solução de IoT: soluções construídas com propósito que trabalham com um conjunto conhecido de dispositivos IoT Plug and Play, e soluções orientadas por modelos que funcionam com qualquer dispositivo IoT Plug and Play.

Este artigo conceitual descreve como implementar a descoberta de modelos em ambos os tipos de solução.

## <a name="model-discovery"></a>Descoberta de modelos

Quando um dispositivo IoT Plug and Play se conecta pela primeira vez ao seu hub IoT, ele envia uma mensagem de telemetria de informações do modelo. Esta mensagem inclui os IDs das interfaces que o dispositivo implementa. Para que sua solução funcione com o dispositivo, ele deve resolver esses IDs e recuperar as definições para cada interface.

Aqui estão as etapas que um dispositivo IoT Plug and Play toma quando usa o DPS (Device Provisioning Service, serviço de provisionamento de dispositivos) para se conectar a um hub:

1. Quando o dispositivo é ligado, ele se conecta ao ponto final global para DPS e autentica usando um dos métodos permitidos.
1. O DPS então autentica o dispositivo e procura a regra que diz a qual hub de IoT atribuir o dispositivo. O DPS então registra o dispositivo com esse hub.
1. O DPS retorna uma seqüência de conexão IoT Hub para o dispositivo.
1. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o seu Hub IoT. A mensagem de telemetria de descoberta contém os IDs das interfaces que o dispositivo implementa.
1. O dispositivo IoT Plug and Play está agora pronto para trabalhar com uma solução que usa seu hub IoT.

Se o dispositivo se conectar diretamente ao seu hub IoT, ele se conecta usando uma seqüência de conexão que está incorporada no código do dispositivo. Em seguida, o dispositivo envia uma mensagem de telemetria de descoberta para o seu Hub IoT.

Consulte a interface [ModelInformation](concepts-common-interfaces.md) para saber mais sobre a mensagem de telemetria de informações do modelo.

### <a name="purpose-built-iot-solutions"></a>Soluções de IoT construídas com propósito

Uma solução IoT construída com propósito funciona com um conjunto conhecido de modelos e interfaces de capacidade de dispositivos IoT Plug and Play.

Você terá o modelo de capacidade e interfaces para os dispositivos que se conectarão à sua solução com antecedência. Use as seguintes etapas para preparar sua solução:

1. Armazene os arquivos JSON da interface em um [repositório de modelos](./howto-manage-models.md) onde sua solução possa lê-los.
1. Escreva lógica em sua solução de IoT com base nos modelos e interface de capacidade de IoT Plug and Play esperados.
1. Inscreva-se nas notificações do hub de IoT que sua solução usa.

Quando você receber uma notificação para uma nova conexão do dispositivo, siga estas etapas:

1. Leia a mensagem de telemetria de descoberta para recuperar os IDs do modelo de capacidade e interfaces implementados pelo dispositivo.
1. Compare o ID do modelo de capacidade com os IDs dos modelos de capacidade armazenados antes do tempo.
1. Agora você sabe que tipo de dispositivo se conectou. Use a lógica que você escreveu anteriormente para permitir que os usuários interajam com o dispositivo apropriadamente.

### <a name="model-driven-solutions"></a>Soluções orientadas por modelos

Uma solução IoT orientada por modelo pode funcionar com qualquer dispositivo IoT Plug and Play. Construir uma solução de IoT orientada por modelo é mais complexo, mas o benefício é que sua solução funciona com quaisquer dispositivos adicionados no futuro.

Para construir uma solução de IoT orientada por modelo, você precisa criar uma lógica contra os primitivos da interface IoT Plug and Play: telemetria, propriedades e comandos. A lógica da sua solução de IoT representa um dispositivo combinando vários recursos de telemetria, propriedade e comando.

Sua solução também deve assinar as notificações do hub de IoT que usa.

Quando sua solução receber uma notificação para uma nova conexão do dispositivo, siga estas etapas:

1. Leia a mensagem de telemetria de descoberta para recuperar os IDs do modelo de capacidade e interfaces implementados pelo dispositivo.
1. Para cada ID, leia o arquivo JSON completo para encontrar as capacidades do dispositivo.
1. Verifique se cada interface está presente em quaisquer caches que você construiu para armazenar os arquivos JSON recuperados anteriormente pela sua solução.
1. Em seguida, verifique se uma interface com esse ID está presente no repositório de modelos públicos. Para obter mais informações, consulte [Repositório de modelos públicos](howto-manage-models.md).
1. Se a interface não estiver presente no repositório de modelos públicos, tente procurá-la em qualquer repositório de modelo da empresa conhecido pela sua solução. Você precisa de uma seqüência de conexões para acessar um repositório de modelos da empresa. Para obter mais informações, consulte [o repositório de modelos da empresa](howto-manage-models.md).
1. Se você não conseguir encontrar todas as interfaces no repositório de modelos públicos ou em um repositório de modelos da empresa, você pode verificar se o dispositivo pode fornecer a definição da interface. Um dispositivo pode implementar a interface [ModelDefinition](concepts-common-interfaces.md) padrão para publicar informações sobre como recuperar arquivos de interface com um comando.
1. Se você encontrou arquivos JSON para cada interface implementada pelo dispositivo, você pode enumerar os recursos do dispositivo. Use a lógica que você escreveu anteriormente para permitir que os usuários interajam com o dispositivo.
1. A qualquer momento, você pode chamar a API de gêmeos digitais para recuperar o ID do modelo de capacidade e iDs de interface para o dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a descoberta de modelos de uma solução de IoT, saiba mais sobre a [Plataforma IoT do Azure](overview-iot-plug-and-play.md) para aproveitar outros recursos para sua solução.
