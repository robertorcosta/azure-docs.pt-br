---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: b0f70fccf3f7f4a6856ae64d0946c2c473fed93c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050457"
---
## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é uma assinatura do serviço Fala do Azure. Consulte o [guia](../get-started.md#new-resource) sobre a criação de uma nova assinatura se você ainda não tiver uma.

## <a name="download-and-install"></a>Fazer o download e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estas etapas para instalar a CLI de Fala no Windows:

1. Instale o [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) ou o [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Baixe o [arquivo zip](https://aka.ms/speech/spx-zips.zip) da CLI de Fala e, em seguida, extraia o arquivo.
3. Acesse o diretório raiz `spx-zips` que você extraiu do download e extraia o subdiretório necessário (`spx-net471` para .NET Framework 4.7 ou `spx-netcore-win-x64` para .NET Core 3.0 em uma CPU x64).

No prompt de comando, altere o diretório para esse local e, em seguida, digite `spx` para ver a ajuda da CLI de Fala.

> [!NOTE]
> O PowerShell não verifica o diretório local ao procurar um comando. No PowerShell, altere o diretório para a localização `spx` e chame a ferramenta inserindo `.\spx`.
> Se você adicionar esse diretório ao caminho, o PowerShell e o prompt de comando do Windows encontrarão `spx` em qualquer diretório sem incluir o prefixo `.\`.

#### <a name="linux-install"></a>[Instalação do Linux](#tab/linuxinstall)

Siga estas etapas para instalar a CLI de Fala no Linux em uma CPU x64:

1. Instalar o [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Baixe o [arquivo zip](https://aka.ms/speech/spx-zips.zip) da CLI de Fala e, em seguida, extraia o arquivo.
3. Acesse o diretório raiz `spx-zips` que você extraiu no download e extraia `spx-netcore-30-linux-x64` para um novo diretório `~/spx`.
4. Em um terminal, digite estes comandos:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digite `spx` para ver a ajuda da CLI de Fala.

***

## <a name="create-subscription-config"></a>Criar configuração da assinatura

Para começar a usar a CLI de Fala, primeiro você precisa inserir sua chave de assinatura da Fala e informações da região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região. Depois de obter a chave de assinatura e o identificador da região (por exemplo, `eastus`, `westus`), execute os comandos a seguir.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Sua autenticação de assinatura agora está armazenada para futuras solicitações de SPX. Se você precisar remover qualquer um desses valores armazenados, execute `spx config @region --clear` ou `spx config @key --clear`.
