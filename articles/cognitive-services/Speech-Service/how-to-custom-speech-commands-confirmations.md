---
title: Adicionar confirmações em um aplicativo de visualização de comandos personalizados-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar confirmações a comandos em um aplicativo de visualização de comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310461"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Adicionar confirmações a um comando em um aplicativo de visualização de comandos personalizados

Neste artigo, você aprenderá a criar confirmações para comandos em um aplicativo de visualização de comandos personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas nos seguintes artigos:
> [!div class="checklist"]
> * [Início rápido: criar um aplicativo de visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Início rápido: criar um aplicativo de visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Criar um comando setAlarm

Para demonstrar confirmações, crie um novo comando que defina um alarme.

1. No [Speech Studio](https://speech.microsoft.com/), abra o aplicativo de visualização de comandos personalizados que você criou.
1. Crie um novo comando **setAlarm** .
1. Adicione um parâmetro **DateTime** que tenha a seguinte configuração:

   | Configuração                           | Valor sugerido                     |  Descrição                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Nome**                              | **DateTime**                                | Nome descritivo para o parâmetro                                |
   | **Necessária**                          | Verificado                                 | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | **Resposta para um parâmetro obrigatório**   | **Editor simples – > qual tempo?**                              | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
   | **Tipo**                              | **DateTime**                                | Tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |
   | **Padrões de data**                     | Se a data estiver ausente, use a data de hoje            | Valor padrão da variável a ser usada se não for fornecido pelo usuário  |  
   | **Padrões de tempo**                     | Se o tempo estiver ausente, use o início do dia     |  Valor padrão da variável a ser usada se não for fornecido pelo usuário|

1. Adicione algumas frases de exemplo.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicione uma regra de conclusão para confirmar o resultado. Use a seguinte configuração:

   | Configuração    | Valor sugerido                               |Descrição                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Nome da regra**  | **Definir alarme**                                               |    Um nome que descreve a finalidade da regra |
   | **Ações**    | **Enviar resposta de fala-> OK, alarme definido para {DateTime}**    |A ação a ser tomada quando a condição da regra for verdadeira

## <a name="try-it-out"></a>Experimentar

1. Selecione **treinar** na parte superior do painel direito.

1. Após a conclusão do treinamento, selecione **teste**e tente as seguintes interações:
    - Entrada: Definir alarme para amanhã às 12h
    - Saída: OK, alarme definido para 2020-05-02 12:00:00
    - Entrada: definir um alarme
    - Saída: qual hora?
    - Entrada: 17:00
    - Saída: OK, alarme definido para 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Adicionar regras de interação para a confirmação

Crie confirmações adicionando regras de interação.

1. No comando **setAlarm** , selecione **Adicionar** no painel central e, em seguida, selecione o comando **regras de interação**  >  **confirmar**.

    Essa regra solicita que o usuário confirme a data e a hora do alarme. Use estas configurações:

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmar data e hora**                                                                | Um nome que descreve a finalidade da regra          |
   | **Condições**            | **Parâmetro necessário-> DateTime**                                                    | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Enviar resposta de fala-> tem certeza de que deseja definir um alarme para {DateTime}?**     | A ação a ser tomada quando a condição da regra for verdadeira |
   | **As**          | **Esperando confirmação do usuário**                                                 | Expectativa para a próxima rodada                      |
   | **Estado de pós-execução**  | **Aguardar a entrada do usuário**                                                            | Estado do usuário após a ativação                  |
  
      > [!div class="mx-imgBorder"]
      > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-validation-set-temperature.png)

1. Adicione uma regra de interação para uma confirmação aceita (o usuário disse "Sim"). Use a seguinte configuração:

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmação aceita**                                                            | Um nome que descreve a finalidade da regra          |
   | **Condições**            | **A confirmação foi bem-sucedida & parâmetro necessário-> DateTime**                      | Condições que determinam quando a regra pode ser executada    |   
   | **Estado de pós-execução** | **Executar regras de conclusão**                                                          | Estado do usuário após a ativação                   |

1. Adicione uma regra de interação para uma confirmação negada (o usuário disse "não"). Use a seguinte configuração:

   | Configuração               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmação negada**                                                                   | Um nome que descreve a finalidade da regra          |
   | **Condições**            | **A confirmação foi negada & parâmetro necessário-> DateTime**                               | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Limpar valor do parâmetro-> DateTime & enviar resposta de fala-> sem problema, em que tempo?**  | A ação a ser tomada quando a condição da regra for verdadeira |
   | **Estado após a execução** | **Esperar entrada**                                                                   | Estado do usuário após a ativação                   |
   | **As**          | **Esperando entrada de parâmetros do > DateTime do usuário**                           | Expectativa para a próxima rodada                      |

## <a name="try-out-the-changes"></a>Experimente as alterações

1. Selecione **Treinar**.

1. Após a conclusão do treinamento, selecione **teste**e tente estas interações:

    - Entrada: Definir alarme para amanhã às 12h
    - Saída: tem certeza de que deseja definir um alarme para 2020-05-02 12:00:00?
    - Entrada: não
    - Saída: sem problema, que tempo então?
    - Entrada: 17:00
    - Saída: "tem certeza de que deseja definir um alarme para 2020-05-01 17:00:00?"
    - Entrada: Sim
    - Saída: OK, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma correção de uma etapa a um comando em um aplicativo de visualização de comandos personalizados](./how-to-custom-speech-commands-one-step-correction.md)
