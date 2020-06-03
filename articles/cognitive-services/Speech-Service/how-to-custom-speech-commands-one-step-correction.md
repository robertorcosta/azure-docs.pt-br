---
title: Adicionar correção de uma etapa em comandos personalizados visualização-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar uma correção de uma etapa para um comando em um aplicativo de visualização de comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310420"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Adicionar uma correção de uma etapa a um comando personalizado em um aplicativo de visualização de comandos personalizados

Neste artigo, você aprenderá a adicionar a confirmação de uma etapa a um comando em um aplicativo de visualização de comandos personalizados.

A correção de uma etapa é usada para atualizar um comando que acabou de ser concluído. Ao adicionar uma correção de uma etapa a um comando de alarme, você pode mudar de ideia e atualizar a hora do alarme. Por exemplo:

- Entrada: Definir alarme para amanhã às 12h
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: não, amanhã às às 13:00
- Saída: Ok

> [!NOTE]
> Em um cenário do mundo real, o cliente executa uma ação como resultado da conclusão do comando. Este artigo pressupõe que você tenha um mecanismo para atualizar o alarme em seu aplicativo de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas nos seguintes artigos:
> [!div class="checklist"]

> * [Início rápido: criar um aplicativo de visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
> * [Como: Adicionar confirmações a um comando em um aplicativo de visualização de comandos personalizados](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Adicionar regras de interação para a correção de uma etapa

Para demonstrar a correção de uma etapa, estenda o comando **setAlarm** criado em [como: adicionar uma confirmação a um comando na visualização de comandos personalizados](./how-to-custom-speech-commands-confirmations.md).

1. Adicione uma regra de interação para atualizar o comando **setAlarm** .

    Essa regra solicita que o usuário confirme a data e a hora do alarme e espera uma confirmação (Sim/não) para a próxima vez.

   | Configuração               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Atualizar alarme anterior**                                            | Um nome que descreve a finalidade da regra          |
   | **Condições**            | **O comando anterior precisa ser atualizado & parâmetro necessário-> DateTime**                | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Enviar resposta de fala-> editor simples – > atualizar o alarme anterior para {DateTime}**      | A ação a ser tomada quando as condições da regra forem verdadeiras |
   | **Estado de pós-execução** | **Comando concluído**        | Estado do usuário após a ativação                   |

1. No painel, selecione a regra de interação que você acabou de criar. Selecione o botão de reticências (**...**) no canto superior esquerdo do painel. Em seguida, use a seta para **cima** para mover a regra para a parte superior da lista de **regras de interação** .
   > [!div class="mx-imgBorder"]
   > ![Adicionar uma validação de intervalo](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > Em um aplicativo do mundo real, use a seção **ações** para enviar de volta uma atividade ao cliente ou chamar um ponto de extremidade http para atualizar o alarme em seu sistema.

## <a name="try-it-out"></a>Experimentar

1. Selecione **Treinar**.

1. Após a conclusão do treinamento, selecione **teste**e tente estas interações:

   - Entrada: Definir alarme para amanhã às 12h
   - Saída: tem certeza de que deseja definir um alarme para 2020-05-02 12:00:00
   - Entrada: Sim
   - Saída: Ok, alarme definido para 2020-05-02 12:00:00
   - Entrada: não, amanhã às 14h
   - Saída: atualizando o alarme anterior para 2020-05-02 14:00:00
