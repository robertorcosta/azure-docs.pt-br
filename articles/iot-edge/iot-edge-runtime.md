---
title: Saiba como o runtime gerencia dispositivos – Azure IoT Edge | Microsoft Docs
description: Saiba como o tempo de execução do IoT Edge gerencia módulos, segurança, comunicação e relatórios em seus dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 4e4895b227bfc699e94155515e829d0bf33aaf9b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043044"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Reconhecer o runtime do Azure IoT Edge e sua arquitetura

O runtime do IoT Edge é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para serem executados na borda e comuniquem os resultados.

O tempo de execução de IoT Edge é responsável pelas seguintes funções em dispositivos IoT Edge:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Verifique se os [módulos IOT Edge](iot-edge-modules.md) estão sempre em execução.
* Relata a integridade do módulo à nuvem para monitoramento remoto.
* Gerencie a comunicação entre dispositivos downstream e dispositivos IoT Edge.
* Gerenciar a comunicação entre módulos no dispositivo IoT Edge.
* Gerencie a comunicação entre o dispositivo IoT Edge e a nuvem.

![O runtime comunica insights e integridade de módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do runtime do IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. Essas duas funções são executadas por dois componentes que fazem parte do tempo de execução de IoT Edge.O *Hub de IOT Edge* é responsável pela comunicação, enquanto o *agente de IOT Edge* implanta e monitora os módulos.

Tanto o hub do IoT Edge quanto o agente do IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. Às vezes, eles são chamados de *módulos de tempo de execução*.

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub do IoT Edge é um dos dois módulos que compõem o runtime do Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (sejam dispositivos ou módulos) podem se conectar no runtime do IoT Edge como faria para no Hub IoT.

>[!NOTE]
> IoT Edge Hub dá suporte a clientes que se conectam usando MQTT ou AMQP. Ele não oferece suporte a clientes que usam HTTP.

O Hub de IoT Edge não é uma versão completa do Hub IoT em execução localmente. IoT Edge Hub Delega silenciosamente algumas tarefas ao Hub IoT. Por exemplo, o hub do IoT Edge encaminha solicitações de autenticação para o Hub IoT quando um dispositivo tenta se conectar. Depois que a primeira conexão é estabelecida, as informações de segurança são armazenadas em cache localmente pelo hub do IoT Edge. Conexões futuras desse dispositivo são permitidas sem a necessidade de se autenticar na nuvem novamente.

Para reduzir a largura de banda que sua solução de IoT Edge usa, o Hub de IoT Edge otimiza quantas conexões reais são feitas na nuvem. O Hub de IoT Edge usa conexões lógicas de módulos ou dispositivos downstream e os combina para uma única conexão física com a nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes pensam que têm sua própria conexão para a nuvem, mesmo que estejam todos sendo enviados pela mesma conexão.

![O hub do IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/Gateway.png)

O hub do IoT Edge pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, o hub do IoT Edge salvará mensagens ou as atualizações duplicadas localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. O local usado para esse cache temporário é determinado por uma propriedade do módulo de IoT Edge do Hub. O tamanho do cache não tem limite e aumentará até atingir toda a capacidade de armazenamento do dispositivo.Para obter mais informações, consulte [recursos offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação do módulo

O hub do IoT Edge facilita a comunicação de módulo para módulo. O uso do hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Um desenvolvedor de soluções pode reunir essas entradas e saídas para que os módulos processem dados na ordem específica para essa solução.

![O hub do IoT Edge facilita a comunicação de módulo para módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados ao hub do IoT Edge, um módulo chamará o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. O pseudocódigo a seguir envia uma mensagem em **Saída1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. O pseudocódigo a seguir registra a função messageProcessor a ser usada para processar todas as mensagens recebidas em **entrada1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem de SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre os módulos. As regras de roteamento são definidas na nuvem e enviadas por push para IoT Edge Hub em seu módulo. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. Para obter mais informações, confira [Aprenda a implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o runtime do Azure IoT Edge. Ele é responsável por instanciar módulos, fazendo com que continuem a funcionar, e indicar o status dos módulos para o Hub IoT. Esses dados de configuração são gravados como uma propriedade do módulo do agente de IoT Edge.

O [daemon de segurança do IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente recupera seu módulo gêmeo do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados.

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente de IoT Edge para controlar o ciclo de vida do módulo. Estas são algumas das propriedades mais interessantes:

* **Settings.Image**: a imagem de contêiner que o agente do IoT Edge usa para iniciar o módulo. O agente do IoT Edge deverá ser configurado com as credenciais para o registro de contêiner se a imagem estiver protegida por senha. As credenciais para o registro de contêiner pode ser configurado remotamente usando o manifesto de implantação ou no próprio dispositivo IoT Edge atualizando o `config.yaml` arquivo na pasta de programa do IoT Edge.
* **Settings.** – uma cadeia de caracteres que é passada diretamente para o daemon de contêiner Moby ao iniciar o contêiner de um módulo. A adição de opções nessa propriedade permite configurações avançadas como encaminhamento de porta ou montagem de volumes no contêiner de um módulo.  
* **status** – o estado no qual o agente do IoT Edge coloca o módulo. Normalmente, esse valor é definido como *em execução* , pois a maioria das pessoas deseja que o agente de IOT Edge inicie imediatamente todos os módulos no dispositivo. No entanto, você pode especificar o estado inicial de um módulo para ser interrompido e aguardar para mandar o agente do IoT Edge iniciar um módulo.O agente do IoT Edge relata o status de cada módulo para a nuvem nas propriedades relatadas. Uma diferença entre a propriedade desejada e a propriedade relatada é um indicador de um dispositivo com comportamento inadequado. Os status com suporte são:

  * Baixando
  * Executando
  * Unhealthy
  * Failed (Falha)
  * Parado

* **restartPolicy** – como o agente do IoT Edge reinicia um módulo. Os valores possíveis incluem:
  
  * `never` – O agente de IoT Edge nunca reinicia o módulo.
  * `on-failure` -Se o módulo falhar, o agente de IoT Edge o reiniciará. Se o módulo for desligado corretamente, o agente de IoT Edge não o reiniciará.
  * `on-unhealthy` -Se o módulo falhar ou for considerado não íntegro, o agente de IoT Edge o reiniciará.
  * `always` -Se o módulo falhar, for considerado não íntegro ou for desligado de alguma forma, o agente de IoT Edge o reiniciará.

* **imagePullPolicy** -se o agente de IOT Edge tenta extrair a imagem mais recente de um módulo automaticamente ou não. Se você não especificar um valor, o padrão será *OnCreate*. Os valores possíveis incluem:

  * `on-create` -Ao iniciar um módulo ou atualizar um módulo com base em um novo manifesto de implantação, o agente de IoT Edge tentará extrair a imagem do módulo do registro de contêiner.
  * `never` -O agente de IoT Edge nunca tentará extrair a imagem do módulo do registro de contêiner. Com essa configuração, você é responsável por obter a imagem do módulo para o dispositivo e gerenciar quaisquer atualizações de imagem.

O agente do IoT Edge envia a resposta de runtime para o Hub IoT. Aqui está uma lista das possíveis respostas:
  
* 200 - OK
* 400 - A configuração de implantação está malformada ou inválida.
* 417-o dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão do esquema na configuração de implantação é inválida.
* 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
* 500 – ocorreu um erro no runtime do IoT Edge.

Para obter mais informações, confira [Aprenda a implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo.

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](iot-edge-security-manager.md).

## <a name="runtime-quality-telemetry"></a>Telemetria de qualidade de tempo de execução

IoT Edge coleta a telemetria anônima do tempo de execução do host e dos módulos do sistema para melhorar a qualidade do produto. Essas informações são chamadas de telemetria de qualidade de tempo de execução. A telemetria coletada é enviada periodicamente como mensagens do dispositivo para a nuvem para o Hub IoT do agente de IoT Edge. Essas mensagens não aparecem na telemetria regular do cliente e não consomem nenhuma cota de mensagem.

O agente de IoT Edge e o Hub geram métricas que você pode coletar para entender o desempenho do dispositivo. Um subconjunto dessas métricas é coletado pelo agente de IoT Edge como parte da telemetria de qualidade de tempo de execução. As métricas coletadas para telemetria de qualidade de tempo de execução são rotuladas com a marca `ms_telemetry` . Para obter informações sobre todas as métricas disponíveis, consulte [acessar métricas internas](how-to-access-built-in-metrics.md).

Todas as informações pessoais ou organizacionais, como nomes de dispositivos e módulos, são removidas antes do carregamento para garantir a natureza anônima da telemetria de qualidade de tempo de execução.

O agente de IoT Edge coleta a telemetria a cada hora e envia uma mensagem ao Hub IoT a cada 24 horas.

Se você quiser recusar o envio de telemetria de tempo de execução de seus dispositivos, há duas maneiras de fazer isso:

* Defina a `SendRuntimeQualityTelemetry` variável de ambiente para `false` para **edgeAgent**ou
* Desmarque a opção na portal do Azure durante a implantação.

## <a name="next-steps"></a>Próximas etapas

* [Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)
* [Saiba mais sobre IoT Edge métricas de tempo de execução](how-to-access-built-in-metrics.md)