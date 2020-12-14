---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 327dce053c38fdb7b03e7ceb0d7a62ec8b8e7248
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96993071"
---
## <a name="download-and-install"></a>Fazer o download e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estas etapas para instalar a CLI de Fala no Windows:

1. No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Ao instalá-los pela primeira vez, pode ser necessária uma reinicialização.
2. Baixe o [arquivo zip](https://aka.ms/speech/spx-zips.zip) da CLI de Fala e, em seguida, extraia o arquivo.
3. Acesse o diretório em que você extraiu `spx-zips`. Essa pasta contém arquivos de programas para a CLI de Fala em uma variedade de plataformas. 
4. Extraia os arquivos da sua plataforma (`spx-net471` para .NET Framework 4.7 ou `spx-netcore-win-x64` para .NET Core 3.0 em uma CPU x64). Tenha em mente que você executará `spx` nesse diretório.

### <a name="run-the-speech-cli"></a>Executar a CLI de Fala

1. Abra o prompt de comando ou o PowerShell e procure o diretório em que você extraiu a CLI de Fala.  
2. Digite `spx` para ver os comandos de ajuda para a CLI de Fala.

> [!NOTE]
> O PowerShell não verifica o diretório local ao procurar um comando. No PowerShell, altere o diretório para a localização `spx` e chame a ferramenta inserindo `.\spx`.
> Se você adicionar esse diretório ao caminho, o PowerShell e o prompt de comando do Windows encontrarão `spx` em qualquer diretório sem incluir o prefixo `.\`.

### <a name="font-limitations"></a>Limitações de fontes

No Windows, a CLI de Fala só pode mostrar as fontes disponíveis no prompt de comando do computador local.
O [Terminal do Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) dá suporte a todas as fontes produzidas de maneira interativa pela CLI de Fala.

Se você produzir a saída em um arquivo, um editor de texto como o Bloco de notas ou um navegador da Web como o Microsoft Edge também poderá mostrar todas as fontes.

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

#### <a name="docker-install-windows-linux-macos"></a>[Instalação do Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Siga estas etapas para instalar a CLI de Fala em um contêiner do Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Instale o Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a> para sua plataforma, se ainda não estiver instalado.
2. Em um novo prompt de comando ou terminal, digite este comando: 
   ```shell   
   docker pull msftspeech/spx
   ```
3. Digite este comando. Você verá informações da Ajuda para a CLI de Fala: 
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Montar um diretório no contêiner

A ferramenta CLI de Fala salva as definições de configuração como arquivos e carrega esses arquivos durante a execução de qualquer comando (exceto os comandos da Ajuda).
Ao usar a CLI de Fala em um contêiner do Docker, você precisará montar um diretório local por meio do contêiner, de modo que a ferramenta possa armazenar ou localizar as definições de configuração, e também para que a ferramenta possa ler ou gravar todos os arquivos necessários para o comando, como arquivos de áudio de fala.

No Windows, digite este comando para criar um diretório local que possa ser usado pela CLI de Fala no contêiner:

`mkdir c:\spx-data`

Ou então, no Linux ou no macOS, digite este comando em um terminal para criar um diretório e ver o caminho absoluto dele:

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

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

No Linux ou macOS, os comandos se parecerão com o exemplo abaixo. Substitua `ABSOLUTE_PATH` pelo caminho absoluto do diretório montado. Esse caminho foi retornado pelo comando `pwd` na seção anterior. 

Se você executar esse comando antes de definir sua chave e região, receberá uma mensagem de erro informando que você deve definir sua chave e região:
```shell   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Para usar o comando `spx` instalado em um contêiner, sempre insira o comando completo mostrado acima, seguido dos parâmetros da solicitação.
Por exemplo, no Windows, este comando define a chave:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

Para obter uma interação mais estendida com a ferramenta de linha de comando, inicie um contêiner com um shell do Bash interativo adicionando um parâmetro de ponto de entrada.
No Windows, insira este comando para iniciar um contêiner que expõe uma interface de linha de comando interativa, na qual você pode inserir vários comandos `spx`:
```shell
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Não é possível usar o microfone do computador durante a execução da CLI da Fala em um contêiner do Docker. No entanto, você pode ler e salvar arquivos de áudio em seu diretório montado local. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Criar configuração da assinatura

Para começar a usar a CLI de Fala, você precisa inserir a chave de assinatura de Fala e o identificador de região. Obtenha essas credenciais seguindo as etapas em [Experimente o serviço de Fala gratuitamente](../overview.md#try-the-speech-service-for-free).
Depois de obter a chave de assinatura e o identificador da região (por exemplo, `eastus`, `westus`), execute os comandos a seguir.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Sua autenticação de assinatura agora está armazenada para futuras solicitações de SPX. Se você precisar remover qualquer um desses valores armazenados, execute `spx config @region --clear` ou `spx config @key --clear`.
