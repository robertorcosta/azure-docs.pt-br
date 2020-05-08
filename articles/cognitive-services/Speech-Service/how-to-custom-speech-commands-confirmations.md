---
title: 'Como: adicionar uma confirmação a um comando personalizado (visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, como implementar confirmações para um comando em comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858216"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Como: adicionar uma confirmação a um comando personalizado (visualização)

Neste artigo, você aprenderá a adicionar uma confirmação a um comando.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:
> [!div class="checklist"]
> *  [Início rápido: criar um comando personalizado (versão prévia)](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Criar um comando setAlarm

Para demonstrar confirmações, vamos criar um novo comando, permitindo que o usuário defina um alarme.

1. Abra o aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/).
1. Crie um novo comando `SetAlarm`.
1. Adicione um parâmetro chamado `DateTime` com a configuração a seguir.

   | Configuração                           | Valor sugerido                     |  Descrição                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Nome                              | Datetime                                | Um nome descritivo para o parâmetro                                |
   | Obrigatório                          | checked                                 | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | Resposta para o parâmetro obrigatório   | Editor simples – > qual tempo?                              | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
   | Type                              | Datetime                                | O tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |
   | Padrões de data                     | Se a data estiver ausente, use hoje mesmo            | Valor padrão da variável a ser usado se não for fornecido pelo usuário.  |  
   | Padrões de tempo                     | Se o tempo estiver ausente, use o início do dia     |  Valor padrão da variável a ser usado se não for fornecido pelo usuário.|

1. Adicione algumas frases de exemplo.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicione uma regra de conclusão para confirmar o resultado.

   | Configuração    | Valor sugerido                               |Descrição                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Nome da Regra  | Definir alarme                                               |    Um nome que descreve a finalidade da regra |
   | Ações    | Enviar resposta de fala-Ok, alarme definido para {DateTime} "    |A ação a ser tomada quando a condição da regra for verdadeira

## <a name="try-it-out"></a>Experimente

1. Selecione `Train` o ícone presente na parte superior do painel direito.

1. Quando o treinamento for concluído, `Test`selecione.
    - Entrada: Definir alarme para amanhã às 12h
    - Saída: Ok, alarme definido para 2020-05-02 12:00:00
    - Entrada: definir um alarme
    - Saída: qual hora?
    - Entrada: 17:00
    - Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Adicionar as regras avançadas para confirmação

As confirmações são obtidas por meio da adição de regras de interações.

1. No comando existente `SetAlarm` , adicione um ícone de **regra** de interação `+Add` por seleção no painel central e, em seguida, selecione o comando **regras** -> de interação**confirmar**.

    Essa regra solicitará que o usuário confirme a data e a hora do alarme e está esperando uma confirmação (Sim/não) para a próxima vez.

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmar data e hora                                                                | Um nome que descreve a finalidade da regra          |
   | Condições            | Parâmetro necessário-> DateTime                                                    | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | Enviar resposta de fala-> tem certeza de que deseja definir um alarme para {DateTime}?     | A ação a ser tomada quando a condição da regra for verdadeira |
   | As          | Esperando confirmação do usuário                                                 | Expectativa para a próxima rodada                      |
   | Estado de pós-execução  | Aguardar a entrada do usuário                                                            | Estado do usuário após a ativação                  |
  
      > [!div class="mx-imgBorder"]
      > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-validation-set-temperature.png)

1. Adicione outra regra de interação para tratar de uma confirmação bem-sucedida (o usuário disse sim)

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação aceita                                                            | Um nome que descreve a finalidade da regra          |
   | Condições            | A confirmação foi bem-sucedida & parâmetro necessário-> DateTime                      | Condições que determinam quando a regra pode ser executada    |   
   | Estado de pós-execução | Executar regras de conclusão                                                          | Estado do usuário após a ativação                   |

1. Adicione uma regra avançada para lidar com uma confirmação negada (o usuário não disse)

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação negada                                                                   | Um nome que descreve a finalidade da regra          |
   | Condições            | A confirmação foi negada & parâmetro necessário-> DateTime                               | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | Limpar valor do parâmetro-> DateTime & enviar resposta de fala-> sem problema, em que tempo?  | A ação a ser tomada quando a condição da regra for verdadeira |
   | Estado após a execução | Esperar entrada                                                                   | Estado do usuário após a ativação                   |
   | As          | Esperando entrada (ões) parâmetros da data e hora do > do usuário                           | Expectativa para a próxima rodada                      |

## <a name="try-out-the-changes"></a>Experimente as alterações

Selecione `Train`, aguarde a conclusão do treinamento e `Test`selecione.

- Entrada: Definir alarme para amanhã às 12h
- Saída: tem certeza de que deseja definir um alarme para 2020-05-02 12:00:00?
- Entrada: não
- Saída: sem problema, que tempo então?
- Entrada: 17:00
- Saída: "tem certeza de que deseja definir um alarme para 2020-05-01 17:00:00?"
- Entrada: Sim
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: adicionar uma correção de uma etapa a um comando personalizado (versão prévia)](./how-to-custom-speech-commands-one-step-correction.md)