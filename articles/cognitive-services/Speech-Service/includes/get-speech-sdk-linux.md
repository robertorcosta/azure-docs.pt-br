---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a298e78c32464680dab9feccb3cfc84f686ff81b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656538"
---
:::row:::
    :::column span="3":::
        O Speech SDK só suporta **ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 8**e **CentOS 7/8** nas seguintes arquiteturas de destino quando usado com Linux:
        - x86
        - x64
        - ARM32
        - ARM64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Ao direcionar o Linux ARM64 e usar o C# - o .NET Core 3.x (pacote dotnet-sdk-3.x) é necessário. Se você estiver mirando ARM32 ou ARM64, o Python não será suportado.

> [!NOTE]
> As arquiteturas x86 do Ubuntu 16.04, Ubuntu 18.04 e Debian 9 só suportam o desenvolvimento do C++ com o Speech SDK.

### <a name="system-requirements"></a>Requisitos do sistema

Para uma aplicação nativa, o Speech `libMicrosoft.CognitiveServices.Speech.core.so`SDK depende de . Certifique-se de que a arquitetura de destino (x86, x64) corresponda ao aplicativo. Dependendo da versão do Linux, podem ser necessárias dependências adicionais.

- As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
- A biblioteca OpenSSL `libssl.so.1.0.2`(ou`libssl.so.1.0.0` )
- A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-8-and-centos-78"></a>[RHEL 8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Siga as instruções [sobre como configurar O RHEL/CentOS 7 para O SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> No RHEL/CentOS 8, siga as instruções [sobre como configurar o OpenSSL para Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
