---
title: Introdução ao IoT Plug and Play Preview   Microsoft Docs
description: Saiba mais sobre o IoT Plug and Play Preview. O IoT Plug and Play baseia-se em uma linguagem de programação de modelagem aberta que permite aos dispositivos IoT declarar as respectivas funcionalidades. Os dispositivos IoT apresentam essa declaração, chamada de modelo de funcionalidade do dispositivo, quando se conectam a soluções de nuvem, tais como o Azure IoT Central ou a aplicativos de parceiros. A solução de nuvem pode, então, entender automaticamente o dispositivo e começar a interagir com ele — tudo sem que nenhum código seja escrito.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 0399e1659fb7cc6a650c6b3c1d0189c8802d4904
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064323"
---
# <a name="what-is-iot-plug-and-play-preview"></a>O que é a versão prévia do IoT Plug and Play?

O IoT Plug and Play Preview permite que os desenvolvedores de soluções integrem dispositivos às soluções deles sem escrever nenhum código inserido. No núcleo do IoT Plug and Play está um esquema de _modelo de funcionalidade do dispositivo_ que descreve os recursos do dispositivo. Esse esquema é um documento JSON estruturado como um conjunto de interfaces que incluem definições de:

- _Propriedades_ que representam o estado somente leitura e de leitura/gravação de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade de leitura/gravação.
- _Telemetria_, que são os dados emitidos por um dispositivo, sejam esses dados um fluxo regular de leituras de sensor, um erro ocasional ou uma mensagem informativa.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

Você pode reutilizar interfaces em modelos de funcionalidades de dispositivo para facilitar a colaboração e acelerar o desenvolvimento.

Para fazer o IOT plug and Play funcionar diretamente com os [Gêmeos Digitais do Azure](../digital-twins/about-digital-twins.md), o esquema de IoT Plug and Play é definido usando o [DTDL (Linguagem de Definição de Gêmeo Digital)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). O IoT Plug and Play e as DTDL estão abertos para a Comunidade, e a Microsoft agradece a colaboração com clientes, parceiros e o setor. Ambos se baseiam em padrões W3C abertos, tais como JSON-LD e RDF, que possibilitam uma adoção mais fácil entre serviços e ferramentas. Além disso, não há nenhum custo adicional para usar o IoT Plug and Play e o DTDL. As taxas padrão para o [Hub IoT do Azure](../iot-hub/about-iot-hub.md), o [Azure IoT Central](../iot-central/core/overview-iot-central.md) e os outros serviços do Azure permanecem as mesmas.

As soluções criadas no Hub IoT ou no IoT Central podem se beneficiar do IoT Plug and Play.

Este artigo descreve:

- As funções típicas associadas a um projeto que usa o IoT Plug and Play.
- Como usar dispositivos de IoT Plug and Play no aplicativo.
- Como desenvolver um aplicativo de dispositivo IoT compatível com IoT Plug and Play.
- Como certificar um dispositivo IoT Plug and Play e publicar no [Catálogo de dispositivos Certified para IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Funções de usuário

O IoT Plug and Play é útil para dois tipos de desenvolvedores:

- Um _desenvolvedor de soluções_ é responsável por desenvolver uma solução de IoT usando o IoT do Azure e outros recursos do Azure e por identificar dispositivos IoT a serem integrados.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo conectado à solução.

## <a name="use-iot-plug-and-play-devices"></a>Usar dispositivos IoT Plug and Play

Como desenvolvedor de soluções, você pode desenvolver uma solução de IoT hospedada na nuvem que usa dispositivos de IoT Plug and Play. É possível usar qualquer um dos serviços do Azure a seguir:

- [IoT Central](../iot-central/core/overview-iot-central.md) – uma solução de software como um serviço de IoT totalmente gerenciada que facilita a criação de produtos que conectam os mundos físico e digital.
- [Hub IoT](../iot-hub/about-iot-hub.md) – um serviço gerenciado de nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos.

Você pode encontrar dispositivos IoT Plug and Play por meio do catálogo de dispositivos do Azure Certified for IoT. Cada dispositivo IoT Plug and Play no catálogo foi validado e tem um modelo de funcionalidade do dispositivo. Exiba o modelo de funcionalidade do dispositivo para entender a funcionalidade do dispositivo ou usá-la para simular o dispositivo no Azure IoT Central.

Ao conectar um dispositivo IoT Plug and Play, você pode exibir o respectivo modelo de funcionalidade do dispositivo, as interfaces incluídas no modelo e a telemetria, as propriedades e os comandos definidos nessas interfaces.

## <a name="develop-an-iot-device-application"></a>Desenvolver um aplicativo de dispositivo IoT

Como desenvolvedor de dispositivos, você pode desenvolver um produto de hardware de IoT compatível com IoT Plug and Play. O processo envolve duas etapas principais:

1. Defina o modelo de funcionalidade do dispositivo e as interfaces. Você cria um conjunto de arquivos JSON que declaram as funcionalidades do dispositivo usando o [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Um modelo de funcionalidade do dispositivo descreve uma entidade completa, tal como um produto físico, e define o conjunto de interfaces implementadas por essa entidade. Interfaces são contratos compartilhados que identificam exclusivamente a telemetria, as propriedades e os comandos compatíveis com um dispositivo. As interfaces podem ser reutilizadas em diferentes modelos de funcionalidade do dispositivo.

1. Crie o software do dispositivo ou o firmware que implementa as funcionalidades declaradas no modelo de funcionalidade do dispositivo e as interfaces. O SDK do IoT do Azure inclui APIs para implementar modelos de funcionalidade do dispositivo.

O pacote de extensão [Azure IoT Tools para VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) fornece muitos recursos para ajudá-lo. Por exemplo, como um desenvolvedor de dispositivos, você pode usar a extensão para gerar um projeto de esqueleto em C com base em um modelo de funcionalidade. No entanto, você pode usar qualquer IDE para criar e implementar modelos de funcionalidade do dispositivo.

## <a name="certify-an-iot-plug-and-play-device"></a>Certificar um dispositivo IoT Plug and Play

Como desenvolvedor de dispositivos, você pode enviar produtos de hardware IoT para certificação. Você pode publicar um dispositivo certificado no catálogo de dispositivos do Certified for IoT. As etapas do processo de certificação incluem:

- Ingresse na [Microsoft Partner Network](https://partner.microsoft.com).
- Integre-se ao portal do Azure Certified para IoT.
- Envie um modelo de funcionalidade do dispositivo IoT Plug and Play e informações de marketing para criar um novo registro de dispositivo.
- Passe o conjunto automatizado de testes de validação para o dispositivo.
- Publique no catálogo de dispositivos Certified para IoT.

## <a name="regional-availability"></a>Disponibilidade regional

Durante a versão prévia pública, a IoT Plug and Play está disponível em todas as regiões.

## <a name="message-quotas-in-iot-hub"></a>Cotas de mensagens no Hub IoT
Durante a versão prévia pública, os dispositivos IoT Plug and Play enviam mensagens separadas por interface, o que pode aumentar o número de mensagens contadas em direção à sua [cota de mensagens](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do IoT Plug and Play, a próxima etapa sugerida é experimentar um dos guias de início rápido:

- [Usar um modelo de funcionalidade do dispositivo para criar um dispositivo IoT Plug and Play](./quickstart-create-pnp-device-windows.md)
- [Conectar um dispositivo ao Hub IoT](./quickstart-connect-pnp-device-c-windows.md)
- [Conectar-se a um dispositivo da solução](./quickstart-connect-pnp-device-solution-node.md)
