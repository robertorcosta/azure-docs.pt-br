---
title: 'Início Rápido: Instalação da plataforma C++ (Linux) do SDK de Fala – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para instalar a plataforma para C++ no Linux com o SDK do serviço de Fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383891"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) no Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos do sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do seguinte:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* As plataformas Linux com suporte deverão ter determinadas bibliotecas instaladas (`libssl` para suporte do protocolo SSL e `libasound2` para um suporte consistente). Consulte a distribuição abaixo para verificar os comandos necessários para instalar as versões corretas dessas bibliotecas.

   * No Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * No Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * No RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> no RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="install-speech-sdk"></a>Instalar SDK de Fala

O SDK de Fala para Linux pode ser usado para criar aplicativos de 64 bits e 32 bits. As bibliotecas necessárias e os arquivos de cabeçalho podem ser baixados como um arquivo tar de https://aka.ms/csspeech/linuxbinary.

Baixe e instale o SDK conforme a seguir:

1. Escolha um diretório para o qual os arquivos do SDK de Fala serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Essa variável facilita fazer referência a esse diretório em futuros comandos. Por exemplo, se você quiser usar o diretório `speechsdk` no seu diretório base, use um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia os arquivos `.tar.gz` que contêm os binários do SDK de Fala:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem do diretório conterá os arquivos de licença e notificações de terceiros, bem como um diretório `include` que contém os arquivos (`.h`) de cabeçalho e um diretório `lib` que contém bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Agora é possível avançar para as [Próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
