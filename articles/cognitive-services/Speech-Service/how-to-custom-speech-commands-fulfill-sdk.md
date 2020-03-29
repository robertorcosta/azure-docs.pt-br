---
title: Como cumprir comandos de um cliente com o Speech SDK
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como lidar com as atividades de Comandos Personalizados em um cliente com o Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367731"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Cumprir comandos de um cliente com o Speech SDK (Preview)

Para concluir tarefas usando um aplicativo de Comandos Personalizados, você pode enviar cargas personalizadas para um dispositivo cliente conectado.

Neste artigo, você:

- Defina e envie uma carga json personalizada do seu aplicativo de comandos personalizados
- Receba e visualize o conteúdo de carga JSON personalizado a partir de um aplicativo cliente C# UWP Speech SDK

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Uma chave de assinatura do Azure para o serviço speech
  - [Obtenha um de graça](get-started.md) ou crie-o no [portal Azure](https://portal.azure.com)
- Um aplicativo de comandos personalizados criado anteriormente
  - [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)
- Um aplicativo cliente habilitado para Speech SDK
  - [Partida rápida: Conecte-se a um aplicativo de comando personalizado com o Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Opcional: Comece rápido

Este artigo descreve, passo a passo, como fazer um aplicativo cliente para falar com seu aplicativo de Comandos Personalizados. Se você preferir mergulhar direito, o código-fonte completo e pronto para compilar usado neste artigo está disponível no [Speech SDK Samples](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Cumprir com a carga útil JSON

1. Abra seu aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Verifique a seção **Regras de conclusão** para ter certeza de que você tem a regra criada anteriormente que responde ao usuário
1. Para enviar uma carga diretamente ao cliente, crie uma nova regra com uma ação Enviar atividade

   > [!div class="mx-imgBorder"]
   > ![Enviar regra de conclusão de atividade](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Configuração | Valor sugerido | Descrição |
   | ------- | --------------- | ----------- |
   | Nome da Regra | AtualizaçãoDeviceState | Um nome descrevendo o propósito da regra |
   | Condições | Parâmetro obrigatório `OnOff` - e`SubjectDevice` | Condições que determinam quando a regra pode ser executada |
   | Ações | `SendActivity`(veja abaixo) | A ação a tomar quando a condição de regra é verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Enviar carga útil da atividade](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Criar visuais para dispositivos dentro ou fora do estado

No [Quickstart: Conecte-se a um aplicativo de comando personalizado com o Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) você criou um aplicativo cliente Speech SDK que lidava com comandos como `turn on the tv`, `turn off the fan`. Agora adicione alguns visuais para que você possa ver o resultado desses comandos.

Adicione caixas rotuladas com texto indicando **ligado** ou **desligado** usando o Seguinte XML adicionado`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Lidar com carga útil personalizável

Agora que você criou uma carga JSON, você pode adicionar uma referência à biblioteca [JSON.NET](https://www.newtonsoft.com/json) para lidar com a desserialização.

> [!div class="mx-imgBorder"]
> ![Enviar carga útil da atividade](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Adicione `InitializeDialogServiceConnector` o seguinte `ActivityReceived` ao seu manipulador de eventos. O código adicional extrairá a carga da atividade e alterará o estado visual da TV ou ventilador de acordo.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Experimentar

1. Iniciar o aplicativo
1. Selecione Ativar microfone
1. Selecione o botão Falar
1. Dizer`turn on the tv`
1. O estado visual da TV deve mudar para "On"

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Adicionar validações aos parâmetros de comando personalizado (visualização)](./how-to-custom-speech-commands-validations.md)
