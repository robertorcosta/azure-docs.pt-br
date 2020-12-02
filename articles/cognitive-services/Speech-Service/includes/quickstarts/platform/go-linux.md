---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 06e4eea32aefcb400c144be98c274e3e4bb4b121
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188139"
---
Este guia mostra como instalar o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) no Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisitos do sistema

Linux; confira a lista de [distribuições do Linux e arquiteturas de destino compatíveis](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do seguinte:

* gcc
* [Binário Go (1.13 ou posterior)](https://golang.org/dl/)

* As plataformas Linux com suporte deverão ter determinadas bibliotecas instaladas (`libssl` para suporte do protocolo SSL e `libasound2` para um suporte consistente). Consulte a distribuição abaixo para verificar os comandos necessários para instalar as versões corretas dessas bibliotecas.

   * No Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Se o libssl 1.0.0 não estiver disponível, instale o libssl 1.0.x (em que x é maior que 0) ou o libssl 1.1.

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


## <a name="configure-go-environment"></a>Configurar o ambiente Go

1. Como as associações dependem de `cgo`, você precisa definir as variáveis de ambiente para que o Go possa encontrar o SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Além disso, para executar aplicativos que incluam o SDK, precisamos dizer ao SO em que local encontrar as bibliotecas:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [windows](../quickstart-list-go.md)]