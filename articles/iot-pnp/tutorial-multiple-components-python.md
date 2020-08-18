---
title: Conectar o código do dispositivo de componente do Python de exemplo da versão prévia do IoT Plug and Play ao Hub IoT | Microsoft Docs
description: Compile e execute o código do dispositivo do Python de exemplo da versão prévia do IoT Plug and Play que usa vários componentes e se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905846"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Tutorial: Conectar um aplicativo de dispositivo de vários componentes de exemplo da versão prévia do IoT Plug and Play ao Hub IoT (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra como criar um aplicativo de dispositivo de exemplo do IoT Plug and Play com componentes e interface raiz, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao hub. O aplicativo de exemplo é escrito no Python e é incluído no SDK do dispositivo de IoT do Azure para Python. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/).

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

## <a name="set-up-your-environment"></a>Configure seu ambiente

Este pacote é publicado como um PIP para a atualização da versão prévia pública. A versão do pacote deve ser a mais recente ou `2.1.4`

No ambiente Python local, instale o arquivo da seguinte maneira:

```cmd/sh
pip install azure-iot-device
```

Clone o repositório de IoT do SDK do Python e faça check-out do **pnp-preview-refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo controlador de temperatura do IoT Plug and Play. O modelo que este exemplo implementa usa [vários componentes](concepts-components.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* contém o código de exemplo do dispositivo IoT Plug and Play. Os arquivos para o exemplo do controlador de temperatura são:

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

O controlador de temperatura tem vários componentes e uma interface raiz, com base no modelo de DTDL do controlador de temperatura.

Abra o arquivo *pnp_temp_controller_with_thermostats.py* em um editor de sua escolha. O código neste arquivo:

1. Importa `pnp_helper_preview_refresh.py` para obter acesso aos métodos auxiliares.

2. Define dois DTMIs (identificadores de modelo de gêmeos digitais) que representam com exclusividade duas interfaces diferentes, definidas no modelo de DTDL. Os componentes em um controlador de temperatura real devem implementar essas duas interfaces. Essas duas interfaces já estão publicadas em um repositório central. Esses DTMIs devem ser conhecidos do usuário e variar dependendo do cenário de implementação do dispositivo. Para o exemplo atual, essas duas interfaces representam:

  - Um termostato
  - Informações do dispositivo desenvolvidas pelo Azure.

3. Define o `model_id` do DTMI do dispositivo que está sendo implementado. O DTMI é definido pelo usuário e deve corresponder ao DTMI no arquivo de modelo de DTDL.

4. Define os nomes fornecidos aos componentes no arquivo DTDL. Há dois termostatos conectados no DTDL e um componente de informações do dispositivo. Uma constante chamada `serial_number` também é definida na interface raiz. Uma `serial_number` não pode ser alterada em um dispositivo.

5. Define implementações do manipulador de comando. Elas definem o que o dispositivo faz quando recebe solicitações de comando.

6. Define funções para criar uma resposta de comando. Elas definem como o dispositivo responde com as solicitações de comando. Você cria funções de resposta de comando se um comando precisa enviar uma resposta personalizada de volta para o hub IoT. Se uma função de resposta para um comando não for fornecida, uma resposta genérica será enviada. Neste exemplo, somente o comando **getMaxMinReport** tem uma resposta personalizada.

7. Define uma função para enviar telemetria deste dispositivo. Os termostatos e a interface raiz enviam telemetria. Essa função usa um parâmetro de nome de componente opcional para habilitá-la a identificar qual componente enviou a telemetria.

8. Define um ouvinte para solicitações de comando.

9. Define um ouvinte para as atualizações de propriedade desejadas.

10. Tem uma função `main` que:

    1. Usa o SDK do dispositivo para criar um cliente de dispositivo e conectar-se ao hub IoT. O dispositivo envia a `model_id` para que o hub IoT possa identificar o dispositivo como um dispositivo do IoT Plug and Play.

    1. Usa a função `create_reported_properties` no arquivo auxiliar para criar as propriedades. Passe o nome do componente e as propriedades como pares chave-valor para essa função.

    1. Atualiza as propriedades legíveis para seus componentes chamando `patch_twin_reported_properties`.

    1. Inicia a escuta de solicitações de comando usando a função `execute_command_listener`. A função configura um ouvinte para solicitações de comando do serviço. Ao configurar o ouvinte, você fornece um `method_name`, `user_command_handler`e um `create_user_response_handler` opcional como parâmetros.
        - O `method_name` define a solicitação de comando. Neste exemplo, o modelo define os comandos **reboot** e **getMaxMinReport**.
        - A função `user_command_handler` define o que o dispositivo deve fazer quando recebe um comando.
        - A função `create_user_response_handler` cria uma resposta a ser enviada ao hub IoT quando um comando é executado com êxito. Você pode vê-la no portal. Se essa função não for fornecida, uma resposta genérica será enviada ao serviço.

    1. Usa `execute_property_listener` para escutar atualizações de propriedade.

    1. Inicia o envio de telemetria usando `send_telemetry`. O código de exemplo usa um loop para chamar três funções de envio de telemetria. Cada uma é chamada a cada oito segundos

    1. Desabilita todos os ouvintes e tarefas e sai do loop quando você pressiona **Q** ou **q**.

Agora que você já viu o código, crie uma variável de ambiente chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de conexão do dispositivo anotada anteriormente. Use o seguinte comando para executar o exemplo:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

O dispositivo de exemplo envia mensagens de telemetria em intervalos de alguns segundos para o hub IoT.

Você vê a saída a seguir, que indica que o dispositivo está enviando dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/tutorial-multiple-components-python/multiple-component.png)

Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem de versão prévia do IoT Plug and Play](concepts-developer-guide.md)
