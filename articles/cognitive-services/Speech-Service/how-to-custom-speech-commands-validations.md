---
title: 'Como: Adicionar validações a parâmetros de comando personalizados'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como adicionar validações a um parâmetro em comandos personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857197"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações a parâmetros de comando personalizados (visualização)

Neste artigo, você adicionará validações a parâmetros e prompts para correção.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

> [!div class="checklist"]
> * [Início rápido: criar um comando personalizado](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um comando personalizado com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperaturas

Para demonstrar validações, vamos criar um novo comando, permitindo que os usuários definam a temperatura.

1. Abra o aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Criar um novo comando`SetTemperature`
1. Adicione um parâmetro para a temperatura de destino.

   | Configuração do parâmetro           | Valor sugerido    |Descrição                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Nome              | Temperatura                       | Um nome descritivo para o parâmetro                                |
   | Obrigatório          | checked                           | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | Resposta para o parâmetro obrigatório     | Editor simples-> qual temperatura você deseja?  | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
   | Type              | Número                            | Tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |

1. Adicione uma validação para o parâmetro de temperatura.

    - Na página configuração de **parâmetros** para `Temperature` parâmetro, selecione `Add a validation` na seção validações.
    - Preencha os valores no pop-up **nova validação** da seguinte maneira e selecione **criar**.

  
       | Configuração do parâmetro         | Valor sugerido                                          | Descrição                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Valor mínimo        | 60               | Para parâmetros numéricos, o valor mínimo que esse parâmetro pode assumir |
       | Valor máximo        | 80               | Para parâmetros numéricos, o valor máximo que esse parâmetro pode assumir |
       | Resposta de falha – editor simples| Primeira variação-Desculpe, só posso definir entre 60 e 80 graus      | Solicitar um novo valor se a validação falhar                                       |

       > [!div class="mx-imgBorder"]
       > ![Adicionar uma validação de intervalo](media/custom-speech-commands/validations-add-temperature.png)

1. Adicionar algumas frases de exemplo

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicionar uma regra de conclusão para confirmar o resultado

   | Configuração    | Valor sugerido                                           |Descrição                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nome       | Mensagem de confirmação                                      |Um nome que descreve a finalidade da regra |
   | Condições | Parâmetros necessários-`Temperature`                       |Condições que determinam quando a regra pode ser executada    |   
   | Ações    | Enviar resposta de fala-`Ok, setting temperature to {Temperature} degrees` | A ação a ser tomada quando a condição da regra for verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Para obter exemplos sobre como concluir o comando com uma ação do cliente, consulte: [como realizar os comandos no cliente com o SDK de fala](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Experimente
1. Selecione `Train` o ícone presente na parte superior do painel direito.

1. Uma vez, o treinamento é concluído `Test` , selecione e experimente algumas interações.

    - Entrada: defina a temperatura como 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus
    - Entrada: defina a temperatura como 45 graus
    - Saída: Desculpe, só posso definir entre 60 e 80 graus
    - Entrada: torne-o 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: adicionar uma confirmação a um comando personalizado (visualização)](./how-to-custom-speech-commands-confirmations.md)
