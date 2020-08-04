---
title: Introdução ao IoT Plug and Play Preview   Microsoft Docs
description: Saiba mais sobre o IoT Plug and Play Preview. O IoT Plug and Play baseia-se em uma linguagem de programação de modelagem aberta que permite aos dispositivos IoT inteligentes declarar as respectivas funcionalidades. Os dispositivos IoT apresentam essa declaração, chamada de modelo de dispositivo, quando se conectam a soluções de nuvem. A solução de nuvem pode, então, entender automaticamente o dispositivo e começar a interagir com ele, tudo sem que nenhum código seja escrito.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 7f889d6730012a11ebf82a78583ef420b7621075
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337646"
---
# <a name="what-is-iot-plug-and-play-preview"></a>O que é a versão prévia do IoT Plug and Play?

A versão prévia do IoT Plug and Play permite que os criadores de solução integrem as respectivas soluções a dispositivos inteligentes sem nenhuma configuração manual. No núcleo do IoT Plug and Play há um _modelo_ de dispositivo que um dispositivo usa para anunciar suas funcionalidades a um aplicativo habilitado para IoT Plug and Play. Esse modelo é estruturado como um conjunto de elementos que definem:

- _Propriedades_ que representam o estado somente leitura ou gravável de um dispositivo ou outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade somente leitura e uma temperatura de destino em um termostato pode ser uma propriedade gravável.
- _Telemetria_, que são os dados emitidos por um dispositivo, sejam esses dados um fluxo regular de leituras de sensor, um erro ocasional ou uma mensagem informativa.
- _Comandos_ que descrevem uma função ou operação que pode ser feita em um dispositivo. Por exemplo, um comando pode reinicializar um gateway ou tirar uma foto usando uma câmera remota.

É possível agrupar esses elementos em interfaces para reutilização entre modelos para facilitar a colaboração e acelerar o desenvolvimento.

Para fazer o IoT Plug and Play funcionar com os [Gêmeos Digitais do Azure](../digital-twins/about-digital-twins.md), defina modelos e interfaces usando a [DTDL (Linguagem de Definição de Gêmeo Digital)](https://github.com/Azure/opendigitaltwins-dtdl). O IoT Plug and Play e as DTDL estão abertos para a Comunidade, e a Microsoft agradece a colaboração com clientes, parceiros e o setor. Ambos se baseiam em padrões W3C abertos, tais como JSON-LD e RDF, o que permite uma adoção mais fácil entre serviços e ferramentas.

Não há nenhum custo adicional para usar o IoT Plug and Play e o DTDL. As taxas Standard para o [Hub IoT do Azure](../iot-hub/about-iot-hub.md) e outros serviços do Azure permanecem as mesmas.

Este artigo descreve:

- As funções típicas associadas a um projeto que usa o IoT Plug and Play.
- Como usar dispositivos de IoT Plug and Play no aplicativo.
- Como desenvolver um aplicativo de dispositivo IoT compatível com IoT Plug and Play.

## <a name="user-roles"></a>Funções de usuário

O IoT Plug and Play é útil para dois tipos de desenvolvedores:

- Um _desenvolvedor de soluções_ é responsável por desenvolver uma solução de IoT usando o Hub IoT do Azure e outros recursos do Azure e por identificar dispositivos IoT a serem integrados.
- Um _desenvolvedor de dispositivos_ cria o código que é executado em um dispositivo conectado à solução.

## <a name="use-iot-plug-and-play-devices"></a>Usar dispositivos IoT Plug and Play

Como desenvolvedor de soluções, você pode desenvolver uma solução de IoT hospedada na nuvem que usa dispositivos IoT Plug and Play. Use o [Hub IoT](../iot-hub/about-iot-hub.md), um serviço de nuvem gerenciado que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos.

Ao conectar um dispositivo IoT Plug and Play a um Hub IoT, você pode usar a ferramenta [Azure IoT Explorer](./howto-use-iot-explorer.md) para ver a telemetria, as propriedades e os comandos definidos nas interfaces que compõem o modelo.

## <a name="develop-an-iot-device-application"></a>Desenvolver um aplicativo de dispositivo IoT

Como desenvolvedor de dispositivos, você pode desenvolver um produto de hardware de IoT compatível com IoT Plug and Play. O processo inclui três etapas principais:

1. Defina o modelo do dispositivo. Você cria um conjunto de arquivos JSON que definem as funcionalidades do dispositivo usando a [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Um modelo descreve uma entidade completa (assim como um produto físico) e define o conjunto de interfaces implementadas por essa entidade. Interfaces são contratos compartilhados que identificam exclusivamente a telemetria, as propriedades e os comandos compatíveis com um dispositivo. As interfaces podem ser reutilizadas em modelos diferentes.

1. Crie software de dispositivo ou firmware de uma forma que as respectivas propriedades, comandos e telemetria sigam as convenções de IoT Plug and Play.

1. O dispositivo anuncia a ID do modelo como parte da conexão MQTT. O SDK do Azure IoT inclui novos constructos para fornecer a ID do modelo no momento da conexão.

> [!Important]
> Os dispositivos IoT Plug and Play precisam usar MQTT ou MQTT sobre WebSockets. Outros protocolos, como AMQP ou HTTP, não são válidos para implementar dispositivos IoT Plug and Play.

## <a name="regional-availability"></a>Disponibilidade regional

Esta atualização de versão prévia do IoT Plug and Play está disponível nos Hubs IoT criados nas regiões EUA Central, Norte da Europa e Leste do Japão.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do IoT Plug and Play, a próxima etapa sugerida é experimentar um dos guias de início rápido:

- [Conectar um dispositivo ao Hub IoT (C)](./quickstart-connect-device-c.md)
- [Interagir com um dispositivo da sua solução (Node.js)](./quickstart-service-node.md)

