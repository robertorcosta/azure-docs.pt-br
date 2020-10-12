---
title: 'Como: configurar o parâmetro como entidade de entidades externas'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como configurar um parâmetro de cadeia de caracteres para se referir ao catálogo exposto em um ponto de extremidade da Web.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284176"
---
# <a name="add-configurations-to-commands-parameters"></a>Adicionar configurações aos parâmetros de comandos

Neste artigo, você aprenderá mais sobre a configuração avançada de parâmetros, incluindo:

 - Como os valores de parâmetro podem pertencer a um conjunto definido externamente para o aplicativo de comandos personalizados
 - Como adicionar cláusulas de validação no valor dos parâmetros

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

> [!div class="checklist"]
> * [Como: criar aplicativo com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: adicionar parâmetros a comandos](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Configurar o parâmetro como uma entidade de catálogo externo

Nesta seção, você configura parâmetros de tipo de cadeia de caracteres para se referir a catálogos externos hospedados em um ponto de extremidade da Web. Isso permite que você atualize o catálogo externo independentemente sem fazer edições no aplicativo de comandos personalizados. Essa abordagem é útil em casos em que as entradas do catálogo podem ser grandes no número.

Reutilize o parâmetro **SubjectDevice** do comando **TurnOnOff** . A configuração atual para esse parâmetro é **aceitar entradas predefinidas do catálogo interno**. Isso se refere à lista estática de dispositivos, conforme definido na configuração do parâmetro. Queremos mover esse conteúdo para uma fonte de dados externa que pode ser atualizada de forma independente.

Para fazer isso, comece adicionando um novo ponto de extremidade da Web. Vá para a seção pontos de extremidade **da Web** no painel à esquerda e adicione um novo Web EndPoint com a configuração a seguir.

| Configuração | Valor sugerido |
|----|----|
| Nome | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Método | GET |


Se o valor sugerido para a URL não funcionar para você, você precisará configurar e hospedar um ponto de extremidade simples da Web que retorna um JSON que consiste em uma lista dos dispositivos que podem ser controlados. O ponto de extremidade da Web deve retornar um JSON formatado da seguinte maneira:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Em seguida, vá para a página Configurações de parâmetro **SubjectDevice** e altere as propriedades para o seguinte.

| Configuração | Valor sugerido |
| ----| ---- |
| Configuração | Aceitar entradas predefinidas do catálogo externo |                               
| Ponto de extremidade do catálogo | dispositivos |
| Método | GET |

Em seguida, selecione **Salvar**.

> [!IMPORTANT]
> Você não verá uma opção para configurar um parâmetro para aceitar entradas de um catálogo externo, a menos que tenha o ponto de extremidade da Web definido na seção **ponto de extremidade da Web** no painel esquerdo.

### <a name="try-it-out"></a>Experimentar

Selecione **treinar** e aguarde a conclusão do treinamento. Quando o treinamento for concluído, selecione **testar** e tente algumas interações.

* Entrada: ativar
* Saída: qual dispositivo você deseja controlar?
* Entrada: luzes
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Observe como você pode controlar todos os dispositivos hospedados no ponto de extremidade da Web agora. Você ainda precisa treinar o aplicativo para testar as novas alterações e publicar o aplicativo novamente.

## <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

**Validações** são construções aplicáveis a determinados tipos de parâmetro que permitem que você configure restrições no valor do parâmetro e solicite a correção se os valores para não ficarem dentro das restrições. Para obter uma lista completa de tipos de parâmetro que estendem a construção de validação, acesse [referências](./custom-commands-references.md)

Teste validações usando o comando **settemperaturas** . Use as etapas a seguir para adicionar uma validação para o parâmetro de **temperatura** .

1. Selecione o comando **settemperaturas** no painel esquerdo.
1. Selecione  **temperatura** no painel central.
1. Selecione **Adicionar uma validação** presente no painel direito.
1. Na janela **nova validação** , configure a validação da seguinte maneira e selecione **criar**.


    | Configuração do parâmetro | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | Valor mínimo | `60` | Para parâmetros numéricos, o valor mínimo que esse parâmetro pode assumir |
    | Valor máximo | `80` | Para parâmetros numéricos, o valor máximo que esse parâmetro pode assumir |
    | Resposta de falha |  Editor simples > primeira > de variação `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Solicitar um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Adicionar uma validação de intervalo](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Experimentar

1. Selecione o ícone de **treinamento** presente na parte superior do painel direito.

1. Uma vez, o treinamento é concluído, selecione **testar** e tente algumas interações:

    - Entrada: defina a temperatura como 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus
    - Entrada: defina a temperatura como 45 graus
    - Saída: Desculpe, só posso definir a temperatura entre 60 e 80 graus
    - Entrada: torne-o 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: adicionar regras de interação](./how-to-custom-commands-add-interaction-rules.md)
