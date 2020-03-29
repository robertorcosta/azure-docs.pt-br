---
title: 'Quickstart: Crie um comando personalizado (Preview) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria e testa um aplicativo de Comandos Personalizados hospedado.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155580"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Quickstart: Crie um comando personalizado (Preview)

Neste artigo, você aprenderá como criar e testar um aplicativo de Comandos Personalizados hospedado.
O aplicativo reconhecerá um enunciado como "ligue a TV" e responderá com uma simples mensagem "Ok, ligando a TV".

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura de discurso.

Se você não tiver uma assinatura de fala, você pode criar uma navegando para o [Speech Studio](https://speech.microsoft.com/) e selecionando Criar um recurso **de fala**.

  > [!div class="mx-imgBorder"]
  > [![Criar um](media/custom-speech-commands/create-new-subscription.png) projeto](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Durante a pré-visualização, apenas a região de Westus2 é suportada.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vá para o Speech Studio para comandos personalizados

1. Abra seu navegador e navegue até o [Speech Studio](https://speech.microsoft.com/)
1. Digite suas credenciais para entrar no portal

   - A exibição padrão é a sua lista de assinaturas de discurso
     > [!NOTE]
     > Se você não ver a página de assinatura selecionada, você pode navegar por lá escolhendo "Recursos de fala" no menu de configurações na barra superior.

1. Selecione sua assinatura de discurso e selecione **Ir para o estúdio**
1. Selecione **comandos personalizados (Visualização)**

A exibição padrão é uma lista dos aplicativos De comandos personalizados criados.

## <a name="create-a-custom-commands-project"></a>Crie um projeto de Comandos Personalizados

1. Selecione **novo projeto** para criar um novo projeto

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Digite o nome e o idioma do projeto.
1. Selecione um recurso de autoria. Se não houver recursos de autoria válidos, crie um selecionando **Criar novo recurso**.

   > [!div class="mx-imgBorder"]
   > ![Criar um recurso](media/custom-speech-commands/create-new-resource.png)

   1. Digite o nome do recurso, grupo, localização e nível de preços.

         > [!NOTE]
         > Você pode criar grupos de recursos inserindo o nome do grupo de recursos desejado no campo "Grupo de recursos". O grupo de recursos será criado quando **criar** for selecionado.

1. Clique **em Criar** para criar seu projeto.
1. Uma vez criado, selecione seu projeto.

Sua visão agora deve ser uma visão geral do seu aplicativo de Comandos Personalizados.

## <a name="update-luis-resources-optional"></a>Atualizar os recursos do LUIS (opcional)

Você pode atualizar o recurso de autoria definido na nova janela do projeto e definir um recurso de previsão usado para reconhecer entradas durante o tempo de execução.

> [!NOTE]
> Você precisará definir um recurso de previsão antes que seu aplicativo solicite previsões além das 1.000 solicitações fornecidas pelo recurso de autoria.

> [!div class="mx-imgBorder"]
> ![Definir recursos LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Navegue até o painel LUIS Resources selecionando **Configurações** do painel esquerdo e, em seguida, **LUIS Resources** do painel médio.
1. Selecione um recurso de previsão ou crie um selecionando **Criar novo recurso**
1. Selecione **Salvar**

## <a name="create-a-new-command"></a>Criar um novo Comando

Agora você pode criar um comando. Vamos usar um exemplo que pegará uma `turn on the tv`única expressão e `Ok, turning on the TV`responderá com a mensagem .

1. Crie um novo Comando `+` selecionando o ícone ao lado dos comandos e dê-lhe o nome`TurnOn`
1. Selecione **Salvar**

> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)

Um Comando é um conjunto de:

| Agrupar            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Sentenças de amostra | Exemplos de declarações que o usuário pode dizer para acionar este Comando                                                                 |
| Parâmetros       | Informações necessárias para completar o Comando                                                                                |
| Regras de conclusão | As ações a serem tomadas para cumprir o Comando. Por exemplo, para responder ao usuário ou se comunicar com outro serviço web |
| Regras Avançadas   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |

### <a name="add-a-sample-sentence"></a>Adicionar uma frase de amostra

Vamos começar com frases de exemplo e fornecer um exemplo do que o usuário pode dizer:

```
turn on the tv
```

Por enquanto, não temos parâmetros para seguirmos para as Regras de Conclusão.

### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Agora adicione uma Regra de Conclusão para responder ao usuário indicando que uma ação está sendo tomada.

1. Crie uma nova regra de `+` conclusão selecionando o ícone ao lado de Regras de Conclusão
1. Digite o nome da regra
1. Adicionar uma ação
   1. Crie uma nova Ação de `+` Resposta à Fala selecionando o ícone ao lado de Ações e selecione`SpeechResponse`
   1. Digite a resposta

   > [!NOTE]
   > O texto regular deve começar com um traço. Para mais detalhes, acesse [aqui](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Crie uma resposta de fala](media/custom-speech-commands/create-speech-response-action.png)

1. Clique **em Salvar** para salvar a regra

> [!div class="mx-imgBorder"]
> ![Crie uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Configuração    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | "Resposta à confirmação"                   | Um nome descrevendo o propósito da regra          |
| Condições | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
| Ações    | SpeechResponse "- Ok, ligando a TV" | A ação a tomar quando a condição de regra é verdadeira |

## <a name="try-it-out"></a>Experimentar

Teste o comportamento usando o painel de bate-papo Teste.

> [!div class="mx-imgBorder"]
> ![Teste com web chat](media/custom-speech-commands/create-basic-test-chat.png)

- Você digita: "ligue a TV"
- Resposta esperada: "Ok, ligando a TV"

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)
