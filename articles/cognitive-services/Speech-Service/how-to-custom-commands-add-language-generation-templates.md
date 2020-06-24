---
title: 'Como: usar modelos de geração de linguagem para respostas de fala – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a usar modelos de geração de idioma com comandos personalizados. Os modelos de geração de linguagem permitem que você personalize as respostas enviadas ao cliente e introduza a variação nas respostas.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307405"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de linguagem para respostas de fala

Neste artigo, você aprenderá a usar modelos de geração de idioma com comandos personalizados. Os modelos de geração de linguagem permitem que você personalize as respostas enviadas ao cliente e introduza a variação nas respostas. A personalização da geração de idioma pode ser obtida por:

- Uso de modelos de geração de linguagem
- Uso de expressões adaptáveis

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

> [!div class="checklist"]
> * [Como: criar aplicativo com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: adicionar parâmetros a comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Visão geral dos modelos de geração de linguagem

Os modelos de comandos personalizados são baseados nos [modelos de LG](https://aka.ms/speech/cc-lg-format)do BotFramework. Como os comandos personalizados criam um novo modelo LG quando necessário (ou seja, para respostas de fala em parâmetros ou ações), não é necessário especificar o nome do modelo LG. Então, em vez de definir seu modelo como:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Você só precisa definir o corpo do modelo sem o nome, da seguinte maneira.

> [!div class="mx-imgBorder"]
> ![exemplo de editor de modelo](./media/custom-commands/template-editor-example.png)


Essa alteração introduz a variação para as respostas de fala que estão sendo enviadas ao cliente. Portanto, para o mesmo expressão, a resposta de fala correspondente seria separada aleatoriamente das opções fornecidas.

Aproveitar os modelos LG também permite que você defina respostas complexas de fala para comandos usando expressões adaptáveis. Você pode consultar o [formato de modelos LG](https://aka.ms/speech/cc-lg-format) para obter mais detalhes. Por padrão, os comandos personalizados dão suporte a todos os recursos com as seguintes diferenças secundárias:

* Nas entidades de modelos LG são representadas como $ {EntityName}. Em comandos personalizados, não usamos entidades, mas parâmetros podem ser usados como variáveis com qualquer uma dessas representações $ {parameterName} ou {parameterName}
* Não há suporte para composição e expansão de modelo em comandos personalizados. Isso ocorre porque você nunca edita o `.lg` arquivo diretamente, mas apenas as respostas de modelos criados automaticamente.
* Não há suporte para funções personalizadas injetadas por LG em comandos personalizados. Funções predefinidas ainda têm suporte.
* Não há suporte para as opções (estrito, replaceNull & lineBreakStyle) em comandos personalizados.

## <a name="add-template-responses-to-turnonoff-command"></a>Adicionar respostas de modelo ao comando TurnOnOff

Modifique o comando **TurnOnOff** para adicionar um novo parâmetro com a seguinte configuração:

| Configuração            | Valor sugerido       | 
| ------------------ | --------------------- | 
| Nome               | `SubjectContext`         | 
| É global          | unchecked             | 
| Obrigatório           | unchecked               | 
| Type               | String                |
| Valor padrão      | `all` |
| Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
| Valores de entrada predefinidos | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Modificar regra de conclusão

Edite a seção **ações** da regra de conclusão existente **ConfirmationResponse**. No pop-up **Editar ação** , alterne para o **Editor de modelos** e substitua o texto pelo exemplo a seguir.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Treine** e **teste** seu aplicativo da seguinte maneira. Observe a variação de respostas devido ao uso de várias alternativas do valor do modelo e também ao uso de expressões adaptáveis.

* Entrada: ativar a TV
* Saída: Ok, ligando a TV
* Entrada: ativar a TV
* Saída: concluído, ativado na TV
* Entrada: desative as luzes
* Saída: Ok, desligando todas as luzes
* Entrada: desligar as luzes de sala
* Saída: Ok, desligando as luzes de sala

## <a name="use-custom-voice"></a>Usar voz personalizada

Outra maneira de personalizar respostas de comandos personalizados é selecionar uma voz de saída personalizada. Use as etapas a seguir para alternar a voz padrão para uma voz personalizada.

1. Em seu aplicativo de comandos personalizados, selecione **configurações** no painel esquerdo.
1. Selecione **voz personalizada** no painel central.
1. Selecione a voz pública ou personalizada desejada da tabela.
1. Clique em **Salvar**.

> [!div class="mx-imgBorder"]
> ![Sentenças de exemplo com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas**, os **tipos neurais** estão disponíveis somente para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neural por região/ponto de extremidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Para **vozes personalizadas**, elas podem ser criadas na página projeto de voz personalizado. Consulte [introdução à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Integre seus comandos personalizados usando o SDK de fala](./how-to-custom-commands-setup-speech-sdk.md).