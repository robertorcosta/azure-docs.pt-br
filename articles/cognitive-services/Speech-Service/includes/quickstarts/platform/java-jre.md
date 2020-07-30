---
title: 'Início Rápido: Configuração de plataforma do SDK de Fala para Java (Windows, Linux e macOS) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar Java (Windows, Linux e macOS) com o SDK do Serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 2a6516ad5f0fc8d9faefd7b7f89ddb1eaa3fd7d6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375606"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para o JRE do Java 8 de 64 bits. Se você quiser apenas o nome do pacote para começar por conta própria, o SDK do Java não estará disponível no repositório central do Maven. Se você estiver usando o Gradle ou um arquivo de dependência `pom.xml`, será necessário adicionar um repositório personalizado apontando para `https://csspeechstorage.blob.core.windows.net/maven/` (veja abaixo o nome do pacote).

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

- O pacote do SDK de Fala do Java está disponível para estes sistemas operacionais:
  - Windows: somente 64 bits
  - Mac: macOS X versão 10.13 ou mais recente
  - Linux: 64 bits apenas no Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8

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

  - No RHEL/CentOS, execute os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Observe que, ao instalá-los pela primeira vez, pode ser necessário reiniciar o Windows antes de continuar com este guia.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Criar um projeto do Eclipse e instalar o SDK de Fala

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
