---
title: 'Início rápido: criar um comando personalizado (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria e testa um aplicativo de comandos personalizados hospedado.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872513"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Início rápido: criar um aplicativo de comandos personalizados (visualização)

Neste guia de início rápido, você aprenderá a criar e testar um aplicativo de comandos personalizados.
O aplicativo criado irá processar declarações como "ligar a TV" e responder com uma mensagem simples "Ok, ligando a TV".

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de Fala do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Neste momento, os comandos personalizados dão suporte apenas a assinaturas de fala nas regiões westus, westus2 e neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ir para o Speech Studio para comandos personalizados

1. Abra o navegador da Web e navegue até o [Speech Studio](https://speech.microsoft.com/)
1. Insira suas credenciais para entrar no portal.

   - A exibição padrão é sua lista de assinaturas de fala.
     > [!NOTE]
     > Se você não vir a página Selecionar assinatura, poderá navegar até lá escolhendo "recursos de fala" no menu configurações na barra superior.

1. Selecione sua assinatura de fala e, em seguida, selecione **ir para estúdio**.
1. Selecione **comandos personalizados**.

     - O modo de exibição padrão é uma lista dos aplicativos de comandos personalizados que você tem sob sua assinatura selecionada.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de comandos personalizados

1. Selecione **novo projeto** para criar um novo projeto.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Insira o nome do projeto.
1. Selecione idioma na lista suspensa.
1. Selecione um recurso de criação na lista suspensa. Se não houver recursos de criação válidos, crie um clicando em **criar novo recurso de criação de Luis**.

   > [!div class="mx-imgBorder"]
   > ![Criar um recurso](media/custom-speech-commands/create-new-resource.png)

   - Insira o nome do recurso, grupo de recursos.
   - Escolha o valor para o local e o tipo de preço na lista suspensa.

      > [!NOTE]
      > Você pode criar grupos de recursos inserindo o nome do grupo de recursos desejado no campo "grupo de recursos". O grupo de recursos será criado quando a **criação** for selecionada.

1. Em seguida, selecione **criar** para criar seu projeto.
1. Depois de criado, selecione seu projeto.

    - Sua exibição agora deve ser uma visão geral do aplicativo de comandos personalizados recém-criado.

## <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o recurso de criação que foi definido na janela novo projeto e definir um recurso de previsão. O recurso de previsão é usado para reconhecimento depois que o aplicativo de comandos personalizados é publicado. Você não precisa de um recurso de previsão para as fases de desenvolvimento e teste.

1. Selecione **configurações** no painel esquerdo e, em seguida, navegue até a seção **recursos Luis** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **criar novo recurso**.
1. Clique em **Salvar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir recursos do LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Como o recurso de criação dá suporte a apenas 1.000 solicitações de ponto de extremidade de previsão por mês, você precisará definir um recurso de previsão LUIS antes de publicar o aplicativo de comandos personalizados.


## <a name="create-a-new-command"></a>Criar um novo comando

Vamos criar um comando simples que usará um único expressão, `turn on the tv`e responderá com a mensagem. `Ok, turning on the tv`

1. Crie um novo comando selecionando o `+ New command` ícone presente na parte superior do painel mais à esquerda. Um novo pop-up aparece intitulado **novo comando**.
1. Forneça um valor para **Name** o campo nome `TurnOn`como.
1. Selecione **Criar**.

O painel central lista as diferentes propriedades de um comando:


| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Sentenças de exemplo | Exemplo declarações o usuário pode dizer para disparar esse comando                                                                 |
| Parâmetros       | Informações necessárias para concluir o comando                                                                                |
| Regras de conclusão | As ações a serem executadas para atender ao comando. Por exemplo, para responder ao usuário ou comunicar-se com outro serviço Web. |
| Regras de interação   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Vamos começar com a seção de sentenças de exemplo e fornecer um exemplo do que o usuário pode dizer.

1. Selecione a seção **sentenças de exemplo** no painel central e no painel mais à direita, Adicione exemplos:

    ```
    turn on the tv
    ```

1. Selecione `Save` o ícone presente na parte superior deste painel.

Por enquanto, não temos parâmetros, portanto, podemos passar para a seção de **regras de conclusão** .

### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Agora, adicione uma regra de conclusão com a configuração a seguir. Essa regra indica ao usuário que uma ação de preenchimento está sendo executada.


| Configuração    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | ConfirmationResponse                  | Um nome que descreve a finalidade da regra          |
| Condições | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
| Ações    | SpeechResponse "-Ok, ligando a TV" | A ação a ser tomada quando a condição da regra for verdadeira |

1. Crie uma nova regra de conclusão selecionando `+Add` o ícone na parte superior do painel central.
1. Forneça o valor na seção **nome** .
1. Adicionar uma ação
   1. Crie uma nova ação selecionando **+ Adicionar uma ação** na seção **ações** .
   1. No pop-up **nova ação** , selecione `Send speech response` nas opções suspensas para **tipo**.
   1. Escolha `Simple editor` para o campo de **resposta** .
       - No primeiro campo de **variação** , forneça valor para resposta como`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta de fala](media/custom-speech-commands/create-speech-response-action.png)

1. Clique em **salvar** para salvar a regra.
1. De volta à seção **regras de conclusão** , selecione **salvar** para salvar todas as alterações. 

> [!div class="mx-imgBorder"]
> ![Criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Experimente

Testar o comportamento usando o painel testar chat
1. Selecione `Train` o ícone presente na parte superior do painel direito.
1. Uma vez, o treinamento é concluído `Test`, selecione. Será exibido um novo **teste da janela do aplicativo** .
    - Você digita: ativar a TV
    - Resposta esperada: Ok, ligando a TV


> [!div class="mx-imgBorder"]
> ![Teste com o Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
