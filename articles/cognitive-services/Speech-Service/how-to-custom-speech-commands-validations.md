---
title: 'Como: Adicionar validações aos parâmetros de comando personalizado (Visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como adicionar validações a um parâmetro em Comandos Personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156447"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações aos parâmetros de comando personalizado (Visualização)

Neste artigo, você aprenderá como adicionar validações aos parâmetros e solicitar correção.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter completado as etapas nos seguintes artigos:

- [Quickstart: Crie um comando personalizado (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Crie um comando SetTemperature

Para demonstrar validações, vamos criar um novo Comando permitindo que o usuário defina a temperatura.

1. Abra seu aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Crie um novo **setde comandoTemperatura**
1. Adicione um parâmetro para a temperatura-alvo
1. Adicionar uma validação para o parâmetro de temperatura
   > [!div class="mx-imgBorder"]
   > ![Adicionar uma validação de intervalo](media/custom-speech-commands/validations-add-temperature.png)

   | Configuração           | Valor sugerido                                          | Descrição                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Um nome descritivo para seu parâmetro de comando                                                    |
   | Obrigatório          | true                                                     | Caixa de seleção indicando se um valor para este parâmetro é necessário antes de completar o Comando |
   | Modelo de resposta | "- Que temperatura você gostaria?"                     | Um aviso para pedir o valor deste parâmetro quando ele não é conhecido                              |
   | Type              | Número                                                   | O tipo de parâmetro, como número, string ou data                                      |
   | Validação        | Valor min: 60, Valor máximo: 80                             | Para parâmetros número, a faixa permitida de valores para o parâmetro                             |
   | Modelo de resposta | "- Desculpe, eu só posso definir entre 60 e 80 graus"      | Solicite um valor atualizado se a validação falhar                                       |

1. Adicione algumas frases de exemplo

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicionar uma regra de conclusão para confirmar o resultado

   | Configuração    | Valor sugerido                                           | Descrição                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Mensagem de confirmação                                      | Um nome descrevendo o propósito da regra          |
   | Condições | Parâmetro necessário - Temperatura                          | Condições que determinam quando a regra pode ser executada    |
   | Ações    | SpeechResponse - "- Ok, configurando para {Graus de temperatura} | A ação a tomar quando a condição de regra é verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Para exemplos ao completar o Comando com uma ação do cliente, consulte: [Como: Cumprir comandos no cliente com o Speech SDK (Preview)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Experimentar

Selecione o painel Teste e tente algumas interações.

- Entrada: Definir a temperatura para 72 graus
- Saída: "Ok, configurando para 72 graus"

- Entrada: Definir a temperatura para 45 graus
- Saída: "Desculpe, eu só posso definir entre 60 e 80 graus"
- Entrada: faça 72 graus em vez disso
- Saída: "Ok, configurando para 72 graus"

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Adicionar uma confirmação a um comando personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)
