---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use o SDK do dispositivo IoT do Azure para .NET para implementar o aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733125"
---
# <a name="get-started-with-device-twins-net"></a>Introdução ao dispositivo gêmeos (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, você criará esses aplicativos de console .NET:

* **CreateDeviceIdentity**. Este aplicativo cria uma identidade de dispositivo e uma chave de segurança associada para conectar o aplicativo de dispositivo simulado.

* **AddTagsAndQuery**. Este aplicativo de back-end adiciona marcas e consultas dispositivo gêmeos.

* **ReportConnectivity**. Este aplicativo de dispositivo simula um dispositivo que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs de IoT do Azure](iot-hub-devguide-sdks.md) apresenta informações sobre os SDKs de IoT do Azure que você pode usar para criar dispositivos e aplicativos de back-end.
>

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo de console do .NET, usando C#, que adiciona metadados de local ao dispositivo "c **".** Ele então consulta os dispositivos gêmeos armazenados no Hub IoT selecionando os dispositivos localizados nos EUA e depois aqueles que relataram a existência de uma conexão celular.

1. No Visual Studio, selecione **Criar um projeto**. Em **criar novo projeto**, selecione **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o projeto **AddTagsAndQuery**.

    ![Configurar seu projeto do AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AddTagsAndQuery** e selecione **gerenciar pacotes NuGet**.

1. Selecione **procurar** e pesquise e selecione **Microsoft. Azure. Devices**. Selecione **Instalar**.

    ![Janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as seguintes `using` instruções na parte superior do arquivo **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua `{iot hub connection string}` pela cadeia de conexão do Hub IOT que você copiou em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    A classe **RegistryManager** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código anterior inicializa primeiro o objeto **registryManager**, depois recupera o dispositivo gêmeo para **myDeviceId** e, finalmente, atualiza as marcações com as informações do local desejado.

    Após a atualização, ele executa duas consultas: a primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, enquanto o segundo aperfeiçoa a consulta para selecionar somente os dispositivos que também estão conectados por meio de rede celular.

    O código anterior, quando ele cria o objeto **query**, especifica um número máximo de documentos retornados. O objeto **query** contém uma propriedade booliana **HasMoreResults** que você pode usar para invocar os métodos **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para resultados que não são dispositivos gêmeos, por exemplo, resultados de consultas de agregação.

1. Por fim, adicione as seguintes linhas ao método **Main** :

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Execute esse aplicativo clicando com o botão direito do mouse no projeto **AddTagsAndQuery** e selecionando **Depurar**, seguido de **Iniciar nova instância**. Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.

    ![Resultados da consulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do .NET que se conecta ao seu hub como **myDeviceId** e depois atualiza suas propriedades reportadas para conter as informações indicando que ele está conectado usando uma rede celular.

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**. Em **criar novo projeto**, escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o projeto **ReportConnectivity**. Para **solução**, escolha **Adicionar à solução**e, em seguida, selecione **criar**.

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReportConnectivity** e selecione **gerenciar pacotes NuGet**.

1. Selecione **procurar** e pesquise e escolha **Microsoft. Azure. Devices. Client**. Selecione **Instalar**.

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

1. Adicione as seguintes `using` instruções na parte superior do arquivo **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua `{device connection string}` pela cadeia de conexão do dispositivo que você anotou em [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código mostrado acima Inicializa o objeto de **cliente** e, em seguida, recupera o **dispositivo.**

1. Adicione o seguinte método à classe **Programa**:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   O código acima atualiza a propriedade relatada de **DeviceID** com as informações de conectividade.

1. Por fim, adicione as seguintes linhas ao método **Main** :

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. No Gerenciador de Soluções, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. No > **projeto de inicialização**de **Propriedades comuns**, selecione **vários projetos de inicialização**. Para **ReportConnectivity**, selecione **Iniciar** como a **ação**. Selecione **OK** para salvar suas alterações.  

1. Execute este aplicativo clicando com o botão direito do mouse no projeto **ReportConnectivity** e selecionando **depurar**e, em seguida, **Iniciar nova instância**. Você deve ver o aplicativo obtendo as informações de conexão e, em seguida, enviando a conectividade como uma ***Propriedade relatada***.

    ![Execute o aplicativo de dispositivo para conectividade de relatório](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Depois que o dispositivo reportou suas informações de conectividade, ele deve aparecer em ambas as consultas.

1. Clique com o botão direito do mouse no projeto **AddTagsAndQuery** e selecione **depurar** > **Iniciar nova instância** para executar as consultas novamente. Desta vez, **Mydeviceid** deve aparecer nos dois resultados da consulta.

    ![Conectividade do dispositivo relatada com êxito](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando a linguagem de consulta do Hub IoT semelhante ao SQL.

Você pode aprender mais com os seguintes recursos:

* Para saber como enviar telemetria de dispositivos, consulte o tutorial [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-dotnet.md) .

* Para saber como configurar dispositivos usando as propriedades desejadas do dispositivo., consulte o tutorial [usar as propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) .

* Para saber como controlar dispositivos interativamente, como ativar um ventilador de um aplicativo controlado pelo usuário, consulte o tutorial [usar métodos diretos](quickstart-control-device-dotnet.md) .
