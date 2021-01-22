---
title: Como criar, implantar e estender o IoT Plug and Play Bridge | Microsoft Docs
description: Identifique os componentes da ponte de Plug and Play IoT. Saiba como estender a ponte e como executá-la em dispositivos IoT, gateways e como um módulo IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 43c89b0fac08bf9f2c72f885fbf4788371876b17
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678569"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>Criar, implantar e estender a ponte de Plug and Play IoT

A ponte de Plug and Play IoT permite que você conecte os dispositivos existentes anexados a um gateway ao Hub IoT. Você usa a ponte para mapear as interfaces de Plug and Play de IoT para os dispositivos anexados. Uma interface de Plug and Play IoT define a telemetria que um dispositivo envia, as propriedades sincronizadas entre o dispositivo e a nuvem e os comandos que o dispositivo responde. Você pode instalar e configurar o aplicativo de ponte de software livre em gateways Windows ou Linux.

Este artigo explica em detalhes como:

- Configurar uma ponte.
- Estenda uma ponte criando novos adaptadores.
- Como compilar e executar a ponte em vários ambientes.

Para obter um exemplo simples que mostra como usar a ponte, consulte [como conectar o exemplo de ponte de plug and Play de IOT que é executado no Linux ou Windows para o Hub IOT](howto-use-iot-pnp-bridge.md).

As orientações e exemplos neste artigo pressupõem familiaridade básica com o [Azure digital gêmeos](../digital-twins/overview.md) e o [plug and Play de IOT](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Pré-requisitos gerais

### <a name="supported-os-platforms"></a>Plataformas de sistema operacional com suporte

Há suporte para as seguintes plataformas e versões de sistema operacional:

|Plataforma  |Versões compatíveis  |
|---------|---------|
|Windows 10 |     Todos os SKUs do Windows têm suporte. Por exemplo: IoT Enterprise, servidor, desktop, IoT Core. *Para a funcionalidade de monitoramento de integridade da câmera, a compilação 20H1 ou posterior é recomendada. Todas as outras funcionalidades estão disponíveis em todas as compilações do Windows 10.*  |
|Linux     |Testado e com suporte no Ubuntu 18, 4, a funcionalidade em outras distribuições não foi testada.         |
||

### <a name="hardware"></a>Hardware

- Qualquer plataforma de hardware que dê suporte às versões e SKUs do sistema operacional acima.
- Os sensores serial, USB, Bluetooth e de câmera são suportados nativamente. A ponte de Plug and Play IoT pode ser estendida para dar suporte a qualquer periférico ou sensor personalizado.

### <a name="azure-iot-products-and-tools"></a>Produtos e ferramentas do Azure IoT

- **Hub IOT do Azure** -você precisa de um [Hub IOT do](../iot-hub/index.yml) Azure em sua assinatura do Azure para conectar seu dispositivo ao. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. Se você não tiver um hub IoT, [siga estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md). O suporte de Plug and Play IoT não está incluído em hubs IoT de camada básica.
- **Azure IOT Explorer** -para interagir com seu dispositivo de plug and Play de IOT, você pode usar a ferramenta do Azure IOT Explorer. [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional. Configure a ferramenta do Azure IoT Explorer para se conectar ao seu hub IoT e procurar definições de modelo na pasta *pnpbridge\docs\schemas* no **IOT-plug-and-Play-Bridge** clonado.

## <a name="configure-a-bridge"></a>Configurar uma ponte

Há duas maneiras de configurar a ponte:

- Se a ponte estiver sendo executada nativamente em um dispositivo ou gateway de IoT, use o arquivo de configuração. Esse cenário pode usar um computador Linux ou Windows.
- Se a ponte estiver sendo executada como um módulo IoT Edge no tempo de execução IoT Edge, use a propriedade de módulo.... Esse cenário pressupõe que o tempo de execução de IoT Edge seja hospedado em um ambiente Linux.

### <a name="configuration-file"></a>Arquivo de configuração

Quando Plug and Play ponte está em execução nativamente em um dispositivo ou gateway de IoT, ele usa um arquivo de configuração para:

- Obtenha as informações de conexão do Hub IoT ou IoT Central.
- Configure dispositivos para os quais as interfaces do IoT Plug and Play são publicadas.

Use uma das opções a seguir para fornecer o arquivo de configuração para a ponte:

- Passe o caminho para o arquivo de configuração como um parâmetro de linha de comando para o executável da ponte.
- Use o *config.jsexistente no* arquivo na pasta *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* .

O [esquema do arquivo de configuração](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) está disponível no repositório github.

> [!TIP]
> Se você editar um arquivo de configuração de ponte no VS Code, poderá usar esse arquivo de esquema para validar o arquivo.

### <a name="iot-edge-module-configuration"></a>Configuração do módulo IoT Edge

Quando a ponte é executada como um módulo IoT Edge em um tempo de execução IoT Edge, o arquivo de configuração é enviado da nuvem como uma atualização para a `PnpBridgeConfig` propriedade desejada. A ponte aguarda essa atualização de propriedade antes de configurar os adaptadores e os componentes.

## <a name="extend-the-bridge"></a>Estender a ponte

Para estender os recursos da ponte, você pode criar seus próprios adaptadores de ponte.

A ponte usa adaptadores para:

- Estabeleça uma conexão entre um dispositivo e a nuvem.
- Habilite o fluxo de dados entre um dispositivo e a nuvem.
- Habilite o gerenciamento de dispositivos da nuvem.

Cada adaptador de ponte deve:

- Crie uma interface gêmeos digital.
- Use a interface para associar a funcionalidade do lado do dispositivo a recursos baseados em nuvem, como telemetria, propriedades e comandos.
- Estabeleça a comunicação de controle e de dados com o hardware ou firmware do dispositivo.

Cada adaptador de ponte interage com um tipo específico de dispositivo com base em como o adaptador se conecta e interage com o dispositivo. Mesmo se a comunicação com um dispositivo usar um protocolo de handshake, um adaptador de ponte poderá ter várias maneiras de interpretar os dados do dispositivo. Nesse cenário, o adaptador de ponte usa informações para o adaptador no arquivo de configuração para determinar a *configuração de interface* que o adaptador deve usar para analisar os dados.

Para interagir com o dispositivo, um adaptador de ponte usa um protocolo de comunicação com suporte do dispositivo e as APIs fornecidas pelo sistema operacional subjacente ou pelo fornecedor do dispositivo.

Para interagir com a nuvem, um adaptador de ponte usa as APIs fornecidas pelo SDK do dispositivo IoT do Azure para enviar telemetria, criar interfaces de atualização digitais, enviar atualizações de propriedade e criar funções de retorno de chamada para atualizações de propriedade e comandos.

### <a name="create-a-bridge-adapter"></a>Criar um adaptador de ponte

A ponte espera que um adaptador de ponte implemente as APIs definidas na interface [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Nesta interface:

- `PNPBRIDGE_ADAPTER_CREATE` cria o adaptador e configura os recursos de gerenciamento de interface. Um adaptador também pode depender de parâmetros globais do adaptador para a criação do adaptador. Essa função é chamada uma vez para um único adaptador.
- `PNPBRIDGE_COMPONENT_CREATE` cria as interfaces de cliente de "digital" e associa as funções de retorno de chamada. O adaptador inicia o canal de comunicação com o dispositivo. O adaptador pode configurar os recursos para habilitar o fluxo de telemetria, mas não inicia a telemetria de relatório até que `PNPBRIDGE_COMPONENT_START` seja chamado. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração.
- `PNPBRIDGE_COMPONENT_START` é chamado para permitir que o adaptador de ponte comece a encaminhar a telemetria do dispositivo para o cliente digital. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração.
- `PNPBRIDGE_COMPONENT_STOP` interrompe o fluxo de telemetria.
- `PNPBRIDGE_COMPONENT_DESTROY` destrói o cliente de teledigital e os recursos de interface associados. Essa função é chamada uma vez para cada componente de interface no arquivo de configuração quando a ponte é interrompida ou quando ocorre um erro fatal.
- `PNPBRIDGE_ADAPTER_DESTROY` limpa os recursos do adaptador de ponte.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interação de núcleo de ponte com adaptadores de ponte

A lista a seguir descreve o que acontece quando a ponte é iniciada:

1. Quando a ponte é iniciada, o Gerenciador do adaptador de ponte examina cada componente de interface definido no arquivo de configuração e chama `PNPBRIDGE_ADAPTER_CREATE` o adaptador apropriado. O adaptador pode usar parâmetros de configuração de adaptador global para configurar recursos para dar suporte a várias *configurações de interface*.
1. Para cada dispositivo no arquivo de configuração, o Gerenciador de ponte inicia a criação da interface chamando `PNPBRIDGE_COMPONENT_CREATE` no adaptador de ponte apropriado.
1. O adaptador recebe quaisquer definições de configuração de adaptador opcionais para o componente de interface e usa essas informações para configurar conexões com o dispositivo.
1. O adaptador cria as interfaces de cliente de atualização digital e associa as funções de retorno de chamada para atualizações de propriedade e comandos. O estabelecimento de conexões de dispositivo não deve bloquear o retorno dos retornos de chamada após a criação da interface de migração de troca de digital. A conexão de dispositivo ativa é independente do cliente de interface ativa que a ponte cria. Se uma conexão falhar, o adaptador assumirá que o dispositivo está inativo. O adaptador de ponte pode optar por tentar fazer essa conexão novamente.
1. Depois que o Gerenciador de adaptador de ponte cria todos os componentes de interface especificados no arquivo de configuração, ele registra todas as interfaces com o Hub IoT do Azure. O registro é uma chamada assíncrona e de bloqueio. Quando a chamada é concluída, ela dispara um retorno de chamada no adaptador de ponte que pode iniciar a manipulação de retornos de chamada de comando e propriedade da nuvem.
1. Em seguida, o Gerenciador do adaptador de ponte chama `PNPBRIDGE_INTERFACE_START` em cada componente e o adaptador de ponte começa a reportar a telemetria para o cliente digital.

### <a name="design-guidelines"></a>Diretrizes de design

Siga estas diretrizes ao desenvolver um novo adaptador de ponte:

- Determine quais recursos de dispositivo têm suporte e a aparência da definição de interface dos componentes que usam esse adaptador.
- Determine qual interface e parâmetros globais seu adaptador precisa definir no arquivo de configuração.
- Identifique a comunicação de dispositivo de nível baixo necessária para dar suporte às propriedades e aos comandos do componente.
- Determine como o adaptador deve analisar os dados brutos do dispositivo e convertê-los nos tipos de telemetria que a definição de interface de IoT Plug and Play especifica.
- Implemente a interface do adaptador de ponte descrita anteriormente.
- Adicione o novo adaptador ao manifesto do adaptador e Compile a ponte.

### <a name="enable-a-new-bridge-adapter"></a>Habilitar um novo adaptador de ponte

Você habilita adaptadores na ponte adicionando uma referência em [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Os retornos de chamada do adaptador de ponte são invocados sequencialmente. Um adaptador não deve bloquear um retorno de chamada porque isso impede que o núcleo da ponte faça progressos.

### <a name="sample-camera-adapter"></a>Adaptador de câmera de exemplo

O [Leiame do adaptador de câmera](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) descreve um adaptador de câmera de exemplo que você pode habilitar.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Compilar e executar a ponte em um dispositivo ou gateway de IoT

| Plataforma | Com suporte |
| :-----------: | :-----------: |
| Windows |  Sim |
| Linux | Sim |

### <a name="prerequisites"></a>Pré-requisitos

Para concluir esta seção, você precisa instalar o seguinte software em seu computador local:

- Um ambiente de desenvolvimento que dá suporte à compilação de C++, como o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) , certifique-se de incluir o componente do Gerenciador de pacotes NuGet e o **desenvolvimento de desktop com** a carga de trabalho do C++ ao instalar o Visual Studio.
- [CMake](https://cmake.org/download/) – quando você instala o CMake, selecione a opção **Adicionar CMake ao caminho do sistema**.
- Se você estiver criando no Windows, também precisará baixar o [SDK do windows 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Código-fonte

Clone o repositório de [ponte de plug and Play IOT](https://github.com/Azure/iot-plug-and-play-bridge) para seu computador local:

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Espere que o comando anterior demore alguns minutos para ser executado.

> [!NOTE]
> Se você encontrar problemas com a `git submodule update` falha no Windows, tente o seguinte comando para resolver o problema: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Criar a ponte no Windows

Abra o **prompt de comando do desenvolvedor para VS 2019** e navegue até a pasta que contém o repositório clonado e execute os seguintes comandos:

```console
cd pnpbridge\scripts\windows

build.cmd
```

Espere que o comando anterior demore alguns minutos para ser executado.

Opcionalmente, você pode abrir o arquivo de solução gerado *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. sln* no Visual Studio para editar, recompilar e depurar o código.

> [!TIP]
> Este projeto usa CMAKE para gerar os arquivos de projeto. Quaisquer modificações feitas no projeto no Visual Studio poderão ser perdidas se os arquivos CMAKE apropriados não forem atualizados.

### <a name="build-the-bridge-on-linux"></a>Criar a ponte no Linux

Usando o shell bash, navegue até a pasta que contém o repositório clonado e execute os seguintes comandos:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Configurar os sensores genéricos

Modifique os parâmetros a seguir no `pnp_bridge_connection_parameters` nó do *config.jsno* arquivo na pasta *IOT-plug-and-Play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* no Windows ou na pasta *IOT-plug-and-Play-bridge/pnpbridge/CMake/pnpbridge_linux \ src/pnpbridge/Samples/console* no Windows:

Se você estiver usando uma cadeia de conexão para se conectar ao seu hub IoT:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> O `symmetric_key` valor deve corresponder à chave SAS na cadeia de conexão.

Se você estiver usando o DPS para se conectar ao seu hub IoT ou IoT Central aplicativo:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Examine o restante do arquivo de configuração para ver quais componentes de interface e parâmetros globais estão configurados neste exemplo.

### <a name="start-the-bridge-in-windows"></a>Iniciar a ponte no Windows

Inicie a ponte executando-a no prompt de comando:

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Para usar um arquivo de configuração de um local diferente, passe seu caminho como um parâmetro de linha de comando ao executar a ponte.
  
> [!TIP]
> Se você tiver uma câmera interna ou uma câmera USB conectada ao seu PC, poderá iniciar um aplicativo que usa a câmera, como o aplicativo de **câmera** interno. Quando o aplicativo de **câmera** está em execução, a saída do console de ponte mostra as estatísticas de monitoramento e a taxa de quadros do é relatada por meio da interface de impressão digital para o Hub IOT.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Compilar e executar a ponte como um módulo IoT Edge

| Plataforma | Com suporte |
| :-----------: | :-----------: |
| Windows |  Não |
| Linux | Sim |

### <a name="prerequisites"></a>Pré-requisitos

Para concluir esta seção, você precisa de um hub IoT do Azure de camada Standard ou gratuita. Para aprender a criar um hub IoT, consulte [criar um hub IOT](../iot-hub/iot-hub-create-through-portal.md).

As etapas nesta seção pressupõem que você tenha o seguinte ambiente de desenvolvimento em um computador com Windows 10. Essas ferramentas permitem que você crie e implante um módulo IoT Edge em seu dispositivo IoT Edge:

- O subsistema do Windows para Linux (WSL) 2 executando o Ubuntu 18, 4 LTS. Para saber mais, confira o [Guia de instalação do subsistema do Windows para Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).
- Docker desktop para Windows configurado para usar o WSL 2. Para saber mais, confira o [back-end da área de trabalho do Docker WSL 2](https://docs.docker.com/docker-for-windows/wsl/).
- [Visual Studio Code instalado em seu ambiente Windows](https://code.visualstudio.com/docs/setup/windows) com as três extensões a seguir instaladas:

  - [Pacote de extensão de desenvolvimento remoto](https://aka.ms/vscode-remote/download/extension) -essa extensão permite que você crie e execute código no WSL 2.
  - [Docker para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) -essa extensão deve ser habilitada no ambiente **WSL: Ubuntu-18, 4** do vs Code.
  - [Ferramentas de IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -essa extensão deve ser habilitada no ambiente **WSL: Ubuntu-18, 4** do vs Code.

- Execute os seguintes comandos em seu ambiente WSL 2 para instalar as ferramentas de compilação necessárias:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- O [CLI do Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) instalado em seu ambiente WSL 2 para gerenciar os recursos do Azure.

  > [!TIP]
  > Se preferir, você pode executar os `az` comandos no [Azure cloud Shell](https://shell.azure.com/) em que a CLI é pré-instalada.

### <a name="create-an-iot-edge-device"></a>Criar um dispositivo do IoT Edge

Os comandos aqui criam um dispositivo IoT Edge em execução em uma máquina virtual do Azure. Executar um dispositivo de IoT Edge em uma máquina virtual é útil para testar sua implantação se você não tiver acesso a um dispositivo real.

Para criar um registro de dispositivo IoT Edge no Hub IoT, execute os seguintes comandos em seu ambiente WSL 2. Use o `az login` comando para entrar em sua assinatura do Azure:

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Para criar uma máquina virtual do Azure com o IoT Edge Runtime instalado, execute os comandos a seguir. Atualize os espaços reservados com valores adequados:

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Agora você tem o tempo de execução IoT Edge em execução em uma máquina virtual. Você pode usar o comando a seguir para verificar se o **$edgeAgent** e **$edgeHub** estão em execução no dispositivo:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Você será cobrado enquanto esta máquina virtual estiver em execução. Não se esqueça de desligá-lo quando você não o estiver usando e removê-lo quando tiver terminado de usá-lo.

### <a name="download-the-source-code"></a>Fazer o download do código-fonte

Nas etapas a seguir, você criará a imagem do Docker em seu ambiente WSL 2. Para clonar o repositório **IOT-plug-and-Play-Bridge** , execute os seguintes comandos em um shell bash do WSL 2 em uma pasta adequada:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Espere que o comando anterior demore alguns minutos para ser executado.

### <a name="update-the-dockerfile"></a>Atualizar o *Dockerfile*

Inicie o VS Code, abra a paleta de comandos, insira *WSL remota: Abra a pasta no WSL* e, em seguida, abra a pasta *IOT-plug-and-Play-Bridge* que contém o repositório clonado.

Abra o arquivo *pnpbridge\Dockerfile.AMD64* . Edite as definições de variável de ambiente da seguinte maneira:

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Você pode usar o seguinte comando para recuperar a cadeia de conexão do dispositivo: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Há exemplos de *Dockerfiles* para outras arquiteturas.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Criar a imagem do módulo de ponte de Plug and Play IoT

Em VS Code, abra a paleta de comandos, insira *registros do Docker: faça logon na CLI do Docker* e, em seguida, selecione sua assinatura do Azure e registro de contêiner. Esse comando permite que o Docker se conecte ao seu registro de contêiner e carregue a imagem do módulo.

Abra o *pnpbridge/module.jsno* arquivo. Adicione `{your container registry name}.azurecr.io/iotpnpbridge` como o repositório de imagens de contêiner e `v1` como a versão.

Em VS Code, clique com o botão direito do mouse no arquivo *pnpbridge/module.js* no modo de exibição do **Explorer** , selecione **criar e enviar por push IOT Edge imagem do módulo** e selecione **AMD64** como a plataforma.

No VS Code, no modo de exibição do **Docker** , você pode procurar o conteúdo do registro de contêiner que agora inclui a imagem do módulo **iotpnpbridge: v1-AMD64** .

### <a name="create-a-container-registry"></a>Criar um registro de contêiner

Um dispositivo IoT Edge baixa suas imagens de módulo de um registro de contêiner. Este exemplo usa um registro de contêiner do Azure.

Crie um registro de contêiner do Azure no grupo de recursos **Bridge-Edge-Resources** . Em seguida, habilite o acesso de administrador ao seu registro de contêiner e obtenha as credenciais de que seu dispositivo IoT Edge precisa para baixar as imagens de módulo:

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Criar o manifesto de implantação

Um manifesto de implantação IoT Edge especifica os módulos e os valores de configuração a serem implantados em um dispositivo IoT Edge.

Em VS Code, abra o *pnpbridge/deployment.template.jsno* arquivo:

- Atualize o `registryCredentials` com os valores do comando anterior. O `address` valor é semelhante a `{your container registry name}.azurecr.io` .
- Atualize o `image` valor para o `ModulePnpBridge` . A imagem do módulo é semelhante a: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Substitua o `PnPBridgeConfig` pelo JSON a seguir para configurar o adaptador de detecção de CO2:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Salve as alterações.

Em VS Code, clique com o botão direito do mouse no arquivo *pnpbridge/deployment.template.js* no modo de exibição do **Explorer** , selecione **gerar IOT Edge manifesto de implantação**. Esse comando gera o *pnpbridge/config/deployment.amd64.jsno manifesto de* implantação.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Implantar a ponte em seu dispositivo IoT Edge

Em VS Code, abra a paleta de comandos, insira *Hub IOT do Azure: selecione Hub IOT* e, em seguida, selecione sua assinatura e Hub IOT.

Em VS Code, clique com o botão direito do mouse em *pnpbridge/config/deployment.amd64.jsno* arquivo no modo de exibição do **Explorer** , selecione **criar implantação para um único dispositivo** e selecione **ponte-borda-dispositivo**.

Para exibir o status dos módulos em seu dispositivo, execute o seguinte comando:

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

A lista de módulos em execução agora inclui o módulo **ModulePnpBridge** configurado para usar o adaptador de detecção de CO2.

### <a name="tidy-up"></a>Limpar

Para remover a máquina virtual e o registro de contêiner da sua assinatura do Azure, execute o seguinte comando:

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Estrutura de pastas

*pnpbridge\deps\azure-IOT-SDK-c-PNP*: submódulos git que contêm o SDK do dispositivo IOT do Azure para c SDK.

*pnpbridge\scripts*: criar scripts.

*pnpbridge\src*: código-fonte para IOT plug and Play Bridge Core.

*pnpbridge\src\adapters*: código-fonte para vários adaptadores de ponte IOT plug and Play.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a ponte de Plug and Play IoT, visite o repositório GitHub do [iot plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) .
