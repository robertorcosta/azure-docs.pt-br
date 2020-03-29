---
title: 'Quickstart: Crie um comando personalizado com parâmetros (Preview) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você adicionará parâmetros a um aplicativo de Comandos Personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348525"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Quickstart: Crie um comando personalizado com parâmetros (Visualização)

No [artigo anterior,](./quickstart-custom-speech-commands-create-new.md)criamos um novo projeto de Comandos Personalizados para responder a comandos sem parâmetros.

Neste artigo, estenderemos este aplicativo com parâmetros para que ele possa lidar com a ligar e desligar vários dispositivos.

## <a name="create-parameters"></a>Create Parameters

1. Abra o projeto [que criamos anteriormente](./quickstart-custom-speech-commands-create-new.md)
1. Como o Comando agora vai manusear ligado e desligado, renomeie o Comando para "TurnOnOff"
   - Passar o passar por cima do nome do Comando e selecionar o ícone de edição para alterar o nome
1. Crie um novo parâmetro para representar se o usuário quer ativar ou desativar o dispositivo
   - Selecione `+` o ícone ao lado da seção Parâmetros

   > [!div class="mx-imgBorder"]
   > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

   | Configuração            | Valor sugerido     | Descrição                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | Onoff               | Um nome descritivo para seu parâmetro                                                                     |
   | É Global          | unchecked           | Caixa de seleção indicando se um valor para este parâmetro é aplicado globalmente a todos os Comandos do projeto |
   | Obrigatório           | checked             | Caixa de seleção indicando se um valor para este parâmetro é necessário antes de completar o Comando          |
   | Modelo de resposta  | "- Ligado ou desligado?"      | Um aviso para pedir o valor deste parâmetro quando ele não é conhecido                                       |
   | Type               | String              | O tipo de parâmetro, como número, string ou data                                               |
   | Configuração      | Lista de cordas         | Para Strings, uma lista de strings limita as entradas a um conjunto de valores possíveis                                      |
   | Valores da lista de strings | on, off             | Para um parâmetro String List, o conjunto de valores possíveis e seus sinônimos                                |

   - Em seguida, `+` selecione o ícone novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos. Para este exemplo, uma TV e um ventilador

   | Configuração            | Valor sugerido       | Descrição                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | Dispositivo de assunto         | Um nome descritivo para seu parâmetro                                                                     |
   | É Global          | unchecked             | Caixa de seleção indicando se um valor para este parâmetro é aplicado globalmente a todos os Comandos do projeto |
   | Obrigatório           | checked               | Caixa de seleção indicando se um valor para este parâmetro é necessário antes de completar o Comando          |
   | Modelo de resposta  | "- Qual dispositivo?"     | Um aviso para pedir o valor deste parâmetro quando ele não é conhecido                                       |
   | Type               | String                | O tipo de parâmetro, como número, string ou data                                               |
   | Configuração      | Lista de cordas           | Para Strings, uma lista de strings limita as entradas a um conjunto de valores possíveis                                      |
   | Valores da lista de strings | tv, ventilador               | Para um parâmetro String List, o conjunto de valores possíveis e seus sinônimos                                |
   | Sinônimos (tv)      | televisão, televisão     | Sinônimos opcionais para cada valor possível de um parâmetro de lista de strings                                      |

## <a name="add-sample-sentences"></a>Adicionar sentenças de exemplo

Com parâmetros, é útil adicionar frases de amostra que cobrem todas as combinações possíveis. Por exemplo: 

1. Informações completas sobre parâmetros -`"turn {OnOff} the {SubjectDevice}"`
1. Informações parciais de parâmetros -`"turn it {OnOff}"`
1. Nenhuma informação de parâmetro -`"turn something"`

As frases de exemplo com diferentes quantidades de informações permitem que o aplicativo Comandos Personalizados resolva resoluções de um tiro e resoluções de vários turnos com informações parciais.

Com isso em mente, edite as Sentenças de Amostra para usar os parâmetros conforme sugerido abaixo.

> [!TIP]
> No editor De frases de exemplo, use chaves para se referir aos seus parâmetros. - `turn {OnOff} the {SubjectDevice}`Use a conclusão da guia para se referir aos parâmetros criados anteriormente.

> [!div class="mx-imgBorder"]
> ![Sentenças de amostra com parâmetros](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Adicionar parâmetros à regra Conclusão

Modificar a regra Conclusão que você criou [no quickstart anterior:](./quickstart-custom-speech-commands-create-new.md)

1. Adicione uma nova Condição e selecione parâmetro necessário. Selecione `OnOff` ambos e`SubjectDevice`
1. Editar a ação resposta `OnOff` `SubjectDevice`de fala a ser usada e:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Experimentar

Abra o painel de bate-papo teste e tente algumas interações.

- Entrada: desligue a TV
- Saída: Ok, desligando a TV

- Entrada: desligue a televisão
- Saída: Ok, desligando a TV

- Entrada: desligá-lo
- Saída: Qual dispositivo?
- Entrada: a tv
- Saída: Ok, desligando a TV

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Quickstart: Use comandos personalizados com voz personalizada (visualização)](./quickstart-custom-speech-commands-select-custom-voice.md)
