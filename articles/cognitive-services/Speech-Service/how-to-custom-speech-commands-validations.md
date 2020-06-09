---
title: Adicionar validações em comandos personalizados visualização-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar validações a um parâmetro de comando em um aplicativo de visualização de comandos personalizados.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509569"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Adicionar validações a um parâmetro de comando em um aplicativo de visualização de comandos personalizados

Neste artigo, você aprenderá a adicionar validações a parâmetros e prompts para correção.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas nos seguintes artigos:

> [!div class="checklist"]
 
> * [Início rápido: criar um aplicativo de visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperaturas

Para demonstrar validações, crie um novo comando que permita aos usuários definir a temperatura.

1. No [Speech Studio](https://speech.microsoft.com/), abra o aplicativo de visualização de comandos personalizados que você criou.
1. Crie um novo comando **settemperatura** .
1. Adicione um parâmetro de temperatura que tenha a seguinte configuração:

   | Configuração do parâmetro           | Valor sugerido    |Descrição                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Nome**              | **Temperatura**                       | Um nome descritivo para o parâmetro                                |
   | **Necessário**          | Verificado                           | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | **Resposta para o parâmetro obrigatório**     | **Editor simples-> qual temperatura você deseja?**  | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
   | **Tipo**              | **Número**                            | Tipo de parâmetro, como número, Cadeia de caracteres, DateTime ou Geografia   |

1. Adicione uma validação para o parâmetro de temperatura.

    1. Na página configuração de **parâmetros** para o parâmetro de temperatura, selecione **Adicionar uma validação** na seção **validações** .

    1. Na janela pop-up **nova validação** , configure a validação da seguinte maneira:
  
       | Configuração do parâmetro         | Valor sugerido                                          | Descrição                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Valor mínimo**        | **60**               | Para parâmetros numéricos, o valor mínimo que esse parâmetro pode assumir |
       | **Valor máximo**        | **80**               | Para parâmetros numéricos, o valor máximo que esse parâmetro pode assumir |
       | **Resposta de falha – editor simples**| **Primeira variação-Desculpe, só posso definir entre 60 e 80 graus**      | Solicitar um novo valor se a validação falhar                                       |

       > [!div class="mx-imgBorder"]
       > ![Adicionar uma validação de intervalo](media/custom-speech-commands/validations-add-temperature.png)

1. Selecione **Criar**.

1. Adicione algumas frases de exemplo.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicione uma regra de conclusão que tenha a configuração a seguir. Essa regra confirma o resultado.

   | Configuração    | Valor sugerido                                           |Descrição                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nome       | Mensagem de confirmação                                      |Um nome que descreve a finalidade da regra |
   | **Condições** | **Parâmetros necessários-temperatura**                       |Condições que determinam quando a regra pode ser executada    |   
   | **Ações**    | **Enviar resposta de fala-Ok, definindo a temperatura para {temperatura} graus** | A ação a ser tomada quando a condição da regra for verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Para obter exemplos sobre como concluir o comando com uma ação do cliente, consulte [como: preencher comandos no cliente com o SDK de fala](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Experimentar

1. Selecione **Treinar**.

1. Após a conclusão do treinamento, selecione **teste**e tente estas interações:

    - Entrada: defina a temperatura como 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus
    - Entrada: defina a temperatura como 45 graus
    - Saída: Desculpe, só posso definir entre 60 e 80 graus
    - Entrada: torne-o 72 graus
    - Saída: Ok, definindo a temperatura como 72 graus

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar confirmações a um comando em um aplicativo de visualização de comandos personalizados](./how-to-custom-speech-commands-confirmations.md)
