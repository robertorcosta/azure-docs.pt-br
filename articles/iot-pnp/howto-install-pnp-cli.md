---
title: Use a extensão Azure IoT para a Azure CLI para interagir com dispositivos IoT Plug e Play Preview | Microsoft Docs
description: Instale a extensão Azure IoT para Azure CLI e use-a para interagir com os dispositivos IoT Plug and Play conectados ao meu hub IoT.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234678"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instale e use a extensão Azure IoT para o Azure CLI

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando cross platform de código aberto para gerenciar recursos do Azure, como o IoT Hub. O Azure CLI está disponível no Windows, Linux e MacOS. O Azure CLI também está pré-instalado no [Azure Cloud Shell](https://shell.azure.com). O Azure CLI permite que você gerencie recursos do Azure IoT Hub, instâncias do Serviço de Provisionamento de Dispositivos e hubs vinculados sem instalar quaisquer extensões.

A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir e testar dispositivos IoT Plug e Play Preview. Você pode usar a extensão para:

- Conecte-se a um dispositivo.
- Veja a telemetria que o dispositivo envia.
- Trabalhe com propriedades do dispositivo.
- Comandos do dispositivo de chamada.

Este artigo mostra como:

- Instale e configure a extensão Azure IoT para o Azure CLI.
- Use a extensão para interagir e testar seus dispositivos.
- Use a extensão para gerenciar interfaces no repositório do modelo.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instale a extensão Azure IoT para o Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Passo 1 - Instalar o Azure CLI

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar o Azure CLI em seu ambiente. Para usar todos os comandos abaixo, sua versão Azure CLI deve ser a versão 2.0.73 ou superior. Use `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Passo 2 - Instalar extensão ioT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-iot`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-iot` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Use a extensão Azure IoT para o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

Para fazer login na assinatura do Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se você estiver usando o shell de nuvem do Azure, você está automaticamente conectado e não precisa executar o comando anterior.

Para usar a extensão Azure IoT para o Azure CLI, você precisa:

- Um Hub IoT do Azure. Existem muitas maneiras de adicionar um hub de IoT à sua assinatura do Azure, como [Criar um hub de IoT usando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da seqüência de conexões do hub IoT para executar os comandos de extensão Azure IoT. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registrado em seu hub de IoT. Você pode usar o seguinte comando Azure CLI para `{YourIoTHubName}` registrar `{YourDeviceID}` um dispositivo, certifique-se de substituir os espaços reservados e espaços por seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alguns comandos precisam da seqüência de conexões para um repositório de modeloda empresa. Um repositório modelo para sua empresa é criado quando você a bordo pela primeira vez [para o portal Azure Certified for IoT](howto-onboard-portal.md). Um terceiro pode compartilhar sua cadeia de conexão de repositório de modelo com você para lhe dar acesso às suas interfaces e modelos.

### <a name="interact-with-a-device"></a>Interaja com um dispositivo

Você pode usar a extensão para visualizar e interagir com dispositivos IoT Plug and Play que estão conectados a um hub ioT. A extensão funciona com o gêmeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Listar dispositivos e interfaces

Liste todos os dispositivos em um Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Liste todas as interfaces registradas por um dispositivo IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriedades

Liste todas as propriedades e valores de propriedade para uma interface em um dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Defina o valor de uma propriedade de leitura-gravação:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Um exemplo de arquivo de carga para definir a propriedade **de nome** na interface do **sensor** de um dispositivo para **Contoso** parece o seguinte:

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

Liste todos os comandos para uma interface em um dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sem `--repo-login` o parâmetro, este comando usa o repositório de modelos públicos.

Invoque um comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventos duplos digitais

Monitore todos os eventos duplos digitais IoT Plug and Play a partir de um dispositivo específico e interface indo para o grupo de consumidores do hub de eventos **$Default:**

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitore todos os eventos duplos digitais IoT Plug and Play a partir de um dispositivo específico e interface que vá para um grupo de consumidores específico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gerenciar interfaces em um repositório de modelos

Os comandos a seguir usam o repositório público do modelo IoT Plug and Play. Para usar um repositório de `--login` modelo da empresa, adicione o argumento com a seqüência de conexão do repositório do modelo.

Listar interfaces no repositório público do modelo IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Mostrar uma interface no repositório público do modelo IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Crie uma interface no repositório de modelos da empresa IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode criar diretamente uma interface no repositório de modelos públicos.

Atualize uma interface no repositório de modelos da empresa IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode atualizar diretamente uma interface no repositório de modelos públicos.

Publique uma interface do seu repositório de modelos da empresa IoT Plug and Play para o repositório de modelos públicos. Esta operação torna a interface imutável:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar interfaces no repositório de modelos públicos.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gerenciar modelos de capacidade do dispositivo em um repositório de modelos

Os comandos a seguir usam o repositório público do modelo IoT Plug and Play. Para usar um repositório de `--login` modelo da empresa, adicione o argumento com a seqüência de conexão do repositório do modelo.

Liste os modelos de capacidade do dispositivo no repositório de modelos públicos IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Mostrar um modelo de capacidade do dispositivo no repositório de modelos públicos IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Crie um modelo de capacidade de dispositivo em um repositório de modelos da empresa IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode criar diretamente um modelo no repositório de modelos públicos.

Atualize um modelo de capacidade do dispositivo no repositório de modelos da empresa IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Você não pode atualizar diretamente um modelo no repositório de modelos públicos.

Publique um modelo de capacidade do dispositivo a partir do repositório de modelos da empresa IoT Plug and Play para o repositório de modelos públicos. Esta operação torna o modelo imutável:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar modelos para o repositório de modelos públicos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo de como fazer, você aprendeu como instalar e usar a extensão Azure IoT para o Azure CLI para interagir com seus dispositivos Plug and Play. Um próximo passo sugerido é aprender a [gerenciar modelos.](./howto-manage-models.md)
