---
title: Tutorial para segurança do X.509 no Hub IoT do Azure | Microsoft Docs
description: Introdução à segurança com base em X.509 em seu Hub IoT do Azure em um ambiente simulado.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759643"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Configurar a segurança de X.509 em seu Hub IoT do Azure

Este tutorial mostra os passos necessários para proteger seu hub Azure IoT usando a *Autenticação de Certificado X.509*. Para fins de ilustração, usamos a ferramenta open-source OpenSSL para criar certificados localmente em sua máquina Windows. É recomendável que você use este tutorial apenas para fins de teste. Para o ambiente de produção, você deve adquirir os certificados de uma *autoridade de certificado raiz (AC)*.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige que você tenha os seguintes recursos prontos:

* Você criou um Hub IoT com sua assinatura do Azure. Consulte [Criar um Hub IoT por meio do portal](iot-hub-create-through-portal.md) para obter etapas detalhadas.

* Você tem [o Visual Studio 2017 ou o Visual Studio 2019](https://www.visualstudio.com/vs/) instalados.

## <a name="get-x509-ca-certificates"></a>Obter certificados de AC X.509

A segurança baseada em certificado X.509 no Hub IoT exige que você comece com uma [cadeia de certificados X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado raiz, bem como os certificados intermediários até o certificado de folha.

Você pode escolher qualquer uma das seguintes maneiras de obter seus certificados:

* Adquira certificados X.509 de uma *autoridade de certificado (AC) raiz*. Este método é recomendado para ambientes de produção.

* Crie seus próprios certificados X.509 usando uma ferramenta de terceiros, como [o OpenSSL](https://www.openssl.org/). Esta técnica é boa para fins de teste e desenvolvimento. Veja [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para obter informações sobre como gerar certificados de autoridade de certificação de teste usando o PowerShell ou Bash. O restante deste tutorial usa certificados de autoridade de certificação de teste gerados seguindo as instruções em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Gerar um [certificado CA intermediário X.509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) assinado por um certificado de CA raiz existente e carregá-lo para o hub. Uma vez que o certificado intermediário é carregado e verificado, conforme instruído abaixo, ele pode ser usado no lugar de um certificado de CA raiz mencionado abaixo. Ferramentas como OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) e [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) podem ser usadas para gerar e assinar um certificado de CA intermediário.

> [!NOTE]
> Não carregue a raiz de terceiros se não for exclusiva para você, pois isso permitiria que outros clientes da terceira parte conectassem seus dispositivos ao seu IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrar certificados de AC X.509 para o Hub IoT

Estas etapas mostram como adicionar uma nova Autoridade de certificação ao Hub IoT por meio do portal.

1. No portal Azure, navegue até o seu hub de IoT e selecione **Certificados de** > **Configurações** para o hub.

1. Selecione **Adicionar** para adicionar um novo certificado.

1. Em **Nome do certificado,** digite um nome de exibição amigável e selecione o arquivo de certificado criado na seção anterior do seu computador.

1. Depois de receber uma notificação de que seu certificado foi carregado com sucesso, **selecione Salvar**.

    ![Carregar um certificado](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Seu certificado aparece na lista de certificados com status de **Não Verificado**.

1. Selecione o certificado que você acabou de adicionar para exibir **detalhes do certificado**e, em seguida, selecione **Gerar código de verificação**.

   ![Verificar o certificado](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Copie o **Código de Verificação** para a área de transferência. Você o usa para validar a propriedade do certificado.

1. Siga o Passo 3 no [gerenciamento de certificados ca de teste para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Este processo assina seu código de verificação com a chave privada associada ao seu certificado DE CA X.509, que gera uma assinatura. Há ferramentas disponíveis para executar esse processo de assinatura, por exemplo, o OpenSSL. Este processo é conhecido como [a Prova de Posse.](https://tools.ietf.org/html/rfc5280#section-3.1)

1. Em **Detalhes do Certificado,** em **Certificado de Verificação .pem ou arquivo .cer,** encontre e abra o arquivo de assinatura. Em seguida, **selecione Verificar**.

   O status do seu certificado muda para **Verificado**. Selecione **Atualizar** se o certificado não for atualizado automaticamente.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Criar um dispositivo X.509 para o Hub IoT

1. No portal Azure, navegue até o seu hub de IoT e selecione**dispositivos IoT do** **Explorers** > .

1. Selecione **Novo** para adicionar um novo dispositivo.

1. Em **ID do dispositivo,** digite um nome de exibição amigável. Para **o tipo de autenticação,** escolha **X.509 CA Signed**e, em seguida, **selecione Salvar**.

   ![Criar dispositivo X.509 no portal](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticar o dispositivo X.509 com os certificados X.509

Para autenticar o dispositivo X.509, é necessário assinar primeiro o dispositivo com o certificado de autoridade de certificação. A assinatura de dispositivos de folha normalmente é feita na fábrica, na qual as ferramentas de fabricação foram habilitadas adequadamente. À medida que o dispositivo passa de um fabricante para outro, a ação de assinatura de cada fabricante é capturada como um certificado intermediário dentro da cadeia. O resultado é uma cadeia de certificados do certificado CA para o certificado de folha do dispositivo. A etapa 4 em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um certificado de dispositivo.

Em seguida, mostraremos como criar um aplicativo C# para simular o dispositivo X.509 registrado para o Hub IoT. Enviaremos valores de temperatura e umidade do dispositivo simulado para o hub. Neste tutorial, vamos criar apenas o aplicativo do dispositivo. Isso é tido como um exercício para os leitores criarem o aplicativo de serviço do Hub IoT que enviará a resposta para os eventos enviados por esse dispositivo simulado. O aplicativo C# pressupõe que você seguiu as etapas em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Abra o Visual Studio, selecione **Criar um novo projeto**e escolha o modelo de projeto Console App **(.NET Framework).** Selecione **Avançar**.

1. Em **Configure seu novo projeto,** nomeie o projeto *SimulateX509Device*e selecione **Criar**.

   ![Criar um projeto de dispositivo X.509 no Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. No Solution Explorer, clique com o botão direito do mouse no projeto **SimulateX509Device** e, em seguida, **selecione Gerenciar pacotes NuGet**.

1. No **NuGet Package Manager,** **selecione Procurar** e procurar e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   ![Adicionar o pacote NuGet do SDK de dispositivo no Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Esta etapa baixa, instala e adiciona uma referência ao pacote SDK NuGet do dispositivo Azure IoT e suas dependências.

1. Adicione as `using` seguintes instruções na parte superior do arquivo **Program.cs:**

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Adicione os seguintes campos à classe **Programa:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Use o nome do dispositivo amigável usado na seção anterior no lugar de _<your_device_id>_.

1. Adicione a função a seguir para criar números aleatórios de temperatura e umidade e envie esses valores para o hub:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Por fim, adicione as seguintes linhas de código à função **Principal,** substituindo o _id do dispositivo_de espaços reservados, _seu nome de hub-iot_e _o caminho absoluto para o seu dispositivo-pfx-arquivo,_ conforme exigido pela sua configuração.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Esse código se conecta ao seu Hub IoT ao criar a cadeia de caracteres de conexão para seu dispositivo X.509. Uma vez conectado com êxito, ele, em seguida, envia os eventos de temperatura e umidade para o hub e aguarda sua resposta.

1. Execute o aplicativo. Como este aplicativo acessa um arquivo *.pfx,* você pode precisar executar este aplicativo como administrador.

   1. Compile a solução do Visual Studio.

   1. Abra uma nova janela De solicitação de comando usando **Executar como administrador**.  

   1. Navegue até a pasta que contém sua solução e navegue até o caminho *bin/Debug* dentro da pasta de solução.

   1. Execute o aplicativo **SimulateX509Device.exe** a partir do prompt de comando.

   Você verá o dispositivo se conectar ao hub e enviar os eventos com êxito.

   ![Executar o aplicativo de dispositivo](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como proteger sua solução IoT, confira:

* [Práticas recomendadas de segurança de IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Arquitetura de segurança IoT](../iot-fundamentals/iot-security-architecture.md)

* [Proteger sua implantação de IoT](../iot-fundamentals/iot-security-deployment.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
