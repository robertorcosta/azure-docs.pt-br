---
title: 'Início rápido: criar um comando personalizado com parâmetros (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você adicionará parâmetros a um aplicativo de comandos personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853579"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Início rápido: criar um aplicativo de comandos personalizados com parâmetros (versão prévia)

No [artigo anterior](./quickstart-custom-speech-commands-create-new.md), você criou um aplicativo simples de comandos personalizados sem parâmetros.

Neste artigo, você estenderá esse aplicativo para fazer uso de parâmetros para que ele possa lidar com a ativação e a desativação de vários dispositivos.

## <a name="create-parameters"></a>Create Parameters

1. Abra o projeto [que você criou anteriormente](./quickstart-custom-speech-commands-create-new.md)
1. Vamos editar o comando existente para ativar e desativar vários dispositivos.
1. Como o comando agora será manipulado e desativado, renomeie o `TurnOnOff`comando para.
   - No painel esquerdo, selecione o `TurnOn` comando e, em seguida, `...` clique no ícone `+ New command` ao lado de na parte superior do painel.
   
   - Selecione `Rename` o ícone. No pop-up de **comando renomear** , altere `TurOnOff`o **nome** para. Em seguida, selecione **Salvar**.

1. Em seguida, você cria um novo parâmetro para representar se o usuário deseja ativar ou desativar o dispositivo.
   - Selecione `+ Add` o ícone presente na parte superior do painel central. Na lista suspensa, selecione **parâmetro**.
   - No painel mais à direita, você pode ver a seção configuração de **parâmetros** .
   - Adicione o valor para o **nome**.
   - Marque a caixa de seleção **necessária** . Na janela **Adicionar resposta para um parâmetro necessário** , selecione **editor simples** e para a **primeira variação**, adicionar
        ```
        On or Off?
        ```
   - Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Em seguida, vamos configurar o restante das propriedades do parâmetro da seguinte maneira e selecionar `Save` para salvar a configuração de todas as configurações no parâmetro.
       

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | OnOff           | Um nome descritivo para o parâmetro                                                                           |
       | É global          | unchecked       | Caixa de seleção que indica se um valor para esse parâmetro é globalmente aplicado a todos os comandos no aplicativo|
       | Obrigatório           | checked         | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
       | Resposta para o parâmetro obrigatório      |Editor simples – > ativado ou desativado?      | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
       | Type               | String          | O tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cadeias de caracteres, isso limita as entradas a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | on, off             | Conjunto de valores possíveis e seus aliases         |
       
        > [!div class="mx-imgBorder"]
        > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

   - Em seguida, selecione `+ Add` o ícone novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos com a configuração a seguir.
   

       | Configuração            | Valor sugerido       | Descrição                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Nome               | SubjectDevice         | Um nome descritivo para o parâmetro                                                                     |
       | É global          | unchecked             | Caixa de seleção que indica se um valor para esse parâmetro é globalmente aplicado a todos os comandos no aplicativo |
       | Obrigatório           | checked               | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando          |
       | Editor simples      | Qual dispositivo?    | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido                                       |
       | Type               | String                | O tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia                                                |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cadeias de caracteres, uma lista String limita as entradas a um conjunto de valores possíveis       |
       | Valores de entrada predefinidos | TV, ventilador               | Conjunto de valores possíveis e seus aliases                               |
       | Aliases (TV)      | televisão, conte     | Aliases opcionais para cada valor possível de valores de entrada predefinidos                                 |

## <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Com comandos com parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo: 

1. Informações completas do parâmetro-`turn {OnOff} the {SubjectDevice}`
1. Informações de parâmetro parcial-`turn it {OnOff}`
1. Nenhuma informação de parâmetro-`turn something`

As frases de exemplo com um grau diferente de informações permitem que o aplicativo de comandos personalizados resolva as resoluções de uma imagem e as resoluções de vários desligamentos com informações parciais.

Com isso em mente, edite as sentenças de exemplo para usar os parâmetros conforme sugerido abaixo.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> No editor de sentenças de exemplo, use chaves para fazer referência aos parâmetros. - `turn {OnOff} the {SubjectDevice}`Use a guia para preenchimento automático apoiado por parâmetros criados anteriormente.

## <a name="add-parameters-to-completion-rules"></a>Adicionar parâmetros às regras de conclusão

Modifique a regra de conclusão que criamos no guia [de início rápido anterior](./quickstart-custom-speech-commands-create-new.md).

1. Na seção **condições** , adicione uma nova condição selecionando **+ Adicionar uma condição**
1. Na nova condição pop-up **nova**, selecione `Required parameters` na lista suspensa **tipo** . Na lista de verificação abaixo, marque `OnOff` e. `SubjectDevice`
1. Clique em **Criar**.
1. Na seção **ações** , edite a ação enviar resposta de fala existente passando o mouse sobre a ação e clicando no ícone Editar. Desta vez, vamos usar os parâmetros `OnOff` e `SubjectDevice` os recém-criados

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Experimente
1. Selecione `Train` o ícone presente na parte superior do painel direito.

1. Uma vez, o treinamento é concluído `Test`, selecione.
    - Será exibido um novo **teste da janela do aplicativo** .
    - Experimente algumas interações.

        - Entrada: desligar a TV
        - Saída: Ok, desligando a TV        
        - Entrada: desligar a televisão
        - Saída: Ok, desligando a TV
        - Entrada: Desative-a
        - Saída: qual dispositivo?
        - Entrada: a TV
        - Saída: Ok, desligando a TV

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início rápido: usar comandos personalizados com voz personalizada (visualização)](./quickstart-custom-speech-commands-select-custom-voice.md)
