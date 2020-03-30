---
title: Comece com o módulo Azure IoT Hub & módulo gêmeo (.NET)
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando SDKs de IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73947675"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Comece com a identidade do módulo IoT Hub e o módulo gêmeo (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo Azure IoT Hub e o dispositivo twin permitem que o aplicativo back-end configure um dispositivo e forneça visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos capazes com vários componentes, como dispositivos baseados em sistemas operacionais ou dispositivos de firmware, as identidades dos módulos e os gêmeos do módulo permitem configurações e condições isoladas para cada componente.

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **Criaridentidades**. Este aplicativo cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de dispositivo e módulo.

* **UpdateModuleTwinReportedProperties**. Este aplicativo envia propriedades atualizadas do módulo twin reported para o seu hub ioT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo .NET

Nesta seção, você criará um aplicativo de console .NET em seu dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

Antes de começar, obtenha a seqüência de conexão do módulo. Faça login no [portal Azure](https://portal.azure.com/). Navegue até o seu hub e selecione **dispositivos IoT**. Encontre **o myFirstDevice**. Selecione **myFirstDevice** para abri-lo e, em seguida, selecione **myFirstModule** para abri-lo. Em **Detalhes de identidade do módulo,** copie a **seqüência de conexões (chave primária)** quando necessário no procedimento a seguir.

   ![Detalhes do módulo do Portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. No Visual Studio, adicione um novo projeto à sua solução selecionando **File** > **New** > **Project**. Em Criar um novo projeto, selecione **Console App (.NET Framework)** e selecione **Next**.

1. Nomeie o projeto *UpdateModuleTwinReportedProperties*. Para **solução,** selecione **Adicionar à solução**. Verifique se a versão do .NET Framework é 4.6.1 ou posterior.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selecione **Criar** para criar seu projeto.

1. No Visual Studio, abra **ferramentas** > **NuGet Package Manager** > **Gerencie pacotes NuGet para solução.** Selecione a guia **Procurar**.

1. Procure e selecione **Microsoft.Azure.Devices.Client**e selecione **Instalar**.

    ![Instale a versão atual do Serviço Azure IoT .NET SDK](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do módulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Adicione o seguinte método **OnDesiredPropertyChanged** à classe de **Programa**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. Adicione as seguintes linhas ao método **Principal:**

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP. Na versão prévia pública, só há suporte a AMQP para operações de módulo gêmeo.

1. Opcionalmente, você pode adicionar essas instruções ao método **Principal** para enviar um evento para o IoT Hub a partir do seu módulo. Coloque estas `try catch` linhas abaixo do bloco.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você pode executar os aplicativos.

1. No Visual Studio, no **Solution Explorer,** clique com o botão direito do mouse na solução e selecione **Set StartUp projetos**.

1. Em **Propriedades Comuns,** selecione **Projeto de Inicialização.**

1. Selecione **Vários projetos de inicialização**e selecione **Iniciar** como ação para os aplicativos e **OK** para aceitar suas alterações.

1. Pressione **F5** para iniciar os aplicativos.

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
