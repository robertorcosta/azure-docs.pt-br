---
title: Use o emulador Azurite para o desenvolvimento local do Azure Storage
description: O emulador de código aberto Azurite (preview) fornece um ambiente local gratuito para testar seus aplicativos de armazenamento Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029930"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Use o emulador Azurite para o desenvolvimento e testes locais do Azure Storage (visualização)

O emulador de código aberto (preview) da versão Azurite 3.2 fornece um ambiente local gratuito para testar seus aplicativos de armazenamento de blob e fila do Azure. Quando estiver satisfeito com o funcionamento do aplicativo localmente, mude para usar uma conta do Azure Storage na nuvem. O emulador oferece suporte multiplataforma no Windows, Linux e MacOS. O Azurite v3 suporta APIs implementadas pelo serviço Azure Blob.

Azurite é a futura plataforma de emulador de armazenamento. A azurte substitui o [Emulador de Armazenamento Azure](storage-use-emulator.md). O Azurite continuará a ser atualizado para suportar as versões mais recentes das APIs de armazenamento do Azure.

Existem várias maneiras diferentes de instalar e executar o Azurite em seu sistema local:

  1. [Instale e execute a extensão Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Instale e execute o Azurite usando o NPM](#install-and-run-azurite-by-using-npm)
  1. [Instale e execute a imagem Azurite Docker](#install-and-run-the-azurite-docker-image)
  1. [Clonar, construir e executar o Azurite do repositório GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Instale e execute a extensão Azurite Visual Studio Code

Dentro do Visual Studio Code, selecione o painel **EXTENSÕES** e procure *por Azurite* no **EXTENSIONS:MARKETPLACE**.

![Mercado de extensões do Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

Alternativamente, navegue para [o mercado de extensão VS Code](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) em seu navegador. Selecione o botão **Instalar** para abrir o Visual Studio Code e vá diretamente para a página de extensão azurite.

Você pode iniciar ou fechar rapidamente o Azurite clicando em **[Serviço de Blob azurite]** ou **[Serviço de Fila azurite]** na barra de status do CÓDIGO VS ou emitindo os seguintes comandos na paleta de comandos VS Code. Para abrir a paleta de comando, **pressione F1** em VS Code.

A extensão suporta os seguintes comandos Visual Studio Code:

   * **Azurite: Start** - Inicie todos os serviços da Azurite
   * **Azurite: Fechar** - Fechar todos os serviços da Azurite
   * **Azurite: Limpo** - Redefinir todos os dados de persistência dos serviços Azurite
   * **Azurite: Iniciar serviço blob** - Iniciar serviço blob
   * **Azurite: Close Blob Service** - Fechar serviço blob
   * **Azurite: Serviço de Blob Limpo** - Serviço de blob limpo
   * **Azurite: Start Queue Service** - Iniciar serviço de fila
   * **Azurite: Serviço de fila de fechamento** - Fechar serviço de fila
   * **Azurite: Serviço de Fila Limpa** - Serviço de limpeza da fila

Para configurar o Azurite dentro do Visual Studio Code, selecione o painel de extensões. Selecione o ícone **Gerenciar** (engrenagem) para **Azurite**. Selecione **Configurar configurações de extensão**.

![Azurite configurar configurações de extensão](media/storage-use-azurite/azurite-configure-extension-settings.png)

As seguintes configurações são suportadas:

   * **Azurite: Blob Host** - O ponto final de escuta do serviço Blob. A configuração padrão é 127.0.0.1.
   * **Azurite: Blob Port** - A porta de escuta do serviço Blob. A porta padrão é 10000.
   * **Azurite: Debug** - Saída do registro de depuração para o canal Azurite. O valor padrão é **false**.
   * **Azurite: Localização** - O caminho da localização do espaço de trabalho. O padrão é a pasta de trabalho Visual Studio Code.
   * **Azurite: Hospedeiro de Fila** - O ponto final de escuta do serviço de fila. A configuração padrão é 127.0.0.1.
   * **Azurite: Porta da Fila** - A porta de escuta do serviço de fila. A porta padrão é 10001.
   * **Azurite: Silent** - O modo silencioso desativa o registro de acesso. O valor padrão é **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Instale e execute o Azurite usando o NPM

Este método de instalação requer que você tenha [a versão 8.0 do Node.js ou posteriormente](https://nodejs.org) instalada. **npm** é a ferramenta de gerenciamento de pacotes incluída em cada instalação do Node.js. Depois de instalar o Node.js, execute o seguinte comando **npm** para instalar o Azurite.

```console
npm install -g azurite
```

Depois de instalar o Azurite, consulte [Executar Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Instale e execute a imagem Azurite Docker

Use [o DockerHub](https://hub.docker.com/) para puxar a [imagem mais recente do Azurite](https://hub.docker.com/_/microsoft-azure-storage-azurite) usando o seguinte comando:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Execute a imagem de Azurite Docker:**

O comando a seguir executa a imagem de Azurite Docker. O `-p 10000:10000` parâmetro redireciona as solicitações da porta 10000 da máquina host para a instância Dona do Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Especifique o local do espaço de trabalho:**

No exemplo a `-v c:/azurite:/data` seguir, o parâmetro especifica *c:/azurite* como o Azurite persistiu a localização dos dados. O diretório, *c:/azurite,* deve ser criado antes de executar o comando Docker.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Execute apenas o serviço blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Definir todos os parâmetros de Azurite:**

Este exemplo mostra como definir todos os parâmetros de linha de comando. Todos os parâmetros abaixo devem ser colocados em uma única linha de comando.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Consulte [as opções de linha de comando](#command-line-options) para obter mais informações sobre a configuração do Azurite na inicialização.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clonar, construir e executar o Azurite do repositório GitHub

Este método de instalação requer que você tenha [o Git](https://git-scm.com/) instalado. Clone o [repositório GitHub](https://github.com/azure/azurite) para o projeto Azurite usando o seguinte comando de console.

```console
git clone https://github.com/Azure/Azurite.git
```

Após a clonagem do código-fonte, execute seguindo comandos da raiz do repo clonado para construir e instalar o Azurite.

```console
npm install
npm run build
npm install -g
```

Depois de instalar e construir o Azurite, consulte [Executar Azurite a partir de uma linha de comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Executar Azurite a partir de uma linha de comando

> [!NOTE]
> O Azurite não pode ser executado a partir da linha de comando se você só instalou a extensão Visual Studio Code. Em vez disso, use a paleta de comando VS Code. Para obter mais informações, consulte [Instalar e executar a extensão Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Para começar imediatamente com a linha de comando, crie um diretório chamado **c:\azurite**e, em seguida, inicie o Azurite emitindo o seguinte comando:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Este comando diz a Azurite para armazenar todos os dados em um diretório específico, **c:\azurite**. Se a opção **--localização** for omitida, ela usará o diretório de trabalho atual.

## <a name="command-line-options"></a>Opções de linha de comando

Esta seção detalha os switches de linha de comando disponíveis ao iniciar o Azurite. Todos os interruptores de linha de comando são opcionais.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

O **-d** é um atalho para **--depuração**, **-l** switch é um atalho para **--localização**, **-s** é um atalho para **--silent**, e **-h** é um atalho para **--help**.

### <a name="blob-listening-host"></a>Anfitrião ouvinte blob

**Opcional** Por padrão, a Azurite ouvirá 127.0.0.1 como servidor local. Use o switch **--blobHost** para definir o endereço aos seus requisitos.

Aceitar solicitações apenas na máquina local:

```console
azurite --blobHost 127.0.0.1
```

Permitir solicitações remotas:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Permitir solicitações remotas pode tornar seu sistema vulnerável a ataques externos.

### <a name="blob-listening-port-configuration"></a>Configuração da porta de escuta blob

**Opcional** Por padrão, a Azurite ouvirá o serviço Blob na porta 10000. Use o switch **--blobPort** para especificar a porta de escuta que você precisa.

> [!NOTE]
> Depois de usar uma porta personalizada, você precisa atualizar a seqüência de conexões ou a configuração correspondente em suas ferramentas de armazenamento azure ou SDKs.

Personalize a porta de escuta do serviço Blob:

```console
azurite --blobPort 8888
```

Deixe que o sistema selecione automaticamente uma porta disponível:

```console
azurite --blobPort 0
```

A porta em uso é exibida durante a inicialização do Azurite.

### <a name="queue-listening-host"></a>Host de escuta na fila

**Opcional** Por padrão, a Azurite ouvirá 127.0.0.1 como servidor local. Use o **switch --queueHost** para definir o endereço aos seus requisitos.

Aceitar solicitações apenas na máquina local:

```console
azurite --queueHost 127.0.0.1
```

Permitir solicitações remotas:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Permitir solicitações remotas pode tornar seu sistema vulnerável a ataques externos.

### <a name="queue-listening-port-configuration"></a>Configuração da porta de escuta da fila

**Opcional** Por padrão, a Azurite ouvirá o serviço Fila na porta 10001. Use o **switch --queuePort** para especificar a porta de escuta que você precisa.

> [!NOTE]
> Depois de usar uma porta personalizada, você precisa atualizar a seqüência de conexões ou a configuração correspondente em suas ferramentas de armazenamento azure ou SDKs.

Personalize a porta de escuta do serviço de fila:

```console
azurite --queuePort 8888
```

Deixe que o sistema selecione automaticamente uma porta disponível:

```console
azurite --queuePort 0
```

A porta em uso é exibida durante a inicialização do Azurite.

### <a name="workspace-path"></a>Caminho do espaço de trabalho

**Opcional** O Azurite armazena dados no disco local durante a execução. Use o **interruptor --localização** para especificar um caminho como o local do espaço de trabalho. Por padrão, o diretório de trabalho do processo atual será usado.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Log de acesso

**Opcional** Por padrão, o registro de acesso é exibido na janela do console. Desative o visor do registro de acesso usando o interruptor **silencioso .-**

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Registro de depuração

**Opcional** O registro de depuração inclui informações detalhadas sobre cada solicitação e rastreamento de pilha de exceções. Habilite o registro de depuração fornecendo um caminho de arquivo local válido para o switch **--depuração.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Modo solto

**Opcional** Por padrão, o Azurite aplica o modo rigoroso para bloquear cabeçalhos e parâmetros de solicitação não suportados. Desabilite o modo estrito usando o interruptor **solto.**

```console
azurite --loose
```

Observe o interruptor de atalho maiúsculo 'L':

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorização para ferramentas e SDKs

Conecte-se ao Azurite a partir de SDKs ou ferramentas de armazenamento azure, como [o Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)usando qualquer estratégia de autenticação. A autenticação é necessária. A Azurite apoia a autorização com chave compartilhada e assinaturas de acesso compartilhado (SAS). A azurte também suporta acesso anônimo a contêineres públicos.

### <a name="well-known-storage-account-and-key"></a>Conta de armazenamento e chave bem conhecidas

Você pode usar o seguinte nome da conta e a chave com a Azurite. Esta é a mesma conta e chave bem conhecidas usadas pelo emulador de armazenamento Azure legado.

* Nome da conta:`devstoreaccount1`
* Chave da conta:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Além da autenticação SharedKey, o Azurite suporta autenticação SAS de conta e serviço. O acesso anônimo também está disponível quando um contêiner é definido para permitir o acesso público.

### <a name="connection-string"></a>Cadeia de conexão

A maneira mais fácil de se conectar ao Azurite a partir do seu aplicativo é configurar uma seqüência de conexão no arquivo de configuração do aplicativo que faz referência ao atalho *UseDevelopmentStorage=true*. Aqui está um exemplo de uma seqüência de conexão em um arquivo *app.config:*

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Para saber mais, confira [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Contas e chaves de armazenamento personalizadas

O Azurite suporta nomes e chaves `AZURITE_ACCOUNTS` de contas de armazenamento `account1:key1[:key2];account2:key1[:key2];...`personalizados, definindo a variável ambiente no seguinte formato: .

Por exemplo, use uma conta de armazenamento personalizada que tenha uma chave:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Ou use várias contas de armazenamento com 2 chaves cada:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

O Azurite atualiza nomes e chaves de conta personalizadas da variável ambiente a cada minuto por padrão. Com esse recurso, você pode girar dinamicamente a chave da conta ou adicionar novas contas de armazenamento sem reiniciar o Azurite.

> [!NOTE]
> A `devstoreaccount1` conta de armazenamento padrão é desativada quando você define contas de armazenamento personalizadas.

> [!NOTE]
> Atualize a seqüência de conexões de acordo ao usar nomes e chaves de conta personalizados.

> [!NOTE]
> Use `export` a palavra-chave para definir variáveis de `set` ambiente em um ambiente Linux, use no Windows.

### <a name="storage-explorer"></a>Gerenciador de Armazenamento

No Azure Storage Explorer, conecte-se ao Azurite clicando no ícone **Adicionar conta** e, em seguida, **selecione Anexar a um emulador local** e clique em **Conectar**.

## <a name="differences-between-azurite-and-azure-storage"></a>Diferenças entre o Armazenamento Azurite e o Azure

Existem diferenças funcionais entre uma instância local do Azurite e uma conta do Azure Storage na nuvem.

### <a name="endpoint-and-connection-url"></a>Ponto final e URL de conexão

Os pontos finais de serviço para Azurite são diferentes dos pontos finais de uma conta do Azure Storage. O computador local não faz resolução de nomes de domínio, exigindo que os pontos finais do Azurite sejam endereços locais.

Quando você endereça um recurso em uma conta do Azure Storage, o nome da conta faz parte do nome de host uri. O recurso que está sendo endereçado faz parte do caminho URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

O URI a seguir é um endereço válido para uma bolha em uma conta do Azure Storage:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Como o computador local não faz a resolução do nome de domínio, o nome da conta faz parte do caminho URI em vez do nome do host. Use o seguinte formato URI para um recurso no Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

O seguinte endereço pode ser usado para acessar uma bolha em Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Dimensionamento e desempenho

O Azurite não é um serviço de armazenamento escalável e não suporta um grande número de clientes simultâneos. Não há garantia de desempenho. O azurita destina-se a fins de desenvolvimento e teste.

### <a name="error-handling"></a>Tratamento de erros

O Azurite está alinhado com a lógica de manipulação de erros do Azure Storage, mas há diferenças. Por exemplo, as mensagens de erro podem ser diferentes, enquanto os códigos de status de erro se alinham.

### <a name="ra-grs"></a>RA-GRS

O Azurite suporta a replicação geo-redundante de acesso de leitura (RA-GRS). Para obter recursos de armazenamento, acesse o local secundário anexando **-secundário** ao nome da conta. Por exemplo, o seguinte endereço pode ser usado para acessar uma bolha usando o secundário somente leitura em Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite é de código aberto

Contribuições e sugestões para Azurite são bem-vindas. Vá para a página do projeto Azurite [GitHub](https://github.com/Azure/Azurite/projects) ou [problemas do GitHub](https://github.com/Azure/Azurite/issues) para obter marcos e itens de trabalho que estamos rastreando para recursos próximos e correções de bugs. Itens de trabalho detalhados também são rastreados no GitHub.

## <a name="next-steps"></a>Próximas etapas

* [Use o emulador de armazenamento Azure para desenvolvimento e testes de](storage-use-emulator.md) documentos do emulador de armazenamento Azure legado, que está sendo substituído pela Azurite.
* [Configurar as strings de conexão azure Storage](storage-configure-connection-string.md) explica como montar uma seqüência de conexão Azure STorage válida.
