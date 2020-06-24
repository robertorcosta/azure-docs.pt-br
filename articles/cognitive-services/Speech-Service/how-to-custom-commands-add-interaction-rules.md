---
title: 'Como: adicionar uma confirmação a um comando personalizado'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a implementar confirmações para um comando em comandos personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307407"
---
# <a name="add-interaction-rules"></a>Adicionar regras de interação

Neste artigo, você aprenderá sobre **as regras de interação**. Essas são regras adicionais para lidar com situações mais específicas ou complexas. Embora você esteja livre para criar suas próprias regras de interação personalizadas, neste artigo, você faz uso de regras de interação para os seguintes cenários de destino:

* Confirmando comandos
* Adicionando uma correção de uma etapa aos comandos

Acesse a seção [referências](./custom-commands-references.md) para saber mais sobre regras de interação.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:
> [!div class="checklist"]
> * [Como: criar aplicativo com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: adicionar parâmetros a comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o comando **settemperaturas** . Para obter a confirmação, você cria regras de interação usando as etapas a seguir.

1. Selecione o comando **settemperaturas** no painel esquerdo.
2. Adicione regras de interação selecionando **Adicionar** no painel central e, em seguida, selecionando **regras de interação**  >  **confirmar comando**.

    Isso adicionará três regras de interação. essa regra solicitará que o usuário confirme a data e a hora do alarme e está esperando uma confirmação (Sim/não) para a próxima rodada.

    1. Modificar a regra de interação de **comando Confirm** de acordo com a configuração a seguir
        1. Renomeie o **nome** para **`Confirm Temperature`** .
        1. Adicionar uma nova condição como **parâmetros obrigatórios > temperatura**
        1. Adicione uma nova ação como **tipo > enviar resposta de fala > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. Deixe o valor padrão de **confirmação esperada do usuário** na seção expectativas.
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifique a regra de interação de **confirmação com êxito** para tratar de uma confirmação bem-sucedida (o usuário disse sim).
      
          1. Modifique o **nome** para **`Confirmation temperature succeeded`** .
          1. Deixe que a confirmação já existente **tenha sido bem-sucedida** .
          1. Adicione uma nova condição como **tipo > parâmetros necessários > temperatura**
          1. Deixe o valor padrão do **estado de pós-execução** como **regras de conclusão de execução**.

    1. Modifique a **confirmação negada** (o usuário não disse) para lidar com cenários quando a confirmação for negada.

          1. Modifique o **nome** para **`Confirmation temperature denied`** .
          1. Deixe a condição já existente com a **confirmação negada** .
          1. Adicione uma nova condição como **tipo > parâmetros necessários > temperatura**
          1. Adicione uma nova ação como **tipo > enviar resposta de fala > `No problem. What temperature then?` **
          1. Deixe o valor padrão do **estado de pós-execução** como **aguardar a entrada do usuário**.

> [!IMPORTANT]
> Neste artigo, você usou o recurso de confirmação embutido. Como alternativa, você também pode obter o mesmo adicionando as regras de interação uma por uma, manualmente.
   

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde a conclusão do treinamento e selecione **testar**.

- Entrada: defina a temperatura como 80 graus
- Saída: Ok 80?
- Entrada: não
- Saída: sem problema. Qual temperatura?
- Entrada: 83 graus
- Saída: Ok 83?
- Entrada: Sim
- Saída: Ok, definindo a temperatura como 83 graus


## <a name="implementing-corrections-in-a-command"></a>Implementando correções em um comando

Nesta seção, você configura a correção de uma etapa, que é usada quando a ação de preenchimento já foi executada. Você também verá um exemplo de como a correção é habilitada por padrão, caso o comando ainda não seja atendido. Para adicionar uma correção quando o comando não for concluído, adicione um novo parâmetro **AlarmTone**.

Selecione o comando **setAlarm** no painel esquerdo e adicione um novo parâmetro **AlarmTone**.
        
- **Nomes** > `AlarmTone`
- > cadeia de caracteres de **tipo**
- **Valor padrão** > `Chimes`
- **Configuração** > aceitar valores de entrada predefinidos do catálogo interno
- **Valores de entrada predefinidos**  >  `Chimes` , `Jingle` e `Echo` . Cada como entradas predefinidas individuais.


Em seguida, atualize a resposta para o parâmetro DateTime para `Ready to set alarm with tone as {AlarmTone}. For what time?` . Em seguida, modifique a regra de conclusão da seguinte maneira.

1. Selecione a regra de conclusão **ConfirmationResponse**existente.
1. No painel direito, passe o mouse sobre a ação existente e selecione o botão **Editar** .
1. Atualizar resposta de fala para`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.
Experimente o seguinte declarações:

- Entrada: definir um alarme
- Saída: pronto para definir o alarme com Tom como Chimes. Por que tempo?
- Entrada: defina um alarme com Tom como jingle para 9h, amanhã
- Saída: Ok, alarme definido para 2020-05-30 09:00:00. O tom de alarme é jingle.

> [!IMPORTANT]
> Observe como o tom de alarme pode ser alterado sem nenhuma configuração explícita em um comando contínuo, ou seja, quando o comando ainda não foi concluído. **A correção é habilitada por padrão para todos os parâmetros de comando, independentemente do número de desativação, caso o comando ainda seja atendido.**

### <a name="correction-when-command-is-completed"></a>Correção quando o comando é concluído

A plataforma de comandos personalizados também fornece a capacidade de correção de uma etapa, mesmo quando o comando tiver sido concluído. Mas esse recurso não está habilitado por padrão e deve ser configurado explicitamente. Use as etapas a seguir para configurar a correção de uma etapa.

1. No comando **setAlarm** , adicione uma regra de interação do tipo **Atualizar comando anterior** para atualizar o alarme definido anteriormente. Renomeie o **nome** padrão da regra de interação para **atualizar o alarme anterior**.
1. Deixe a condição padrão que o **comando anterior precisa ser atualizado** como está.
1.  Adicione uma nova condição como **tipo > parâmetro obrigatório > DateTime**.
1. Adicione uma nova ação como **tipo > enviar resposta de fala > editor simples `Updating previous alarm time to {DateTime}.` >**
1. Deixe o valor padrão do estado de pós-execução como **comando concluído**.

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde a conclusão do treinamento e selecione **testar**.

- Entrada: definir um alarme
- Saída: pronto para definir o alarme com Tom como Chimes. Que horas?
- Entrada: defina um alarme com Tom como jingle para 9h, amanhã
- Saída: Ok, alarme definido para 2020-05-21 09:00:00. O tom de alarme é jingle.
- Entrada: não, 8:00
- Saída: atualizando a hora do alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Em um aplicativo real, na seção ações dessa regra de correção, você também precisará enviar de volta uma atividade para o cliente ou chamar um ponto de extremidade HTTP para atualizar a hora do alarme em seu sistema. Essa ação deve ser singularmente responsável por apenas atualizar a hora do alarme e não qualquer outro atributo do comando, nesse caso, o tom do alarme.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como adicionar modelos de geração de linguagem para respostas de fala](./how-to-custom-commands-add-language-generation-templates.md)