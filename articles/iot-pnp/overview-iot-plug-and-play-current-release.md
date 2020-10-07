---
title: Versão atual do IoT Plug and Play | Microsoft Docs
description: Saiba o que está incluído na versão atual do IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580759"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>O que a versão atual do IoT Plug and Play inclui

Este artigo resume as ferramentas, os SDKs e as APIs que dão suporte à versão atual do IoT Plug and Play. Os números de versão mostrados refletem o número de versão no momento em que o IoT Plug and Play ficou em disponibilidade geral. Os números de versão podem ser incrementados após o lançamento.

## <a name="modeling-language"></a>Linguagem de modelagem

[DTDL (Linguagem de Definição de Gêmeos Digitais) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Para saber mais sobre como os dispositivos IoT Plug and Play funcionam com a DTDL, confira [Convenções do IoT Plug and Play](concepts-convention.md).

## <a name="tools-and-utilities"></a>Ferramentas e utilitários

- Azure IoT Explorer 0.12.0.

    Para saber mais, confira [Instalar e usar o Azure IoT Explorer](howto-use-iot-explorer.md).

- Extensão do VS Code 1.0.0.

    Para saber mais, confira [Instalar e usar as ferramentas de criação DTDL](howto-use-dtdl-authoring-tools.md).

- Extensão do Visual Studio 2019 1.0.0.

    Para saber mais, confira [Instalar e usar as ferramentas de criação DTDL](howto-use-dtdl-authoring-tools.md).

- Extensão de IoT da CLI do Azure 0.10.0.

    Para saber mais, confira [Instalar e usar a extensão de IoT do Azure para a CLI do Azure](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > A extensão de IoT do Azure inclui comandos que ajudam na certificação de dispositivos. Consulte `az iot product -h`.



## <a name="libraries-and-sdks"></a>Bibliotecas e SDKs

Para saber mais sobre as bibliotecas e os SDKs, confira [SDKs da Microsoft para o IoT Plug and Play](libraries-sdks.md).

- SDK do dispositivo C [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- SDK do dispositivo .NET [NuGet Microsoft.Azure.Devices.Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- SDK do dispositivo Java [Maven iot-device-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- SDK do dispositivo Python [Pip azure-iot-device v2.2.0](https://pypi.org/project/azure-iot-device/)
- SDK do dispositivo Node.js [npm azure-iot-device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET – Serviço Hub IoT [NuGet Microsoft.Azure.Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java – Serviço Hub IoT [Maven iot-service-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js – Serviço Hub IoT [npm azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python – Serviço Hub IoT/Gêmeos Digitais [Pip azure-iot-hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser) do analisador de modelo DTDL.

### <a name="preview"></a>Versão Prévia

- SDK do Azure para o Embedded [1.0.0-preview.5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Middleware de IoT do Azure RTOS [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>APIs REST

API REST [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Para saber mais, confira o [Guia do desenvolvedor do IoT Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>Hub IoT

O IoT Plug and Play é compatível com o Hub IoT em todas as regiões. O IoT Plug and Play é compatível somente com os hubs IoT Standard ou da Camada gratuita.

## <a name="announcements"></a>Comunicados

Para obter os anúncios atuais e anteriores do IoT Plug and Play, confira as seguintes postagens no blog:

- [Atualização da versão prévia pública (postada em 29 de agosto de 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Preparar e certificar seus dispositivos para o IoT Plug and Play (postado em 26 de agosto de 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Agora, o IoT Plug and Play está disponível em versão prévia (postado em 22 de agosto de 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Criar com o Azure IoT Central e o IoT Plug and Play (postado em 7 de maio de 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

