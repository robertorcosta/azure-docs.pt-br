---
title: Saiba como o runtime gerencia dispositivos – Azure IoT Edge | Microsoft Docs
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456601"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Reconhecer o runtime do Azure IoT Edge e sua arquitetura

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* Relata a integridade do módulo à nuvem para monitoramento remoto.
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![O runtime comunica insights e integridade de módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do runtime do IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

Tanto o hub do IoT Edge quanto o agente do IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub do IoT Edge é um dos dois módulos que compõem o runtime do Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (sejam dispositivos ou módulos) podem se conectar no runtime do IoT Edge como faria para no Hub IoT. 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. Ele não oferece suporte a clientes que usam HTTP. 

O hub do IoT Edge não é uma versão completa do Hub IoT executado localmente. Há algumas coisas que o hub do IoT Edge delega para o Hub IoT silenciosamente. Por exemplo, o hub do IoT Edge encaminha solicitações de autenticação para o Hub IoT quando um dispositivo tenta se conectar. Depois que a primeira conexão é estabelecida, as informações de segurança são armazenadas em cache localmente pelo hub do IoT Edge. São permitidas conexões subsequentes desse dispositivo sem ele precisar se autenticar na nuvem. 

Para reduzir a largura de banda que a solução IoT Edge usa, o hub do IoT Edge otimiza quantas conexões reais são feitas com a nuvem. IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. Os detalhes desse processo são transparentes para o restante da solução. Os clientes pensam que têm sua própria conexão para a nuvem, mesmo que estejam todos sendo enviados pela mesma conexão. 

![O hub do IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/Gateway.png)

O hub do IoT Edge pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, o hub do IoT Edge salvará mensagens ou as atualizações duplicadas localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. A localização usada para esse cache temporário é determinado por uma propriedade do gêmeo do módulo do hub do IoT Edge. O tamanho do cache não tem limite e aumentará até atingir toda a capacidade de armazenamento do dispositivo. For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação do módulo

O hub do IoT Edge facilita a comunicação de módulo para módulo. O uso do hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![O hub do IoT Edge facilita a comunicação de módulo para módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados ao hub do IoT Edge, um módulo chamará o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre os módulos. Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o runtime do Azure IoT Edge. Ele é responsável por instanciar módulos, fazendo com que continuem a funcionar, e indicar o status dos módulos para o Hub IoT. This configuration data is written as a property of the IoT Edge agent module twin. 

O [daemon de segurança do IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente recupera seu módulo gêmeo do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados. 

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente o IoT Edge para controlar o ciclo de vida do módulo. Estas são algumas das propriedades mais interessantes: 

* **Settings.Image**: a imagem de contêiner que o agente do IoT Edge usa para iniciar o módulo. O agente do IoT Edge deverá ser configurado com as credenciais para o registro de contêiner se a imagem estiver protegida por senha. As credenciais para o registro de contêiner pode ser configurado remotamente usando o manifesto de implantação ou no próprio dispositivo IoT Edge atualizando o `config.yaml` arquivo na pasta de programa do IoT Edge.
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* **status** – o estado no qual o agente do IoT Edge coloca o módulo. Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. No entanto, você pode especificar o estado inicial de um módulo para ser interrompido e aguardar para mandar o agente do IoT Edge iniciar um módulo. The IoT Edge agent reports the status of each module back to the cloud in the reported properties. Uma diferença entre a propriedade desejada e a propriedade relatada é um indicador de um dispositivo com comportamento inadequado. Os status com suporte são:
   * Baixando
   * Executando
   * Não Íntegro
   * Com falha
   * Parado
* **restartPolicy** – como o agente do IoT Edge reinicia um módulo. Os valores possíveis incluem:
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. Se o módulo é desligado corretamente, o agente do IoT Edge não o reinicia.
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. Os valores possíveis incluem: 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

O agente do IoT Edge envia a resposta de runtime para o Hub IoT. Aqui está uma lista das possíveis respostas:
  * 200 - OK
  * 400 - A configuração de implantação está malformada ou inválida.
  * 417 - The device doesn't have a deployment configuration set.
  * 412 - A versão do esquema na configuração de implantação é inválida.
  * 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
  * 500 – ocorreu um erro no runtime do IoT Edge.

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo. 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Próximos passos

[Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)
