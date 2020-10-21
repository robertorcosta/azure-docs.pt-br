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
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 1befc26d6c6d0a12af57c8a0d3d0e6d32f363f19
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096888"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) no Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos do sistema

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)

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

   * No RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
