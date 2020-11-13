---
title: Atualizar um comando de um ponto de extremidade da Web
titleSuffix: Azure Cognitive Services
description: atualizar um comando de um ponto de extremidade da Web
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571161"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Atualizar um comando de um ponto de extremidade da Web

Se o aplicativo cliente exigir a atualização do estado de um comando contínuo sem entrada de voz, você poderá usar uma chamada para um ponto de extremidade da Web para atualizar o comando.

Neste artigo, você aprenderá como atualizar um comando contínuo de um ponto de extremidade da Web.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class = "checklist"]
> * Um [aplicativo de Comandos Personalizados criado](quickstart-custom-commands-application.md) anteriormente

## <a name="create-an-azure-function"></a>Criar uma Função do Azure 

Para este exemplo, precisaremos de uma HTTP-Triggered [função do Azure](https://docs.microsoft.com/azure/azure-functions/) que dá suporte à entrada a seguir (ou a um subconjunto dessa entrada).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Vamos examinar os principais atributos dessa entrada.

| Atributo | Explicação |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversaid" é o identificador exclusivo da conversa, observe que essa ID pode ser gerada no aplicativo cliente. |
| **currentCommand** | "currentCommand" é o comando atualmente ativo na conversa. |
| **name** | "Name" é o nome do comando e "Parameters" é um mapa com os valores atuais dos parâmetros. |
| **currentGlobalParameters** | "currentGlobalParameters" também é um mapa como "Parameters", mas é usado para parâmetros globais. |

A saída da função do Azure precisa oferecer suporte ao formato a seguir.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Você pode reconhecer esse formato, uma vez que é o mesmo usado ao [atualizar um comando do cliente](./how-to-custom-commands-update-command-from-client.md). 

Agora, crie uma função do Azure com base em NodeJS e copie e cole este código

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Quando chamamos essa função do Azure de comandos personalizados, enviaremos os valores atuais da conversa e retornaremos os parâmetros que desejamos atualizar ou, se quisermos cancelar o comando atual.

## <a name="update-the-existing-custom-commands-app"></a>Atualizar o aplicativo de comandos personalizados existente

Agora, vamos conectar o Azure function com o aplicativo de comandos personalizados existente.

1. Adicione um novo comando chamado IncrementCounter.
1. Adicione apenas uma frase de exemplo com o valor "Increment".
1. Adicione um novo parâmetro chamado Counter (mesmo nome especificado na função do Azure acima) do tipo number com um valor padrão de 0.
1. Adicione um novo ponto de extremidade da Web chamado IncrementEndpoint com a URL do Azure Function e com as atualizações remotas habilitadas.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Definir ponto de extremidade da Web com atualizações remotas":::
1. Crie uma nova regra de interação chamada "IncrementRule" e adicione uma ação chamar ponto de extremidade da Web.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Regra de incremento":::
1. Na configuração da ação, selecione o IncrementEndpoint, configurar em caso de êxito para enviar a resposta de fala com o valor de contador e em caso de falha com uma mensagem de erro.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Definir ponto de extremidade de chamada do contador de incremento":::
1. Definir o estado de pós-execução da regra para aguardar a entrada do usuário

## <a name="test-it"></a>Testá-lo

1. Salvar e treinar seu aplicativo
1. Clique em testar
1. Envie algumas vezes "incremento" (que é a sentença de exemplo para o comando IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Exemplo de contador de incremento":::

Observe como o valor do parâmetro Counter é incrementado em cada uma das transformações do Azure function.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar um processo de CI/CD para o aplicativo de Comandos Personalizados](./how-to-custom-commands-deploy-cicd.md)