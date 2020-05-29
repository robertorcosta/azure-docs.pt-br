---
title: 'Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você adicionará parâmetros a um aplicativo de comandos personalizados para que ele possa ativar e desativar vários dispositivos.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142275"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros

No [artigo anterior](./quickstart-custom-speech-commands-create-new.md), você criou um aplicativo simples de comandos personalizados sem parâmetros.

Neste artigo, você estenderá esse aplicativo com parâmetros para que ele possa ativar e desativar vários dispositivos.

## <a name="create-parameters"></a>Criar parâmetros

1. Abra o projeto que você criou no [artigo anterior](./quickstart-custom-speech-commands-create-new.md).

   Vamos editar o comando existente para que ele possa ser usado para ativar e desativar vários dispositivos.
1. Como o comando agora tratará on e off, renomeie-o como **TurnOnOff**.
   1. No painel esquerdo, selecione o comando **Ativar** e, em seguida, selecione o botão de reticências (**...**) ao lado do **comando novo** na parte superior do painel.
   
   1. Selecione **renomear**. Na janela de **comando renomear** , altere o **nome** para **TurOnOff**. Selecione **Salvar**.

1. Crie um parâmetro para representar se o usuário deseja ativar ou desativar o dispositivo.
   1. Selecione **Adicionar** na parte superior do painel central. Na lista suspensa, selecione **parâmetro**.
   1. No painel direito, na seção **parâmetros** , adicione um valor na caixa **nome** .
   1. Selecione **obrigatório**. Na janela **Adicionar resposta para um parâmetro necessário** , selecione **editor simples**. Na primeira caixa de **variação** , digite este texto:
        ```
        On or Off?
        ```
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Configure o restante das propriedades do parâmetro da seguinte maneira:
       

    | Configuração      | Valor sugerido     | Descrição                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Nome**               | **OnOff**           | Um nome descritivo para o parâmetro.                                                                  |
    | **É global**          | Desmarcada       | Uma caixa de seleção que indica se um valor para o parâmetro é aplicado globalmente a todos os comandos no aplicativo.|
    | **Necessária**           | Selecionada         | Uma caixa de seleção que indica se um valor para o parâmetro é necessário.  |
    | **Resposta para o parâmetro obrigatório**      |**Editor simples – > ativado ou desativado?**      | Um prompt para solicitar o valor do parâmetro quando ele não for conhecido. |
    | **Tipo**               | **Cadeia de caracteres**          | O tipo do parâmetro. Por exemplo, número, Cadeia de caracteres, data e hora, geografia.   |
    | **Configuration**      | **Aceitar valores de entrada predefinidos do catálogo interno** | Para cadeias de caracteres, essa configuração limita as entradas a um conjunto de valores possíveis. |
    | **Valores de entrada predefinidos**     | **ativado**, **desativado**             | Um conjunto de possíveis valores e seus aliases.         |
       


    > [!div class="mx-imgBorder"]
    > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

1. Clique em **Salvar** para salvar as configurações.

 1. Selecione **Adicionar** novamente para adicionar um segundo parâmetro. Esse parâmetro representa o nome do dispositivo. Use estas configurações:
   

       | Configuração            | Valor sugerido       | Descrição                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Nome**               | **SubjectDevice**         | Um nome descritivo para o parâmetro.                                                                     |
       | **É global**          | Desmarcada             | Uma caixa de seleção que indica se um valor para o parâmetro é aplicado globalmente a todos os comandos no aplicativo. |
       | **Necessária**           | Selecionada               | Uma caixa de seleção que indica se um valor para o parâmetro é necessário.          |
       | **Editor simples**      | **Qual dispositivo?**    | Um prompt para solicitar o valor do parâmetro quando ele não for conhecido.                                       |
       | **Tipo**               | **Cadeia de caracteres**                | O tipo do parâmetro. Por exemplo, número, Cadeia de caracteres, data e hora, geografia.                                                |
       | **Configuration**      | **Aceitar valores de entrada predefinidos do catálogo interno** | Para cadeias de caracteres, essa configuração limita as entradas a um conjunto de valores possíveis.       |
       | **Valores de entrada predefinidos** | **TV**, **ventilador**               | Um conjunto de possíveis valores e seus aliases.                               |
       | **Aliases** (TV)      | **televisão**, **conte**     | Aliases opcionais para cada um dos valores de entrada predefinidos.                                 |

## <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Para comandos que têm parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

- Informações completas do parâmetro:`turn {OnOff} the {SubjectDevice}`
- Informações de parâmetro parcial:`turn it {OnOff}`
- Nenhuma informação de parâmetro:`turn something`

As frases de exemplo que têm diferentes quantidades de informações permitem que o aplicativo de comandos personalizados resolva as resoluções de uma imagem e as resoluções de várias opções que têm informações parciais.

Com isso em mente, edite as sentenças de exemplo para usar os parâmetros como sugeridos aqui:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> No editor de sentenças de exemplo, use chaves para se referir aos seus parâmetros: `turn {OnOff} the {SubjectDevice}` .
>
> Use Tab para preenchimento automático definido por parâmetros criados anteriormente.

## <a name="add-parameters-to-completion-rules"></a>Adicionar parâmetros às regras de conclusão

Modifique a regra de conclusão que você criou no guia de [início rápido anterior](./quickstart-custom-speech-commands-create-new.md).

1. Na seção **condições** , selecione **Adicionar uma condição**.
1. Na janela **nova condição** , na lista **tipo** , selecione **parâmetros necessários**. Na lista, selecione **Onoff** e **SubjectDevice**.
1. Selecione **Criar**.
1. Na seção **ações** , edite a ação **Enviar resposta de fala** existente passando o mouse sobre a ação e selecionando o botão Editar. Desta vez, use os `OnOff` parâmetros novos e `SubjectDevice` :

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Experimentar
1. Selecione **treinar** na parte superior do painel direito.

1. Quando o treinamento for concluído, selecione **testar**.
    
    Uma janela **testar seu aplicativo** será exibida.

1. Experimente algumas interações.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início rápido: usar comandos personalizados com voz personalizada (visualização)](./quickstart-custom-speech-commands-select-custom-voice.md)
