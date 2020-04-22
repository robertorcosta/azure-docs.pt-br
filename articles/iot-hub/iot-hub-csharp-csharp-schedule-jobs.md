---
title: Agendar trabalhos com o Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Você pode usar o SDK do dispositivo IoT do Azure para .NET para implementar os aplicativos do dispositivo simulado e um aplicativo de serviço para executar o trabalho.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 4c71a108d1967027465d127db50737119af3e2c1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733363"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Programação e transmissão de empregos (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Hub IoT do Azure para agendar e controlar os trabalhos que atualizam milhões de dispositivos. Use trabalhos para:

* Atualizar as propriedades desejadas

* Marcas de atualização

* Chamar métodos diretos

Um trabalho encapsula uma dessas ações e controla a execução em um conjunto de dispositivos definido por uma consulta de dispositivo gêmeo. Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reinicie os dispositivos. Você especifica o conjunto de dispositivos com uma consulta de dispositivo gêmeo e agenda o trabalho para execução futura. O trabalho controla o andamento conforme cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada uma dessas capacidades, consulte:

* Dispositivo gêmeo e propriedades: [Introdução aos dispositivos gêmeos](iot-hub-csharp-csharp-twin-getstarted.md) e [Tutorial: como usar as propriedades de dispositivos gêmeos](tutorial-device-twins.md)

* Métodos diretos: [Guia de desenvolvedor do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: Use métodos diretos](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Crie um aplicativo de dispositivo que implemente um método direto chamado **LockDoor**, que pode ser chamado pelo aplicativo back-end.

* Crie um aplicativo back-end que crie um trabalho para chamar o método direto **LockDoor** em vários dispositivos. Outro trabalho envia as atualizações de propriedade desejadas para vários dispositivos.

No fim deste tutorial, você terá dois aplicativos de console .NET (C#):

* **SimularMétodos de dispositivos**. Este aplicativo se conecta ao seu hub de IoT e implementa o método direto **LockDoor.**

* **AgendamentoTrabalho**. Este aplicativo usa trabalhos para chamar o método direto **LockDoor** e atualizar as propriedades desejadas do dispositivo em vários dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console .NET que responde a um método direto chamado pelo back-end da solução.

1. No Visual Studio, selecione **Criar um novo projeto**e, em seguida, escolha o modelo de projeto Console App **(.NET Framework).** Selecione **Avançar** para continuar.

1. Em **Configure seu novo projeto,** nomeie o projeto *SimulateDeviceMethods*e selecione **Criar**.

    ![Configure seu projeto SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. No Solution Explorer, clique com o botão direito do mouse no projeto **SimulaDeviceMethods** e, em seguida, **selecione Gerenciar pacotes NuGet**.

1. No **NuGet Package Manager,** **selecione Procurar** e procurar e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Esta etapa baixa, instala e adiciona uma referência ao pacote SDK NuGet [do dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

1. Adicione as `using` seguintes instruções na parte superior do arquivo **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte código para implementar o método direto no dispositivo:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Adicione o seguinte método para implementar o ouvinte gêmeos do dispositivo no dispositivo:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a conexão para o hub IoT e inicializar o ouvinte do método:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Salve seu trabalho e compile sua solução.

> [!NOTE]
> Para manter as coisas simples, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como repetição de conexão), conforme sugerido no [manuseio de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Agendar trabalhos para chamar um método direto e enviar atualizações ao dispositivo gêmeo

Nesta seção, você cria um aplicativo de console .NET (usando C#) que usa trabalhos para chamar o método direto **LockDoor** e enviar as atualizações de propriedade desejadas para vários dispositivos.

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**. Em **Criar um novo projeto,** escolha Console App **(.NET Framework)** e selecione **Next**.

1. Em **Configurar seu novo projeto,** nomeie o projeto *ScheduleJob*. Para **Solução,** escolha **Adicionar à solução**e, em seguida, selecione **Criar**.

    ![Nomee e configure o projeto AgendaJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. No Solution Explorer, clique com o botão direito do mouse no projeto **ScheduleJob** e, em seguida, **selecione Gerenciar pacotes NuGet**.

1. No **NuGet Package Manager**, **selecione Procurar,** procurar e escolher **Microsoft.Azure.Devices**e, em seguida, **selecione Instalar**.

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as `using` seguintes instruções na parte superior do arquivo **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione a instrução `using` a seguir caso ela não esteja presente nas instruções padrão.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua os espaços reservados pela seqüência de conexões IoT Hub que você copiou anteriormente em [Obter a seqüência de conexão do hub IoT](#get-the-iot-hub-connection-string) e o nome do seu dispositivo.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adicione outro método à classe **Programa**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Para obter mais informações sobre a sintaxe de consulta, consulte [linguagem de consulta de Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    >

1. Por fim, adicione as seguintes linhas ao método **Principal:**

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Salve seu trabalho e compile sua solução.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio Solution Explorer, clique com o botão direito do mouse na solução e selecione **Set StartUp Projects**.

1. Selecione **Common Properties** > **Startup Project**e selecione Vários projetos de **inicialização**.

1. Certifique-se de que `SimulateDeviceMethods` está no topo da lista, seguido por `ScheduleJob`. Defina ambas as ações para **Iniciar** e selecione **OK**.

1. Execute os projetos clicando em **Iniciar** ou vá para o menu **Depurar** e clique em **Iniciar depuração**.

   Você verá a saída do dispositivo e dos aplicativos back-end.

    ![Executar os aplicativos para agendar trabalhos](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

* Para continuar com a introdução ao Hub IoT e aos padrões de gerenciamento de dispositivos como atualização de firmware remota aérea, leia: [Tutorial: como fazer uma atualização de firmware](tutorial-firmware-update.md).

* Para saber mais sobre como implantar IA em dispositivos de borda com o Azure IoT Edge, confira [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
