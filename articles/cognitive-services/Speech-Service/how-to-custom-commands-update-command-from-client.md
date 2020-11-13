---
title: Atualizar um comando do aplicativo cliente
titleSuffix: Azure Cognitive Services
description: atualizar um comando do aplicativo cliente
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 290f9ee9c23071ac56b1ff0c65ddc03decbc7344
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571153"
---
# <a name="update-a-command-from-the-client"></a>Atualizar um comando do cliente

Neste artigo, aprenderemos como atualizar um comando contínuo de um aplicativo cliente.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente

## <a name="update-the-state-of-a-command"></a>Atualizar o estado de um comando

Se o aplicativo cliente exigir a atualização do estado de um comando contínuo sem entrada de voz, você poderá enviar um evento para atualizar o comando.

Para ilustrar esse cenário, para atualizar o estado de um comando contínuo (TurnOnOff), podemos enviar a atividade de evento a seguir. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Vamos examinar os principais atributos dessa atividade.

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **tipo** | A atividade é do tipo "Event" e o nome do evento precisa ser "RemoteUpdate". |
| **value** | O atributo "value" contém os atributos necessários para atualizar o comando atual. |
| **updatedCommand** | O atributo "updatedCommand" contém o nome do comando, "updatedparameters" é um mapa com o nome dos parâmetros e seus valores atualizados. |
| **cancel** | Se o comando contínuo precisar ser cancelado, defina o atributo "Cancelar" como verdadeiro. |
| **updatedGlobalParameters** | O atributo "updatedGlobalParameters" também é um mapa, assim como "updatedparameters", mas usado para parâmetros globais. |
| **processTurn** | Se a ativação precisar ser processada depois que a atividade for enviada, defina o atributo "processTurn" como true. |

Você pode testar esse cenário no portal de comandos personalizados.

1. Abra o aplicativo de Comandos Personalizados criado anteriormente. 
1. Clique em treinar e em testar.
1. Envie "Turn".
1. Abra o painel lateral e clique em editor de atividade.
1. Digite e envie o evento RemoteCommand especificado na seção anterior.
    > [!div class="mx-imgBorder"]
    > ![Enviar comando remoto](media/custom-commands/send-remote-command-activity.png)

Observe como o valor do parâmetro "OnOff" foi definido como "on" usando uma atividade do cliente em vez de fala ou texto.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Atualizar o catálogo do parâmetro para um comando

Quando você configura a lista de opções válidas para um parâmetro, os valores para o parâmetro são definidos globalmente para o aplicativo. 

Em nosso exemplo, o parâmetro SubjectDevice terá uma lista fixa de valores com suporte, independentemente da conversa.

Caso você precise adicionar novas entradas ao catálogo do parâmetro por conversa, você pode enviar a atividade a seguir.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Com essa atividade, adicionamos uma entrada para "estéreo" ao catálogo do parâmetro "SubjectDevice" no comando "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Atualizar catálogo":::

Observe algumas coisas.
1. Você só precisa enviar essa atividade uma vez (idealmente logo após a inicialização de uma conexão).
1. Depois de enviar essa atividade, você deve aguardar até que o evento ParameterCatalogsUpdated seja enviado de volta ao cliente.

## <a name="add-additional-context-from-the-client-application"></a>Adicionar contexto adicional do aplicativo cliente

Você pode definir contexto adicional do aplicativo cliente por conversa que pode ser usado posteriormente em seu aplicativo de comandos personalizados. 

Por exemplo, pense no cenário em que você deseja enviar a ID e o nome do dispositivo conectado ao aplicativo de comandos personalizados.

Para testar esse cenário, vamos criar um novo comando em nosso aplicativo atual.
1. Crie um novo comando chamado GetDeviceInfo.
1. Adicione uma sentença de exemplo com "obter informações do dispositivo".
1. Na regra de conclusão "concluído", adicione uma ação enviar resposta de fala.
    > ![Enviar resposta de fala com contexto](media/custom-commands/send-speech-response-context.png)
1. Salve e treine seu aplicativo.
1. Teste seu aplicativo.
    > ![Enviar atividade de contexto de cliente](media/custom-commands/send-client-context-activity.png)

Observe algumas coisas.
1. Você só precisa enviar essa atividade uma vez (idealmente logo após a inicialização de uma conexão).
1. Você pode usar objetos complexos para ClientContext.
1. Você pode usar ClientContext em respostas de fala para enviar atividades e ao chamar pontos de extremidade da Web.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atualizar um comando de um ponto de extremidade da Web](./how-to-custom-commands-update-command-from-web-endpoint.md)
