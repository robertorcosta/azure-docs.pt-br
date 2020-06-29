---
title: Usar a extensão do Azure IoT para a CLI do Azure para interagir com dispositivos IoT Plug and Play Preview | Microsoft Docs
description: Instale a extensão do Azure IoT para a CLI do Azure e use-a para interagir com os dispositivos IoT Plug and Play conectados ao meu Hub IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770451"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalar e usar a extensão do Azure IoT para a CLI do Azure

A [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o Hub IoT. A CLI do Azure está disponível no Windows, Linux e MacOS. A CLI do Azure também está pré-instalada no [Azure Cloud Shell](https://shell.azure.com). A CLI do Azure permite gerenciar recursos do Hub IoT do Azure, instâncias do Serviço de Provisionamento de Dispositivos e hubs vinculados sem instalar extensões.

A extensão do Azure IoT para a CLI do Azure é uma ferramenta de linha de comando para interagir com dispositivos IoT Plug and Play Preview e testá-los. É possível usar a extensão para:

- Conectar-se a um dispositivo.
- Exibir a telemetria enviada pelo dispositivo.
- Trabalhar com propriedades do dispositivo.
- Chamar os comandos do dispositivo.

Este artigo mostra como:

- Instalar e configurar a extensão do Azure IoT para a CLI do Azure.
- Usar a extensão para interagir com seus dispositivos e testá-los.
- Usar a extensão para gerenciar interfaces no repositório de modelos.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalar a extensão do Azure IoT para a CLI do Azure

### <a name="step-1---install-the-azure-cli"></a>Etapa 1 – Instalar a CLI do Azure

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure no seu ambiente. Para usar todos os comandos abaixo, sua versão da CLI do Azure deve ser a versão 2.0.73 ou superior. Use `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Etapa 2 – Instalar a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-iot`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-iot` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Usar a extensão do Azure IoT para a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para entrar em sua assinatura do Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se você estiver usando o Azure Cloud Shell, estará automaticamente conectado e não precisará executar o comando anterior.

Para usar a extensão do Azure IoT para a CLI do Azure, você precisa de:

- Um Hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [Criar um hub IoT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do hub IoT para executar os comandos de extensão do Azure IoT. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registrado em seu hub IoT. É possível usar o seguinte comando da CLI do Azure para registrar um dispositivo. Não se esqueça de substituir os espaços reservados `{YourIoTHubName}` e `{YourDeviceID}` pelos seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alguns comandos precisam da cadeia de conexão para um repositório de modelos da empresa. Um repositório de modelos para sua empresa é criado quando você [se integra pela primeira vez ao portal do Azure Certified para IoT](howto-onboard-portal.md). Um terceiro pode compartilhar a cadeia de conexão do repositório de modelos com você para fornecer acesso às interfaces e aos modelos dele.

### <a name="interact-with-a-device"></a>Interagir com um dispositivo

É possível usar a extensão para exibir e interagir com dispositivos IoT Plug and Play conectados a um hub IoT. A extensão funciona com o gêmeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Listar dispositivos e interfaces

Listar todos os dispositivos em um Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Listar todas as interfaces registradas por um dispositivo IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriedades

Listar todas as propriedades e valores de propriedade para uma interface em um dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Defina o valor de uma propriedade de leitura/gravação:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Um arquivo de conteúdo de exemplo para definir a propriedade **name** na interface do **sensor** de um dispositivo para a **Contoso** é semelhante ao seguinte:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Comandos

Listar todos os comandos para uma interface em um dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sem o parâmetro `--repo-login`, esse comando usa o repositório público de modelos.

Invocar um comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventos de gêmeo digital

Monitorar todos os eventos de gêmeo digital do IoT Plug and Play em um dispositivo e uma interface específicos que vão para o grupo de consumidores do hub de eventos **$Default**:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorar todos os eventos de gêmeo digital do IoT Plug and Play em um dispositivo e uma interface específicos que vão para um grupo de consumidores específico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gerenciar interfaces em um repositório de modelos

Os comandos a seguir usam o repositório público de modelos de IoT Plug and Play. Para usar um repositório de modelo corporativo, adicione o argumento `--login` com a cadeia de conexão do repositório de modelos.

Listar interfaces no repositório público de modelos do IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Mostrar uma interface no repositório público de modelos do IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Criar uma interface no repositório de modelos da empresa do IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível criar diretamente uma interface no repositório público de modelos.

Atualizar uma interface no repositório de modelos da empresa do IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente uma interface no repositório público de modelos.

Publicar uma interface do seu repositório de modelos da empresa do IoT Plug and Play no repositório público de modelos. Esta operação torna a interface imutável:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Somente os parceiros da Microsoft podem publicar interfaces no repositório público de modelos.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gerenciar modelos de funcionalidade do dispositivo em um repositório de modelos

Os comandos a seguir usam o repositório público de modelos de IoT Plug and Play. Para usar um repositório de modelo corporativo, adicione o argumento `--login` com a cadeia de conexão do repositório de modelos.

Listar modelos de funcionalidade do dispositivo no repositório público de modelos do IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Mostrar um modelo de funcionalidade do dispositivo no repositório público de modelos do IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Criar um modelo de funcionalidade do dispositivo em um repositório de modelos da empresa do IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível criar diretamente um modelo no repositório público de modelos.

Atualizar um modelo de funcionalidade do dispositivo no repositório de modelos da empresa do IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente um modelo no repositório público de modelos.

Publicar um modelo de funcionalidade do dispositivo do seu repositório de modelos da empresa do IoT Plug and Play no repositório público de modelos. Esta operação torna o modelo imutável:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Somente os parceiros da Microsoft podem publicar modelos no repositório público de modelos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu a instalar e a usar a extensão do Azure IoT para a CLI do Azure a fim de interagir com seus dispositivos Plug and Play. Uma próxima etapa sugerida é saber como [Gerenciar modelos](./howto-manage-models.md).
