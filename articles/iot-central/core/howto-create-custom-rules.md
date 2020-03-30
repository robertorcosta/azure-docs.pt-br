---
title: Estender o Azure IoT Central com regras e notificações personalizadas | Microsoft Docs
description: Como desenvolvedor de soluções, configure um aplicativo IoT Central para enviar notificações de e-mail quando um dispositivo parar de enviar telemetria. Essa solução usa o Azure Stream Analytics, o Azure Functions e o SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158139"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>ExtendIfique o Azure IoT Central com regras personalizadas usando stream analytics, funções do Azure e SendGrid



Este guia de como fazer mostra, como desenvolvedor de soluções, como um desenvolvedor de soluções, como estender seu aplicativo IoT Central com regras e notificações personalizadas. O exemplo mostra o envio de uma notificação a um operador quando um dispositivo pára de enviar telemetria. A solução usa uma consulta [do Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) para detectar quando um dispositivo parou de enviar telemetria. O trabalho do Stream Analytics usa [funções do Azure](https://docs.microsoft.com/azure/azure-functions/) para enviar e-mails de notificação usando [o SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Este guia de como fazer mostra como estender a Central de IoT além do que ela já pode fazer com as regras e ações incorporadas.

Neste guia de como fazer, você aprende como:

* Transmitir telemetria a partir de um aplicativo IoT Central usando *exportação contínua de dados*.
* Crie uma consulta do Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por e-mail usando os serviços Azure Functions e SendGrid.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

### <a name="iot-central-application"></a>Aplicativo IoT Central

Crie um aplicativo IoT Central no site do [gerenciador de aplicativos Central do Azure IoT](https://aka.ms/iotcentral) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Plano de preços | Standard |
| Modelo de aplicativo | Análise na loja – monitoramento de condições |
| Nome do aplicativo | Aceite o padrão ou escolha seu próprio nome |
| URL | Aceite o padrão ou escolha seu próprio prefixo de URL exclusivo |
| Diretório | Seu inquilino do Diretório Ativo do Azure |
| Assinatura do Azure | Sua assinatura do Azure |
| Região | Sua região mais próxima |

Os exemplos e capturas de tela deste artigo usam a região **dos Estados Unidos.** Escolha um local próximo a você e certifique-se de criar todos os seus recursos na mesma região.

Este modelo de aplicativo inclui dois dispositivos de termostato simulados que enviam telemetria.

### <a name="resource-group"></a>Resource group

Use o [portal Azure para criar um grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) chamado **DetectStoppedDevices** para conter os outros recursos que você cria. Crie seus recursos do Azure no mesmo local que seu aplicativo IoT Central.

### <a name="event-hubs-namespace"></a>Namespace do Hubs de Eventos

Use o [portal Azure para criar um namespace do Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha seu nome de namespace |
| Tipo de preço | Basic |
| Subscription | Sua assinatura |
| Resource group | Detectadispositivos paradas |
| Location | Leste dos EUA |
| Unidades de produtividade | 1 |

### <a name="stream-analytics-job"></a>Trabalho do Stream Analytics

Use o [portal Azure para criar um trabalho de Stream Analytics](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha seu nome de trabalho |
| Subscription | Sua assinatura |
| Resource group | Detectadispositivos paradas |
| Location | Leste dos EUA |
| Ambiente de hospedagem | Nuvem |
| Unidades de transmissão | 3 |

### <a name="function-app"></a>Aplicativo de funções

Use o [portal Azure para criar um aplicativo de função](https://portal.azure.com/#create/Microsoft.FunctionApp) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome do aplicativo    | Escolha o nome do aplicativo da função |
| Subscription | Sua assinatura |
| Resource group | Detectadispositivos paradas |
| Sistema operacional | Windows |
| Plano de Hospedagem | Plano de consumo |
| Location | Leste dos EUA |
| Pilha de runtime | .NET |
| Armazenamento | Criar Novo |

### <a name="sendgrid-account"></a>Conta sendgrid

Use o [portal Azure para criar uma conta sendgrid](https://portal.azure.com/#create/Sendgrid.sendgrid) com as seguintes configurações:

| Configuração | Valor |
| ------- | ----- |
| Nome    | Escolha o nome da sua conta do SendGrid |
| Senha | Crie uma senha |
| Subscription | Sua assinatura |
| Resource group | Detectadispositivos paradas |
| Tipo de preço | F1 Gratuito |
| Informações de contato | Preencher as informações necessárias |

Quando você criou todos os recursos necessários, o grupo de recursos **DetectStoppedDevices** parece a seguinte captura de tela:

![Detectar o grupo de recursos de dispositivos parados](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Você pode configurar um aplicativo IoT Central para exportar continuamente a telemetria para um hub de eventos. Nesta seção, você cria um hub de eventos para receber telemetria a partir do seu aplicativo IoT Central. O hub de eventos oferece a telemetria ao seu trabalho de Stream Analytics para processamento.

1. No portal Azure, navegue até o namespace do Event Hubs e selecione **+ Event Hub**.
1. Nomeie o **centro de eventos centralexportar**e selecione **Criar**.

O namespace do Event Hubs parece a seguinte captura de tela:

![Namespace do Hubs de Eventos](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Obtenha a chave da API sendgrid

Seu aplicativo de função precisa de uma chave de API sendgrid para enviar mensagens de e-mail. Para criar uma chave DePI sendgrid:

1. No portal Azure, navegue até sua conta sendgrid. Em seguida, escolha **Gerenciar** para acessar sua conta SendGrid.
1. Em sua conta SendGrid, escolha **Configurações**e, em seguida, **chaves de API**. Escolha **criar a chave de API:**

    ![Criar a chave da API sendgrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Na página **Criar a aPI Key,** crie uma chave chamada **AzureFunctionAccess** com permissões **de acesso total.**
1. Faça uma anotação da chave da API, você precisa dela quando configurar seu aplicativo de função.

## <a name="define-the-function"></a>Defina a função

Essa solução usa um aplicativo Azure Functions para enviar uma notificação por e-mail quando o trabalho do Stream Analytics detecta um dispositivo parado. Para criar seu aplicativo de função:

1. No portal Azure, navegue até a instância **do Serviço de Aplicativos** no grupo de recursos **DetectStoppedDevices.**
1. Selecione **+** para criar uma nova função.
1. Na página **ESCOLHA UM AMBIENTE DE DESENVOLVIMENTO,** escolha **In-portal** e, em seguida, selecione **Continuar**.
1. Na página **CRIAR UMA FUNÇÃO,** escolha **Webhook + API** e, em seguida, selecione **Criar**.

O portal cria uma função padrão chamada **HttpTrigger1**:

![Função de gatilho HTTP padrão](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurar vinculações de função

Para enviar e-mails com o SendGrid, você precisa configurar as vinculações para sua função da seguinte forma:

1. Selecione **Integrar,** escolha a saída **HTTP ($return)** e, em seguida, **selecione excluir**.
1. Escolha **+ Nova saída,** depois escolha **SendGrid**e escolha **Selecionar**. Escolha **Instalar** para instalar a extensão SendGrid.
1. Quando a instalação for concluída, selecione **Usar o valor de retorno da função**. Adicione um endereço válido **para** receber notificações por e-mail.  Adicione um **endereço de partir** válido para usar como remetente de e-mail.
1. Selecione **novo** ao lado da **configuração do aplicativo de chave da API SendGrid**. Digite **SendGridAPIKey** como a chave e a tecla SendGrid API que você observou anteriormente como o valor. Em seguida, **selecione Criar**.
1. Escolha **Salvar** para salvar as vinculações sendgrid para sua função.

As configurações de integração parecem a seguinte captura de tela:

![Integrações de aplicativos de função](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Adicione o código de função

Para implementar sua função, adicione o código C# para analisar a solicitação HTTP recebida e enviar os e-mails da seguinte forma:

1. Escolha a função **HttpTrigger1** no seu aplicativo de função e substitua o código C# pelo seguinte código:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Você pode ver uma mensagem de erro até salvar o novo código.

1. Selecione **Salvar** para salvar a função.

### <a name="test-the-function-works"></a>Testar o funcionamento da função

Para testar a função no portal, primeiro escolha **Logs** na parte inferior do editor de código. Em seguida, escolha **Testar** à direita do editor de código. Use o Seguinte JSON como **órgão de solicitação:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

As mensagens de registro de função aparecem no painel **Logs:**

![Saída de log de função](media/howto-create-custom-rules/function-app-logs.png)

Após alguns minutos, o endereço **para** e-mail recebe um e-mail com o seguinte conteúdo:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Adicionar consulta do Stream Analytics

Esta solução usa uma consulta do Stream Analytics para detectar quando um dispositivo pára de enviar telemetria por mais de 120 segundos. A consulta usa a telemetria do hub de eventos como sua entrada. O trabalho envia os resultados da consulta para o aplicativo de função. Nesta seção, você configura o trabalho do Stream Analytics:

1. No portal Azure, navegue até o trabalho do Stream Analytics, em **Jobs topology** selecione **Entradas,** escolha **+ Adicione entrada de fluxo**e escolha Event **Hub**.
1. Use as informações na tabela a seguir para configurar a entrada usando o hub de eventos que você criou anteriormente e, em seguida, escolha **Salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Alias de entrada | centralemetria |
    | Subscription | Sua assinatura |
    | Namespace do Hub de Eventos | Seu espaço de nome do Event Hub |
    | Nome do Hub de Eventos | Use o existente - **centralexporte** |

1. Em **'Trabalho de topologia',** **selecione Saídas,** escolha **+ Adicione**e escolha a função **Azure**.
1. Use as informações na tabela a seguir para configurar a saída e, em seguida, escolha **Salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Alias de saída | notificação por e-mail |
    | Subscription | Sua assinatura |
    | Aplicativo de funções | Seu aplicativo de função |
    | Função  | HttpTrigger1 |

1. Em **Topologia de Trabalhos,** **selecione Consulta** e substitua a consulta existente pelo seguinte SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selecione **Salvar**.
1. Para iniciar o trabalho do Stream Analytics, escolha **Visão geral,** depois **Inicie,** depois **Agora**e **Comece:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configure a exportação na IoT Central

No site do gerenciador de [aplicativos Azure IoT Central,](https://aka.ms/iotcentral) navegue até o aplicativo IoT Central que você criou a partir do modelo Contoso. Nesta seção, você configura o aplicativo para transmitir a telemetria de seus dispositivos simulados para o seu hub de eventos. Para configurar a exportação:

1. Navegue até a página **De Exportação de Dados,** selecione **+ Novo**e, em seguida, **Azure Event Hubs**.
1. Use as seguintes configurações para configurar a exportação e, em seguida, **selecione Salvar**:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome de exibição | Exportar para hubs de eventos |
    | habilitado | Por |
    | Namespace do Hubs de Eventos | Seu nome de namespace do Event Hubs |
    | Hub de Eventos | centralexportação |
    | Medidas | Por |
    | Dispositivos | Desativado |
    | Modelos de Dispositivo | Desativado |

![Configuração contínua de exportação de dados](media/howto-create-custom-rules/cde-configuration.png)

Aguarde até que o status de exportação esteja **em execução** antes de continuar.

## <a name="test"></a>Teste

Para testar a solução, você pode desativar a exportação contínua de dados da IoT Central para dispositivos parados simulados:

1. Em seu aplicativo IoT Central, navegue até a página **de exportação de dados** e selecione a configuração de exportação **Exportto Event Hubs.**
1. Defina **Ativado** para **desligar** e escolher **Salvar**.
1. Após pelo menos dois minutos, o endereço **para** e-mail recebe um ou mais e-mails que se parecem com o seguinte exemplo de conteúdo:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Limpar

Para arrumar o que fazer e evitar custos desnecessários, exclua o grupo de recursos **DetectStoppedDevices** no portal Azure.

Você pode excluir o aplicativo IoT Central da página **Gerenciamento** dentro do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu como:

* Transmitir telemetria a partir de um aplicativo IoT Central usando *exportação contínua de dados*.
* Crie uma consulta do Stream Analytics que detecta quando um dispositivo parou de enviar dados.
* Envie uma notificação por e-mail usando os serviços Azure Functions e SendGrid.

Agora que você sabe como criar regras e notificações personalizadas, o próximo passo sugerido é aprender como estender o [Azure IoT Central com análises personalizadas](howto-create-custom-analytics.md).
