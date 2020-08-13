---
title: Conectar o código do dispositivo de componente do Node.js de exemplo da versão prévia do IoT Plug and Play ao Hub IoT | Microsoft Docs
description: Compile e execute o código do dispositivo do Node.js de exemplo da versão prévia do IoT Plug and Play que usa vários componentes e se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-javascript
ms.openlocfilehash: d26179ab82f29ce8f937f5b444463c1308d92047
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904047"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Tutorial: Conectar um aplicativo de dispositivo de vários componentes de exemplo da versão prévia do IoT Plug and Play ao Hub IoT (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra como criar um aplicativo de dispositivo de exemplo do IoT Plug and Play com componentes e interface raiz, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao hub. O aplicativo de exemplo é escrito para Node.js e é incluído no SDK do Dispositivo do Hub IoT do Azure para Node.js. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa do Node.js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas de [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Para interagir com o dispositivo de exemplo na segunda parte deste tutorial, use a ferramenta **Azure IoT Explorer**. [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o comando a seguir para obter a _cadeia de conexão do hub IoT_ para o seu hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste tutorial:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Você também pode usar a ferramenta Azure IoT Explorer para localizar a cadeia de conexão do hub IoT.

Execute o comando a seguir para obter a _cadeia de conexão de dispositivo_ do dispositivo que você adicionou ao hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste tutorial:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Baixar o código

Neste tutorial, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo Hub IoT do Azure para Node.js.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node) nesta localização:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Essa operação pode levar vários minutos para ser concluída.

## <a name="install-required-libraries"></a>Instalar as bibliotecas necessárias

Use o SDK do dispositivo para criar o código de exemplo incluído. O aplicativo que você cria simula um dispositivo Plug and Play com vários componentes e uma interface raiz que se conecta a um hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Em uma janela de terminal local, acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-node/device/samples/pnp*. Em seguida, execute o seguinte comando para instalar as bibliotecas necessárias:

```cmd/sh
npm install
```

Isso instalará os arquivos npm relevantes necessários para executar os exemplos na pasta.

1. Configure a variável de ambiente com a cadeia de conexão do dispositivo que você anotou anteriormente:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Examine o código

Navegue até a pasta *azure-iot-sdk-node\device\samples\pnp*.

A pasta *azure-iot-sdk-node\device\samples\pnp* contém o código de exemplo do dispositivo controlador de temperatura do IoT Plug and Play.

O código no arquivo *pnpTemperatureController.js* implementa um dispositivo controlador de temperatura do IoT Plug and Play. O modelo que este exemplo implementa usa [vários componentes](concepts-components.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

Abra o arquivo *pnpTemperatureController.js* em um editor de código de sua escolha. O código de exemplo mostra como:

1. Definir o `modelId`, que é o DTMI do dispositivo que está sendo implementado. Esse DTMI é definido pelo usuário e deve corresponder ao DTMI do [modelo de DTDL do controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Implementar os componentes definidos no modelo de DTDL do controlador de temperatura. Os componentes em um controlador de temperatura real devem implementar essas duas interfaces. Essas duas interfaces já estão publicadas em um repositório central. Neste exemplo, as duas interfaces são:
  - Termostato
  - Informações do dispositivo desenvolvidas pelo Azure

3. Definir os nomes dos componentes. Este exemplo tem dois termostatos e um componente de informações do dispositivo.

4. Definir o nome do comando. Estes são os comandos aos quais o dispositivo responde.

5. Definir a constante `serialNumber`. O `serialNumber` é corrigido é fixo para qualquer dispositivo específico.

6. Definir os manipuladores de comando.

7. Definir as funções para enviar respostas de comando.

8. Definir funções auxiliares para registrar solicitações de comando em log.

9. Definir uma função auxiliar para criar as propriedades.

10. Definir um ouvinte para atualizações de propriedade.

11. Definir uma função para enviar telemetria deste dispositivo. Tanto os termostatos quanto o componente raiz enviam telemetria. Essa função recebe o nome do componente como parâmetro.

12. Definir uma função `main` que:

    1. Usa o SDK do dispositivo para criar um cliente de dispositivo e conectar-se ao hub IoT. O dispositivo fornece a `modelId` para que o Hub IoT possa identificar o dispositivo como um dispositivo IoT Plug and Play.

    1. Inicia a escuta de solicitações de comando usando a função `onDeviceMethod`. A função configura um ouvinte para solicitações de comando do serviço:
        - O dispositivo DTDL define os comandos `reboot` e `getMaxMinReport`.
        - A função `commandHandler` define a maneira que o dispositivo responde a um comando.

    1. Inicia o envio de telemetria usando `setInterval` e `sendTelemetry`.

    1. Usa a função `helperCreateReportedPropertiesPatch` para criar as propriedades e a `updateComponentReportedProperties` para atualizar as propriedades.

    1. Usa `desiredPropertyPatchListener` para escutar atualizações de propriedade.

    1. Desabilita todos os ouvintes e tarefas e sai do loop quando você pressiona **Q** ou **q**.

Agora que você já viu o código, use o seguinte comando para executar o exemplo:

```cmd\sh
node pnpTemperatureController.js
```

Você vê a saída a seguir, indicando que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/tutorial-multiple-components-node/multiple-component.png)

Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem de versão prévia do IoT Plug and Play](concepts-developer-guide.md)
