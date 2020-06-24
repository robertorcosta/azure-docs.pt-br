---
title: Configurar pontos de extremidade da Web (versão prévia)
titleSuffix: Azure Cognitive Services
description: configurar pontos de extremidade da Web para comandos personalizados
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5bdb77d27b01f576ca06aa5b6d3df0572b3b1ea6
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307388"
---
# <a name="set-up-web-endpoints"></a>Configurar pontos de extremidade da Web

Neste artigo, você aprende a configurar pontos de extremidade da Web em um aplicativo de comandos personalizados que permite fazer solicitações HTTP de um aplicativo cliente. Você conclui as seguintes tarefas:

- Configurar pontos de extremidade da Web no aplicativo de comandos personalizados
- Chamar pontos de extremidade da Web no aplicativo de comandos personalizados
- Receber a resposta de pontos de extremidade da Web 
- Integrar a resposta de pontos de extremidade da Web em um conteúdo JSON personalizado, enviá-lo e visualizá-lo de um aplicativo cliente do SDK de fala do UWP C#

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Uma chave de assinatura do Azure para o serviço de fala: [obtenha uma gratuitamente](get-started.md) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Um [aplicativo de comandos personalizados criado](quickstart-custom-commands-application.md) anteriormente
> * Um aplicativo cliente habilitado para SDK de fala: [como encerrar a atividade para o aplicativo cliente](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Pontos de extremidade da Web de instalação

1. Abra o aplicativo de comandos personalizados que você criou anteriormente. 
1. Vá para "pontos de extremidade da Web", clique em "novo ponto de extremidades da Web".

   > [!div class="mx-imgBorder"]
   > ![Novo ponto de extremidade da Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Nome para o ponto de extremidade da Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | A URL do ponto de extremidade ao qual você deseja que seu aplicativo de comando personalizado se comunique. |
   | Método | POST | As interações permitidas (como GET, POST) com o ponto de extremidade.|
   | Cabeçalhos | Chave: aplicativo, valor: um nome exclusivo para seu aplicativo | Os parâmetros de cabeçalho a serem incluídos no cabeçalho da solicitação.|

    > [!NOTE]
    > - O ponto de extremidade da Web de exemplo criado usando o [Azure function](https://docs.microsoft.com/azure/azure-functions/), que se conecta ao banco de dados que salva o estado do dispositivo da TV e do ventilador
    > - O cabeçalho sugerido só é necessário para o ponto de extremidade de exemplo
    > - No mundo real, o ponto de extremidade da Web pode ser o ponto de extremidade para o [Hub IOT](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) que gerencia seus dispositivos

1. Clique em **Save** (Salvar).

## <a name="call-web-endpoints"></a>Chamar pontos de extremidade da Web

1. Vá para o comando **TurnOnOff** , selecione **ConfirmationResponse** em regra de conclusão e, em seguida, selecione **Adicionar uma ação**.
1. Em **novo tipo de ação**, selecione **chamar ponto de extremidade da Web**
1. Em **Editar ação-pontos**de extremidade, selecione **UpdateDeviceState**, que é o ponto de extremidade da Web que criamos.  
1. Em **configuração**, coloque os seguintes valores: 
   > [!div class="mx-imgBorder"]
   > ![Chamar parâmetros de ação de pontos de extremidade da Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Pontos de extremidade | UpdateDeviceState | O ponto de extremidade da Web que você deseja chamar nesta ação. |
   | Parâmetros de consulta | item = {SubjectDevice} &&valor = {OnOff} | Os parâmetros de consulta a serem acrescentados à URL do ponto de extremidade da Web.  |
   | Conteúdo do corpo | N/D | O conteúdo do corpo da solicitação. |

    > [!NOTE]
    > - Os parâmetros de consulta sugeridos só são necessários para o ponto de extremidade de exemplo

1. Em **em ação de sucesso a ser executada**, selecione **Enviar resposta de fala**.
   
   > [!div class="mx-imgBorder"]
   > ![Chamar ação de pontos de extremidade da Web em caso de êxito](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a ser executada | Enviar resposta de fala | Ação a ser executada se a solicitação para o ponto de extremidade da Web for realizada com sucesso |
   
   > [!NOTE]
   > - Você também pode acessar diretamente os campos na resposta HTTP usando `{YourWebEndpointName.FieldName}` . Por exemplo: `{UpdateDeviceState.TV}`

1. Em **em caso de falha-ação a ser executada**, selecione **Enviar resposta de fala**
   > [!div class="mx-imgBorder"]
   > ![Chamar ação de pontos de extremidade da Web em caso de falha](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Ação a ser executada | Enviar resposta de fala | Ação a ser executada se a solicitação para o ponto de extremidade da Web falhar |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` é opcional. Você está livre para removê-lo se não quiser expor nenhuma mensagem de erro.
   > - Em nosso ponto de extremidade de exemplo, enviamos uma resposta http novamente com mensagens de erro detalhadas para erros comuns, como parâmetros de cabeçalho ausentes. 

### <a name="try-it-out-in-test-portal"></a>Experimente no portal de teste
- Em resposta de êxito \
Salvar, treinar e testar
   > [!div class="mx-imgBorder"]
   > ![Chamar ação de pontos de extremidade da Web em caso de êxito](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Em resposta de falha \
Remover um dos parâmetros de consulta, salvar, treinar novamente e testar
   > [!div class="mx-imgBorder"]
   > ![Chamar ação de pontos de extremidade da Web em caso de êxito](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrar com o aplicativo cliente

Em [como: enviar atividade para o aplicativo cliente (visualização)](./how-to-custom-commands-send-activity-to-client.md), você adicionou uma **atividade de envio à** ação do cliente. A atividade é enviada ao aplicativo cliente se a ação de **ponto de extremidade da Web de chamada** for bem-sucedida ou não.
No entanto, na maioria dos casos, você só deseja enviar atividade ao aplicativo cliente quando a chamada para o ponto de extremidade da Web é bem-sucedida. Neste exemplo, é quando o estado do dispositivo é atualizado com êxito.

1. Exclua a **atividade enviar para a** ação do cliente que você adicionou anteriormente.
1. Editar ponto de extremidade da Web de chamada: 
    1. Em **configuração**, verifique se os **parâmetros de consulta** são`item={SubjectDevice}&&value={OnOff}`
    1. Em caso de **êxito**, alterar **ação a ser executada** para **Enviar atividade ao cliente**
    1. Copie o JSON abaixo para o **conteúdo da atividade**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Atividade de envio em caso de êxito](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Agora você só envia atividade ao cliente quando a solicitação para o ponto de extremidade da Web é bem-sucedida.

### <a name="create-visuals-for-syncing-device-state"></a>Criar visuais para sincronizar o estado do dispositivo
Adicione o XML a seguir ao `MainPage.xaml` `"EnableMicrophoneButton"` bloco acima.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizar estado do dispositivo 

No `MainPage.xaml.cs` , adicione a referência `using Windows.Web.Http;` . Adicione o código a seguir à classe `MainPage` . Esse método enviará uma solicitação GET para o ponto de extremidade de exemplo e extrairá o estado atual do dispositivo para seu aplicativo. Certifique-se de alterar `<your_app_name>` para o que você usou no **cabeçalho** no ponto de extremidade da Web do comando personalizado

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Experimentar

1. Iniciar o aplicativo
1. Clique em sincronizar estado do dispositivo. \
Se você testou o aplicativo `turn on tv` na seção anterior, verá que a TV aparece como "ativada".
    > [!div class="mx-imgBorder"]
    > ![Sincronizar estado do dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selecione habilitar microfone
1. Selecione o botão falar
1. Mencione`turn on the fan`
1. O estado visual do ventilador deve mudar para "ligado"
    > [!div class="mx-imgBorder"]
    > ![Ativar ventilador](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar um processo de CI/CD para seu aplicativo de comandos personalizados](./how-to-custom-commands-deploy-cicd.md)