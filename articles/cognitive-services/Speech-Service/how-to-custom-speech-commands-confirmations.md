---
title: 'Como: Adicionar uma confirmação a um comando personalizado (Visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, como implementar confirmações para um comando em Comandos Personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456493"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Como: Adicionar uma confirmação a um comando personalizado (Pré-visualização)

Neste artigo, você aprenderá como adicionar uma confirmação a um comando.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter completado as etapas nos seguintes artigos:

- [Quickstart: Crie um comando personalizado (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Crie um comando SetAlarm

Para demonstrar validações, vamos criar um novo Comando permitindo que o usuário defina um alarme.

1. Abra seu aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Crie um novo **SetAlarm** de comando
1. Adicionar um parâmetro chamado DateTime

   | Configuração           | Valor sugerido                                          | Descrição                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nome              | Datetime                                                 | Um nome descritivo para seu parâmetro de comando                                                    |
   | Obrigatório          | true                                                     | Caixa de seleção indicando se um valor para este parâmetro é necessário antes de completar o Comando |
   | Modelo de resposta | "- Que horas?"                                           | Um aviso para pedir o valor deste parâmetro quando ele não é conhecido                              |
   | Type              | Datetime                                                 | O tipo de parâmetro, como número, string ou data                                      |
   | Padrões de data     | Se a data está faltando uso hoje                             |                                                                                                  |
   | Padrões de tempo     | Se falta tempo, o uso começa o dia                      |                                                                                                  | 

1. Adicione algumas frases de exemplo
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicionar uma regra de conclusão para confirmar o resultado

   | Configuração    | Valor sugerido                                         | Descrição                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Definir alarme                                               | Um nome descrevendo o propósito da regra          |
   | Ações    | SpeechResponse - "- Ok, configuração de alarme para {DateTime}"       | A ação a tomar quando a condição de regra é verdadeira |

## <a name="try-it-out"></a>Experimentar

Selecione o painel Teste e tente algumas interações.

- Entrada: Afina o alarme para amanhã ao meio-dia
- Saída: "Ok, alarme definido para 12/06/2019 12:00:00"

- Entrada: Defina um alarme
- Saída: "Que horas?"
- Entrada: 17h
- Saída: "Ok, alarme definido para 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Adicione as regras avançadas para confirmação

1. Adicione uma regra avançada para confirmação. 

    Esta regra pedirá ao usuário para confirmar a data e a hora do alarme e está esperando uma confirmação (sim/não) para a próxima volta.

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmar a data                                                                | Um nome descrevendo o propósito da regra          |
   | Condições            | Parâmetro necessário - DataTime                                                    | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | SpeechResponse - "- Tem certeza de que deseja definir um alarme para {DateTime}?"       | A ação a tomar quando a condição de regra é verdadeira |
   | Estado após execução | Esperar entrada                                                                   | Estado para o usuário após a volta                  |
   | Expectativas          | Confirmação                                                                     | Expectativa para a próxima volta                      |

1. Adicione uma regra avançada para lidar com uma confirmação bem sucedida (o usuário disse sim)

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação aceita                                                            | Um nome descrevendo o propósito da regra          |
   | Condições            | SucessoConfirmação & parâmetro necessário - Hora de data                           | Condições que determinam quando a regra pode ser executada    |   
   | Estado após execução | Pronto para conclusão                                                             | Estado do usuário após a curva                   |

1. Adicionar uma regra avançada para lidar com uma confirmação negada (o usuário disse não)

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Negado confirmar                                                                   | Um nome descrevendo o propósito da regra          |
   | Condições            | NegadoConfirmação & parâmetro necessário - DataTime                               | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | ClearParameter - DateTime & SpeechResponse - "- Sem problema, que horas então?"     | A ação a tomar quando a condição de regra é verdadeira |
   | Estado após execução | Esperar entrada                                                                   | Estado do usuário após a curva                   |
   | Expectativas          | ElicitParameters - DateTime                                                      | Expectativa para a próxima volta                      |

## <a name="try-it-out"></a>Experimentar

Selecione o painel Teste e tente algumas interações.

- Entrada: Afina o alarme para amanhã ao meio-dia
- Saída: "Tem certeza de que deseja configurar um alarme para 12/07/2019 12:00:00?"
- Entrada: Não
- Saída: "Sem problema, que horas então?"
- Entrada: 17h
- Saída: "Tem certeza de que deseja configurar um alarme para 12/06/2019 17:00:00?"
- Entrada: Sim
- Saída: "Ok, alarme definido para 12/06/2019 17:00:00"

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Adicionar uma correção de uma etapa a um comando personalizado (Visualização)](./how-to-custom-speech-commands-one-step-correction.md)