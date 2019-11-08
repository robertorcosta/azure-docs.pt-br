---
title: 'Início Rápido: Configuração de plataforma do SDK de Fala para Java (Windows, Linux e macOS) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar Java (Windows, Linux e macOS) com o SDK dos Serviços de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504336"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para o JRE do Java 8 de 64 bits.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

- O pacote do SDK de Fala do Java está disponível para estes sistemas operacionais:
  - Windows: somente 64 bits
  - Mac: macOS X versão 10.13 ou mais recente
  - Linux: somente 64 bits no Ubuntu 16.04, no Ubuntu 18.04 ou no Debian 9

## <a name="prerequisites"></a>Pré-requisitos

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Java IDE do Eclipse](https://www.eclipse.org/downloads/) (requer que o Java já esteja instalado)
- As plataformas Linux com suporte deverão ter determinadas bibliotecas instaladas (`libssl` para suporte do protocolo SSL e `libasound2` para um suporte consistente). Consulte a distribuição abaixo para verificar os comandos necessários para instalar as versões corretas dessas bibliotecas.

  - No Ubuntu, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Observe que, ao instalá-los pela primeira vez, pode ser necessário reiniciar o Windows antes de continuar com este guia.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Criar um projeto do Eclipse e instalar o SDK de Fala

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
