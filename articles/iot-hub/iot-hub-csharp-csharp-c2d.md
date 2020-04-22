---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (.NET)| Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um Hub IoT do Azure usando os SDKs do IoT do Azure para .NET. Você modifica um aplicativo de dispositivo para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 41c29e55f04f9edf06ba375ad4539e5fb3f82c18
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733427"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Enviar mensagens de nuvem para seu dispositivo com o Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. A [telemetria Send de um dispositivo para um hub de IoT](quickstart-send-telemetry-dotnet.md) mostra como criar um hub de IoT, provisionar uma identidade de dispositivo nele e codificar um aplicativo de dispositivo que envia mensagens dispositivo-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia em [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-dotnet.md). Ele mostra como fazer as seguintes tarefas:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* A partir do back-end da solução, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas a um dispositivo do IoT Hub.

É possível encontrar mais informações sobre mensagens de nuvem para dispositivo em [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executa dois aplicativos de console .NET.

* **Dispositivo simulado**. Este aplicativo se conecta ao seu hub de IoT e recebe mensagens nuvem-dispositivo. Este aplicativo é uma versão modificada do aplicativo criado na [telemetria Send de um dispositivo para um hub ioT](quickstart-send-telemetry-dotnet.md).

* **SendCloudToDevice**. Este aplicativo envia uma mensagem nuvem-para-dispositivo para o aplicativo do dispositivo através do IoT Hub e, em seguida, recebe seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte a SDK para muitas plataformas e idiomas de dispositivos, incluindo C, Java, Python e Javascript, através [de SDKs de dispositivos IoT Azure.](iot-hub-devguide-sdks.md) Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens no aplicativo do dispositivo

Nesta seção, modifique o aplicativo do dispositivo criado em [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-dotnet.md) para receber mensagens nuvem-dispositivo do hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice**, adicione o método a seguir à classe **Program**.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:

   ```csharp
   ReceiveC2dAsync();
   ```

O método `ReceiveAsync` retorna de forma assíncrona a mensagem recebida no momento em que ela é recebida pelo dispositivo. Ele retorna *nulo* após um período de tempo especificável. Neste exemplo, o padrão de um minuto é usado. Quando o aplicativo recebe *null*, ele deve continuar aguardando novas mensagens. Esse requisito é o motivo da linha `if (receivedMessage == null) continue`.

A chamada para `CompleteAsync()` notifica o Hub IoT de que a mensagem foi processada com êxito. A mensagem pode ser removida da fila do dispositivo com segurança. Se ocorreu algo que impediu que o aplicativo do dispositivo concluísse o processamento da mensagem, o Hub IoT a entrega novamente. A lógica de processamento de mensagens no aplicativo do dispositivo deve ser *impotente,* de modo que receber a mesma mensagem várias vezes produza o mesmo resultado.

Um aplicativo também pode abandonar temporariamente uma mensagem, o que resulta em um Hub IoT reter a mensagem na fila para consumo futuro. Ou o aplicativo pode rejeitar uma mensagem, o que a remove permanentemente da fila. Para obter mais informações sobre o ciclo de vida da mensagem de nuvem para dispositivo, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).

   > [!NOTE]
   > Ao usar HTTPS em vez de MQTT ou AMQP como transporte, o método `ReceiveAsync` é retornado imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTPS são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Emitir mais recebimentos de HTTPS resulta na limitação das solicitações pelo Hub IoT. Para obter mais informações sobre as diferenças entre o suporte de MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens nuvem-dispositivo através do hub ioT que você criou no [Enviar telemetria de um dispositivo para um hub ioT](quickstart-send-telemetry-dotnet.md). Para enviar mensagens em nuvem para dispositivo, seu serviço precisa da permissão de conexão de **serviço.** Por padrão, todo IoT Hub é criado com uma política de acesso compartilhado chamada **service** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Agora você escreve um aplicativo de console .NET que envia mensagens de nuvem para dispositivo para o aplicativo do dispositivo.

1. Na solução atual do Visual Studio, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** selecione Console App **(.NET Framework)** e selecione **Next**.

1. Nomeie o projeto *SendCloudToDevice*. Em **Solução,** **selecione Adicionar à solução** e aceitar a versão mais recente do Quadro .NET. Selecione **Criar** para criar o cluster.

   ![Configure um novo projeto no Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. No Solution Explorer, clique com o botão direito do mouse na nova solução e selecione **Gerenciar pacotes NuGet**.

1. Em **Gerenciar pacotes NuGet,** selecione **Procurar**e, em seguida, procure e selecione **Microsoft.Azure.Devices**. Selecione **Instalar**.

   Esta etapa baixa, instala e adiciona uma referência ao [pacote SDK NuGet do serviço Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

1. Adicione a `using` seguinte declaração na parte superior do arquivo **Program.cs.**

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela seqüência de conexão de hub IoT que você copiou anteriormente em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string).

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. Adicione o seguinte método à classe **Programa**. Defina o nome do dispositivo para o que você usou ao definir o dispositivo em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Esse método envia uma nova mensagem da nuvem para o dispositivo ao dispositivo com a ID `myFirstDevice`. Altere este parâmetro somente se você modificou-o do usado na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md).

1. Por fim, adicione as seguintes linhas ao método **Principal.**

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. No Solutions Explorer, clique com o botão direito do mouse na solução e selecione **Set StartUp Projects**.

1. No **Common Properties** > **Startup Project**, selecione Vários projetos **de inicialização,** selecione a ação **Iniciar** para **ReadDeviceToCloudMessages,** **SimulatedDevice**e **SendCloudToDevice**. Selecione **OK** para salvar suas alterações.

1. Pressione **F5**. Todos os três aplicativos devem ser iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem que está sendo recebida pelo aplicativo do dispositivo.

   ![Aplicativo recebendo mensagens](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Receber comentários de entrega

É possível solicitar reconhecimentos de entrega (ou expiração) do IoT Hub para cada mensagem nuvem-dispositivo. Essa opção permite que o back-end da solução informe a lógica de repetição ou de compensação facilmente. Para obter mais informações sobre comentários da nuvem para o dispositivo, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).

Nesta seção, você modifica o aplicativo **SendCloudToDevice** para solicitar comentários e os recebe do Hub IoT.

1. No Visual Studio, no projeto **SendCloudToDevice**, adicione o método a seguir à classe **Program**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    É importante lembrar que o padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

1. Adicione a seguinte linha no método `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` **Principal,** logo após .

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Para solicitar comentários sobre a entrega da mensagem da nuvem para o dispositivo, você deve especificar uma propriedade no método **SendCloudToDeviceMessageAsync** . Adicione a seguinte linha, `var commandMessage = new Message(...);` logo após a linha.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Execute os aplicativos pressionando **F5**. Você deve ver todos os três aplicativos serem iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem sendo recebida pelo aplicativo do dispositivo e, depois de alguns segundos, a mensagem de comentários sendo recebida pelo aplicativo **SendCloudToDevice**.

   ![Aplicativo recebendo mensagens](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como recuo exponencial, como sugerido no [manuseio de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a enviar e receber mensagens de nuvem para dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvedores do [IoT Hub](iot-hub-devguide.md).
