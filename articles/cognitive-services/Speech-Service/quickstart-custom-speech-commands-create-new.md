---
title: 'Início rápido: criar um aplicativo de visualização de comandos personalizados – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará e testará um aplicativo de visualização de comandos personalizados hospedados. O aplicativo irá processar o declarações.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 5b54923bb667053ffc886b335ab9d1b0cf4cb754
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509348"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Início rápido: criar um aplicativo de visualização de comandos personalizados

Neste guia de início rápido, você aprenderá a criar e testar um aplicativo de comandos personalizados.
O aplicativo processará declarações como "ligar a TV" e responderá com uma mensagem simples como "Ok, ligando a TV".

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de Fala do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Neste momento, os comandos personalizados dão suporte apenas a assinaturas de fala nas regiões westus, westus2 e neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ir para o Speech Studio para comandos personalizados

1. Em um navegador da Web, vá para o [Speech Studio](https://speech.microsoft.com/).
1. Insira suas credenciais para entrar no portal.

   A exibição padrão é sua lista de assinaturas de fala.
    > [!NOTE]
    > Se você não vir a página Selecionar assinatura, poderá acessá-la selecionando **recursos de fala** no menu configurações na parte superior da tela.

1. Selecione sua assinatura de fala e, em seguida, selecione **ir para Studio**.
1. Selecione **comandos personalizados**.

     O modo de exibição padrão é uma lista dos aplicativos de comandos personalizados que você tem na assinatura selecionada.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de comandos personalizados

1. Selecione **novo projeto** para criar um projeto.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Na caixa **nome** , insira um nome de projeto.
1. Na lista **idioma** , selecione um idioma.
1. Na lista de **recursos de criação do Luis** , selecione um recurso de criação. Se não houver recursos de criação válidos, crie um selecionando **criar novo recurso de criação de Luis**.

   > [!div class="mx-imgBorder"]
   > ![Criar um recurso](media/custom-speech-commands/create-new-resource.png)

   1. Na caixa **nome do recurso** , digite o nome do recurso.
   1. Na lista **grupo de recursos** , selecione um grupo de recursos.
   1. Na lista **local** , selecione um local.
   1. Na lista **tipo de preço** , selecione uma camada.

      > [!NOTE]
      > Você pode criar um grupo de recursos inserindo um nome de grupo de recursos na caixa **grupo de recursos** . O grupo de recursos será criado quando você selecionar **criar**.

1. Selecione **Criar**.
1. Depois que o projeto for criado, selecione-o.

    Agora você deve ver uma visão geral do seu novo aplicativo de comandos personalizados.

## <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o recurso de criação que você selecionou na janela **novo projeto** e definir um recurso de previsão. O recurso de previsão é usado para reconhecimento quando o aplicativo de comandos personalizados é publicado. Você não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

1. Selecione **configurações** no painel esquerdo e, em seguida, selecione **recursos do Luis** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **criar novo recurso**.
1. Clique em **Salvar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir recursos do LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Como o recurso de criação dá suporte a apenas 1.000 solicitações de ponto de extremidade de previsão por mês, você precisará definir um recurso de previsão LUIS antes de publicar seu aplicativo de comandos personalizados.


## <a name="create-a-command"></a>Criar um comando

Vamos criar um comando simples que usará um único expressão, `turn on the tv` e responderá com a mensagem `Ok, turning on the tv` .

1. Crie um comando selecionando **novo comando** na parte superior do painel esquerdo. A **nova** janela de comando é aberta.
1. Na caixa **nome** , digite **Ativar**.
1. Selecione **Criar**.

O painel central lista as propriedades do comando:


| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Sentenças de exemplo** | Exemplos de declarações que o usuário pode dizer para disparar o comando.                                                                 |
| **Parâmetros**       | Informações necessárias para concluir o comando.                                                                                |
| **Regras de conclusão** | Ações que serão tomadas para atender ao comando. Por exemplo, respondendo ao usuário ou se comunicando com outro serviço Web. |
| **Regras de interação**   | Regras adicionais para lidar com situações mais específicas ou complexas.                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Vamos começar com a seção de **sentenças de exemplo** . Forneceremos um exemplo do que o usuário pode dizer.

1. Selecione **sentenças de exemplo** no painel central. 
1. No painel direito, Adicione exemplos:

    ```
    turn on the tv
    ```

1. Selecione **salvar** na parte superior do painel.

Por enquanto, não temos parâmetros, portanto, podemos mudar para a seção de **regras de conclusão** .

### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Agora, adicione uma regra de conclusão que tenha a configuração a seguir. Essa regra informa ao usuário que uma ação de preenchimento está sendo executada.


| Configuração    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Nome**  | **ConfirmationResponse**                  | Um nome que descreve a finalidade da regra.          |
| **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada.    |
| **Ações**    | **Enviar resposta de fala-> Ok, ligando a TV** | A ação a ser tomada quando a condição da regra for verdadeira. |

1. Crie uma nova regra de conclusão selecionando **Adicionar** na parte superior do painel central.
1. Na caixa **Nome**, insira um nome.
1. Adicione uma ação.
   1. Crie uma ação selecionando **Adicionar uma ação** na seção **ações** .
   1. Na janela **Editar ação** , na lista **tipo** , selecione **Enviar resposta de fala**.
   1. Em **resposta**, selecione **editor simples**. Na primeira caixa de **variação** , digite **OK, ligando a TV**.

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta](media/custom-speech-commands/create-speech-response-action.png)

1. Selecione **salvar** para salvar a regra.
1. De volta à seção **regras de conclusão** , selecione **salvar** para salvar todas as alterações. 

> [!div class="mx-imgBorder"]
> ![Criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Experimentar

Teste o comportamento usando o painel testar chat.
1. Selecione **treinar** na parte superior do painel direito.
1. Após a conclusão do treinamento, selecione **testar**. Será exibida uma janela novo **teste seu aplicativo** .
    - Digite **ligar para a TV**
    - Resposta esperada: **OK, ligando a TV**


> [!div class="mx-imgBorder"]
> ![Testar o comportamento](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
