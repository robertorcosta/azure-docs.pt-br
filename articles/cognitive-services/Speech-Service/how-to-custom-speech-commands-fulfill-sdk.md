---
title: Como atender a comandos de um cliente com o SDK de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como lidar com atividades de comandos personalizados em um cliente com o SDK de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871757"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Preencher comandos de um cliente com o SDK de fala (versão prévia)

Para concluir tarefas usando um aplicativo de comandos personalizados, você pode enviar cargas personalizadas para um dispositivo de cliente conectado.

Neste artigo, você vai:

- Definir e enviar uma carga JSON personalizada de seu aplicativo de comandos personalizados
- Receber e visualizar o conteúdo da carga JSON personalizada de um aplicativo cliente do SDK de fala do C# UWP

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Uma chave de assinatura do Azure para o serviço de fala: [obtenha uma gratuitamente](get-started.md) ou crie-a no [portal do Azure](https://portal.azure.com)
> * Um aplicativo de comandos personalizados criado anteriormente: [início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
> * Um aplicativo cliente habilitado para SDK de fala: [início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala (versão prévia)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcional: introdução rápida

Este artigo descreve, passo a passo, como fazer um aplicativo cliente se comunicar com seu aplicativo de comandos personalizados. Se você preferir se aprofundar diretamente no, o código-fonte completo e pronto para compilar usado neste artigo está disponível nos exemplos do [SDK de fala](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Atender à carga JSON

1. Abra o aplicativo de comandos personalizados que você criou anteriormente em [início rápido: criar um comando personalizado com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
1. Marque a seção **regras de conclusão** para verificar se você tem a regra criada anteriormente que responde de volta ao usuário
1. Para enviar uma carga diretamente para o cliente, crie uma nova regra com uma ação enviar atividade

   > [!div class="mx-imgBorder"]
   > ![Regra de conclusão de atividade de envio](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome da Regra | UpdateDeviceState | Um nome que descreve a finalidade da regra |
   | Condições | Parâmetro obrigatório- `OnOff` e`SubjectDevice` | Condições que determinam quando a regra pode ser executada |
   | Ações | `SendActivity`(veja abaixo) | A ação a ser tomada quando a condição da regra for verdadeira |

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
   > ![Enviar carga da atividade](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Criar visuais para o estado ligado ou desligado do dispositivo

No [início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala](./quickstart-custom-speech-commands-speech-sdk.md), você criou um aplicativo cliente de SDK de fala `turn on the tv`que `turn off the fan`tratou comandos como,. Com alguns elementos visuais adicionados, você pode ver o resultado desses comandos.

Adicione caixas rotuladas com texto indicando **Ativar** ou **desativar** usando o seguinte XML adicionado ao`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Tratar conteúdo personalizável
### <a name="add-reference-libraries"></a>Adicionar bibliotecas de referência

Como você criou uma carga JSON, precisará adicionar uma referência à biblioteca [JSON.net](https://www.newtonsoft.com/json) para lidar com a desserialização.
- Cliente à sua solução.
- Escolha **gerenciar pacotes NuGet para a solução**, selecione **instalar** 
- Procure **Newtonsoft. JSON** na lista de atualizações, atualize **Microsoft. NetCore. UniversalWindowsPlatform** para a versão mais recente

> [!div class="mx-imgBorder"]
> ![Enviar carga da atividade](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Em ' MainPage. XAML. cs ', adicione
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Carga recebida do identificador

No `InitializeDialogServiceConnector`, substitua o `ActivityReceived` manipulador de eventos pelo código a seguir. O manipulador `ActivityReceived` de eventos modificado extrairá a carga da atividade e alterará o estado visual da TV ou do ventilador de acordo.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Experimente

1. Iniciar o aplicativo
1. Selecione habilitar microfone
1. Selecione o botão falar
1. Mencione`turn on the tv`
1. O estado visual da TV deve mudar para "on"
   > [!div class="mx-imgBorder"]
   > ![Enviar carga da atividade](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Adicionar validações a parâmetros de comando personalizados (visualização)](./how-to-custom-speech-commands-validations.md)
