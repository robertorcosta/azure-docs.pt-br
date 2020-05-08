---
title: 'Como: adicionar uma correção de uma etapa a um comando personalizado (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como implementar correções de uma etapa para um comando em comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858257"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Como: adicionar uma correção de uma etapa a um comando personalizado (versão prévia)

Neste artigo, você aprenderá a adicionar a confirmação de uma etapa a um comando.

A correção de uma etapa é usada para atualizar um comando que acabou de ser concluído. Ou seja, se você apenas configurou um alarme, poderá mudar de ideia e atualizar a hora do alarme. Um exemplo de tal caso é o seguinte:

- Entrada: Definir alarme para amanhã às 12h
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: não, amanhã às às 13:00
- Saída: Ok

Um cenário do mundo real, que é geralmente acompanhado pelo cliente executando uma ação como resultado da conclusão do comando – este artigo pressupõe que você como desenvolvedor tem um mecanismo para atualizar o alarme em seu aplicativo de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:
> [!div class="checklist"]

> * [Início rápido: criar um comando personalizado (versão prévia)](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Como: adicionar uma confirmação a um comando personalizado (visualização)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Adicionar regras de interação para a correção de uma etapa 

Para demonstrar a correção de uma etapa, Vamos estender o comando **setAlarm** criado em [como: adicionar uma confirmação a um comando personalizado (versão prévia)](./how-to-custom-speech-commands-confirmations.md).
1. Adicione uma regra de interação para atualizar o alarme definido anteriormente. 

    Essa regra solicitará que o usuário confirme a data e a hora do alarme e está esperando uma confirmação (Sim/não) para a próxima vez.

   | Configuração               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Atualizar alarme anterior                                            | Um nome que descreve a finalidade da regra          |
   | Condições            | O comando anterior precisa ser atualizado & parâmetro necessário-> DateTime                | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | Enviar resposta de fala-> editor simples – > atualizar o alarme anterior para {DateTime}      | A ação a ser tomada quando as condições da regra forem verdadeiras |
   | Estado de pós-execução | Comando concluído        | Estado do usuário após a ativação                   |

1. Mova a regra que você acabou de criar para a parte superior das regras de interação (selecione a regra no painel e clique na seta `...` para cima presente em ícone na parte superior do painel central).
   > [!div class="mx-imgBorder"]
   > ![Adicionar uma validação de intervalo](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > Em um aplicativo real, na seção ações dessa regra, você também enviará uma atividade para o cliente ou chamará um ponto de extremidade HTTP para atualizar o alarme em seu sistema.

## <a name="try-it-out"></a>Experimente

Selecione `Train`, aguarde a conclusão do treinamento e selecione `Test`.

- Entrada: Definir alarme para amanhã às 12h
- Saída: tem certeza de que deseja definir um alarme para 2020-05-02 12:00:00
- Entrada: Sim
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: não, amanhã às 14h
- Saída: atualizando o alarme anterior para 2020-05-02 14:00:00
