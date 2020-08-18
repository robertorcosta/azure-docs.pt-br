---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c92d6569e3c92d3bad3575599283c7796bd78225
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068607"
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
> No Windows, a CLI de Fala só pode mostrar as fontes disponíveis no prompt de comando do computador local.
> O [Terminal do Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) dá suporte a todas as fontes produzidas de maneira interativa pela CLI de Fala.
> Se você produzir a saída em um arquivo, um editor de texto como o Bloco de notas ou um navegador da Web como o Microsoft Edge também poderá mostrar todas as fontes.

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

#### <a name="docker-install"></a>[Instalação do Docker](#tab/dockerinstall)

Siga estas etapas para instalar a CLI de Fala em um contêiner do Docker:

1. Instale e execute o [Docker Desktop na sua plataforma](https://www.docker.com/get-started).
1. Em um novo prompt de comando ou terminal, digite este comando: `docker pull msftspeech/spx`
1. Digite este comando. Você verá informações da Ajuda para a CLI de Fala: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Montar um diretório no contêiner

A ferramenta CLI de Fala salva as definições de configuração como arquivos e carrega esses arquivos durante a execução de qualquer comando (exceto os comandos da Ajuda).
Ao usar a CLI de Fala em um contêiner do Docker, você precisará montar um diretório local por meio do contêiner, de modo que a ferramenta possa armazenar ou localizar as definições de configuração, e também para que a ferramenta possa ler ou gravar todos os arquivos necessários para o comando, como arquivos de áudio de fala.

No Windows, digite este comando para criar um diretório local que possa ser usado pela CLI de Fala no contêiner:

`mkdir c:\spx-data`

Ou então, no Linux ou no Mac, digite este comando em um terminal para criar um diretório e ver o caminho absoluto dele:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Você usará o caminho absoluto ao chamar a CLI de Fala.

### <a name="run-speech-cli-in-the-container"></a>Executar a CLI de Fala no contêiner

Esta documentação mostra o comando `spx` da CLI de Fala usado nas instalações que não são do Docker.
Ao chamar o comando `spx` em um contêiner do Docker, você precisará montar um diretório no contêiner para o sistema de arquivos em que a CLI de Fala possa armazenar e localizar valores de configuração, bem como ler e gravar arquivos.
No Windows, os comandos serão iniciados da seguinte maneira:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

No Linux ou no Mac, os comandos serão iniciados de maneira semelhante a esta:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Substitua `/ABSOLUTE_PATH` pelo caminho absoluto mostrado pelo comando `pwd` na seção acima.

Para usar o comando `spx` instalado em um contêiner, sempre insira o comando completo mostrado acima, seguido dos parâmetros da solicitação.
Por exemplo, no Windows, este comando define a chave:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Não é possível usar o microfone nem o alto-falante do computador durante a execução da CLI de Fala em um contêiner do Docker.
> Para usar esses dispositivos, transmita os arquivos de áudio na CLI de Fala para gravação/reprodução fora do contêiner do Docker.
> A ferramenta CLI de Fala pode acessar o diretório local configurado nas etapas acima.

***

## <a name="create-subscription-config"></a>Criar configuração da assinatura

Para começar a usar a CLI de Fala, primeiro você precisa inserir sua chave de assinatura da Fala e informações da região. Confira a página [suporte a regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o identificador de sua região. Depois de obter a chave de assinatura e o identificador da região (por exemplo, `eastus`, `westus`), execute os comandos a seguir.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Sua autenticação de assinatura agora está armazenada para futuras solicitações de SPX. Se você precisar remover qualquer um desses valores armazenados, execute `spx config @region --clear` ou `spx config @key --clear`.
