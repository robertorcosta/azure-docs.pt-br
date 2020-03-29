---
title: 'Como: Adicionar uma correção de uma etapa a um comando personalizado (Preview) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como implementar correções de uma etapa para um comando em Comandos Personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456449"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Como: Adicionar uma correção de uma etapa a um comando personalizado (Visualização)

Neste artigo, você aprenderá como adicionar confirmação de uma etapa a um comando.

A correção de uma etapa é usada para atualizar um comando que acabou de ser concluído.

Ou seja, se você configurar um alarme, você pode mudar de idéia e atualizar a hora do alarme.

- Entrada: Afina o alarme para amanhã ao meio-dia
- Saída: "Ok, alarme definido para 12/06/2019 12:00:00"
- Entrada: Não, amanhã às 13h
- Saída: "Ok

Tenha em mente que isso implica que você, como desenvolvedor, tenha um mecanismo para atualizar o alarme em seu aplicativo backend.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter completado as etapas nos seguintes artigos:

- [Quickstart: Crie um comando personalizado (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)
- [Como: Adicionar uma confirmação a um comando personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Adicione as regras avançadas para correção de uma etapa 

Para demonstrar uma correção em uma etapa, vamos estender o comando **SetAlarm** criado no [Sistema confirmações como](./how-to-custom-speech-commands-confirmations.md).
 
1. Adicione uma regra avançada para atualizar o alarme anterior. 

    Esta regra pedirá ao usuário para confirmar a data e a hora do alarme e está esperando uma confirmação (sim/não) para a próxima volta.

   | Configuração               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Atualizar alarme anterior                                            | Um nome descrevendo o propósito da regra          |
   | Condições            | UpdateLastCommand & parâmetro necessário - Hora de data                | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | SpeechResponse - "- Atualizando o alarme anterior para {DateTime}"       | A ação a tomar quando a condição de regra é verdadeira |
   | Estado após execução | Comando completo                                                 | Estado do usuário após a curva                   |

1. Mova a regra que você acabou de criar para o topo de regras avançadas (role sobre a regra no painel e clique na seta UP).
   > [!div class="mx-imgBorder"]
   > ![Adicionar uma validação de intervalo](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Em um aplicativo real, na seção Ações desta regra, você também enviará de volta uma atividade para o cliente ou chamará um ponto final HTTP para atualizar o alarme em seu sistema.

## <a name="try-it-out"></a>Experimentar

Selecione o painel Teste e tente algumas interações.

- Entrada: Afina o alarme para amanhã ao meio-dia
- Saída: "Tem certeza de que deseja configurar um alarme para 12/07/2019 12:00:00?"
- Entrada: Sim
- Saída: "Ok, alarme definido para 12/07/2019 12:00:00"
- Entrada: Não, amanhã às 13h
- Saída: "Atualização do alarme anterior para 12/07/2019 13:00:00"
