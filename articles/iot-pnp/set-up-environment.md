---
title: Configurar os recursos de IoT necessários para o Plug and Play de IoT | Microsoft Docs
description: Crie uma instância do serviço de provisionamento de dispositivos e Hub IoT para usar com os guias de início rápido e tutoriais do IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3b7c9b51bad45bb348f70c8b0e433404b49b5aac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761355"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Configurar seu ambiente para os guias de início rápido e tutoriais do IoT Plug and Play

Antes de concluir qualquer um dos tutoriais e guias de início rápido de IoT Plug and Play, você precisa configurar um hub IoT e o DPS (serviço de provisionamento de dispositivos) em sua assinatura do Azure. Você também precisará de cópias locais dos arquivos de modelo usados pelos aplicativos de exemplo e pela ferramenta do Azure IoT Explorer.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para evitar a necessidade de instalar o CLI do Azure localmente, você pode usar o Azure Cloud Shell para configurar os serviços de nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Criar os recursos

Crie um grupo de recursos do Azure para os recursos:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Crie um Hub IoT. O comando a seguir usa o nome `my-pnp-hub` como um exemplo para o nome do Hub IOT a ser criado. Escolha um nome exclusivo para o Hub IoT a ser usado no lugar de `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Crie uma instância de DPS. O comando a seguir usa o nome `my-pnp-dps` como um exemplo para o nome da instância do DPS a ser criada. Escolha um nome exclusivo para a instância do DPS a ser usada no lugar de `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Para vincular a instância do DPS ao Hub IoT, use os comandos a seguir. Substitua `my-pnp-dps` e `my-pnp-hub` pelos nomes exclusivos que você escolheu anteriormente:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Recuperar as configurações

Alguns guias de início rápido e tutoriais usam a cadeia de conexão para o Hub IoT. Você também precisa da cadeia de conexão ao configurar a ferramenta do Azure IoT Explorer. Recupere a cadeia de conexão e anote-a agora. Substitua `my-pnp-hub` pelo nome exclusivo que você escolheu para o Hub IOT:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

A maioria dos guias de início rápido e tutoriais usam o *escopo de ID* da sua configuração de DPS. Recupere o escopo da ID e anote-o agora. Substitua `my-pnp-dps` pelo nome exclusivo que você escolheu para sua instância do DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Todos os guias de início rápido e tutoriais usam um registro de dispositivo de DPS. Use o comando a seguir para criar um `my-pnp-device` *registro de dispositivo individual* em sua instância do DPS. Substitua `my-pnp-dps` pelo nome exclusivo que você escolheu para sua instância de DPS. Anote os valores de ID de registro e chave primária a serem usados nos guias de início rápido e tutoriais:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

Crie cinco variáveis de ambiente para configurar os exemplos nos guias de início rápido e tutoriais para usar o DPS (serviço de provisionamento de dispositivos) para se conectar ao seu hub IoT:

* **IOTHUB_DEVICE_SECURITY_TYPE**: o valor `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: o escopo da ID de DPS que você fez uma anotação anteriormente.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: o valor `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: a chave primária de registro anotada anteriormente.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: o valor `global.azure-devices-provisioning.net`

Os exemplos de serviço precisam das seguintes variáveis de ambiente para identificar o Hub e o dispositivo aos quais se conectar:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Por exemplo, em um shell bash do Linux:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Por exemplo, na linha de comando do Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Baixar os arquivos de modelo

Os guias de início rápido e os tutoriais usam arquivos de modelo de exemplo para o controlador de temperatura e dispositivos termostato. Para baixar os arquivos de modelo de exemplo:

1. Crie uma pasta chamada *models* no computador local.

1. Clique com o botão direito do mouse em [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) e salve o arquivo JSON na pasta *models*.

1. Clique com o botão direito do mouse em [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) e salve o arquivo JSON na pasta *models*.

## <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Os guias de início rápido e os tutoriais usam a ferramenta **Azure IOT Explorer** . Vá para [versões do Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para a versão mais recente. Baixe e instale a versão mais recente do aplicativo para seu sistema operacional.

Na primeira vez que você executar a ferramenta, você será solicitado a fornecer a cadeia de conexão do Hub IoT. Use a cadeia de conexão que você anotou anteriormente.

Configure a ferramenta para usar os arquivos de modelo que você baixou anteriormente. Na home page da ferramenta, selecione configurações de **IoT plug and Play**e, em seguida, **+ Adicionar > pasta local**. Selecione a pasta *modelos* que você criou anteriormente. Em seguida, selecione **salvar** para salvar as configurações.

Para saber mais, confira [Instalar e usar o Azure IoT Explorer](howto-use-iot-explorer.md).

## <a name="remove-the-resources"></a>Remover os recursos

Você pode usar o Hub IoT e a instância do DPS para todos os guias de início rápido e tutoriais do IoT Plug and Play, para que você só precise concluir as etapas neste artigo uma vez. Quando tiver terminado, você poderá removê-las da sua assinatura com o seguinte comando:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou seu ambiente, pode experimentar um dos guias de início rápido ou tutoriais como:

> [!div class="nextstepaction"]
> [Conectar um aplicativo de dispositivo de Plug and Play de IoT de exemplo ao Hub IoT (Node.js)](quickstart-connect-device-node.md)
