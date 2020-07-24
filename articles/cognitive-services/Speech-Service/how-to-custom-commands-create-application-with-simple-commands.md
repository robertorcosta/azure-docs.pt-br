---
title: Como criar aplicativos com comandos simples – serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a criar e testar um aplicativo de comandos personalizados hospedado usando comandos simples.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 6552d13ab68d8028cd95b18f2d0895d53bba462c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090953"
---
# <a name="create-application-with-simple-commands"></a>Criar aplicativo com comandos simples

Neste artigo, você aprenderá como:
 - Criar um aplicativo vazio
 - Atualizar recursos do LUIS
 - Adicionar alguns comandos básicos ao aplicativo de comandos personalizados

## <a name="create-empty-application"></a>Criar aplicativo vazio
Crie um aplicativo de comandos personalizados vazio. Para obter detalhes, consulte o guia de [início rápido](quickstart-custom-commands-application.md). Desta vez, em vez de importar um projeto, você cria um projeto em branco.

1. Na caixa **nome** , insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa de sua escolha).
1. Na lista **idioma** , selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS de sua escolha.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o recurso de criação que você selecionou na janela **novo projeto** e definir um recurso de previsão. O recurso de previsão é usado para reconhecimento quando o aplicativo de comandos personalizados é publicado. Você não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

## <a name="add-turnon-command"></a>Adicionar comando de ativação

No aplicativo de comandos personalizados do **ambiente inteligente-Lite** vazio que você acabou de criar, adicione um comando simples que processa um expressão, `turn on the tv` e responde com a mensagem `Ok, turning the tv on` .

1. Crie um novo comando selecionando **novo comando** na parte superior do painel esquerdo. A **nova** janela de comando é aberta.
1. Forneça um valor para o campo **nome** como **ativado**.
1. Selecione **Criar**.

O painel central lista as diferentes propriedades do comando. Você configura as propriedades a seguir do comando. Para obter uma explicação de todas as propriedades de configuração de um comando, acesse [referências](./custom-commands-references.md).

| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Sentenças de exemplo** | Exemplo declarações o usuário pode dizer para disparar esse comando                                                                 |
| **Parâmetros**       | Informações necessárias para concluir o comando                                                                                |
| **Regras de conclusão** | As ações a serem executadas para atender ao comando. Por exemplo, para responder ao usuário ou comunicar-se com outro serviço Web. |
| **Regras de interação**   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Vamos começar com a seção de **sentenças de exemplo** e fornecer um exemplo do que o usuário pode dizer.

1. Selecione a seção **sentenças de exemplo** no painel central.
1. No painel mais à direita, Adicione exemplos:

    ```
    turn on the tv
    ```

1.  Selecione **salvar** na parte superior do painel.

Por enquanto, não temos parâmetros, portanto, podemos mudar para a seção de **regras de conclusão** .

### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Em seguida, o comando precisa ter uma regra de conclusão. Essa regra informa ao usuário que uma ação de preenchimento está sendo executada. Para ler mais sobre regras e regras de conclusão, acesse [referências](./custom-commands-references.md).

1. Selecione regra de conclusão padrão **concluída** e edite-a da seguinte maneira:

    
    | Configuração    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | ConfirmationResponse                  | Um nome que descreve a finalidade da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | Enviar resposta de fala > editor simples > primeira variação >`Ok, turning the tv on` | A ação a ser tomada quando a condição da regra for verdadeira |
    



   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta de fala](media/custom-commands/create-speech-response-action.png)

1. Selecione **salvar** para salvar a ação.
1. De volta à seção **regras de conclusão** , selecione **salvar** para salvar todas as alterações. 

    > [!NOTE]
    > Não é necessário usar a regra de conclusão padrão que vem com o comando. Se necessário, você pode excluir a regra de conclusão padrão existente e adicionar sua própria regra.

### <a name="try-it-out"></a>Experimente

Testar o comportamento usando o painel testar chat
1. Selecione o ícone de **treinamento** presente na parte superior do painel direito.
1. Uma vez, o treinamento é concluído, selecione **testar**. Experimente os seguintes expressão por meio de voz/texto:
    - Entrada: ativar a TV
    - Saída: Ok, ligando a TV


> [!div class="mx-imgBorder"]
> ![Teste com o Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, você pode selecionar **Ativar detalhes** para obter informações sobre como essa entrada de voz/texto foi processada.  

## <a name="add-settemperature-command"></a>Adicionar comando settemperatura

Agora, adicione mais um comando **settemperaturas** que usará um único expressão, `set the temperature to 40 degrees` e responda com a mensagem `Ok, setting temperature to 40 degrees` .

Siga as etapas conforme ilustrado para o comando de **ativação** para criar um novo comando usando a sentença de exemplo "**definir a temperatura como 40 graus**".

Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome  | ConfirmationResponse                  |
| Condições | Nenhum                                     |
| Ações    | Enviar resposta de fala > editor simples > primeira variação >`Ok, setting temperature to 40 degrees` |

Selecione **salvar** para salvar todas as alterações no comando.

## <a name="add-setalarm-command"></a>Adicionar comando setAlarm
Crie um novo comando **setAlarm** usando a sentença de exemplo "**set a Alarm for 9 AM amanhã**". Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome da Regra  | ConfirmationResponse                  |
| Condições | Nenhum                                     |
| Ações    | Enviar resposta de fala > editor simples > primeira variação >`Ok, setting an alarm for 9 am tomorrow` |

Selecione **salvar** para salvar todas as alterações no comando.

## <a name="try-it-out"></a>Experimente

Testar o comportamento usando o painel testar chat
1. Selecione **Treinar**. Após o êxito do treinamento, selecione **teste** e experimente:
    - Digite: defina a temperatura como 40 graus
    - Resposta esperada: Ok, definindo a temperatura como 40 graus
    - Você digita: ativar a TV
    - Resposta esperada: Ok, ativando a TV
    - Você digita: definir um alarme para 9h, amanhã
    - Resposta esperada: Ok, configurando um alarme para 9h, amanhã

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: adicionar parâmetros a comandos](./how-to-custom-commands-add-parameters-to-commands.md)
