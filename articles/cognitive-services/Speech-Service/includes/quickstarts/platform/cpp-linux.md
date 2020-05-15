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
ms.openlocfilehash: 973a50833d92fd9b68aae084fc6b4cbb3afe7160
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980093"
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

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list.md)]
