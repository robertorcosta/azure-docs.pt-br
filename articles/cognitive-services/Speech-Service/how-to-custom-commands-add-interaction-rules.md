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
ms.openlocfilehash: f37109cc2677ad5ef18c5677bda9308a78cebccf
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851316"
---
# <a name="add-interaction-rules"></a>Adicionar regras de interação

Neste artigo, você aprenderá sobre *as regras de interação*. Essas regras adicionais tratam de situações mais específicas ou complexas. Você pode criar suas próprias regras de interação personalizadas, mas neste artigo você faz uso de regras de interação para os seguintes cenários de destino:

* Confirmando comandos
* Adicionando uma correção de uma etapa aos comandos

Para saber mais sobre regras de interação, vá para a seção [referências](./custom-commands-references.md) .

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:
> [!div class="checklist"]
> * [Criar aplicativo com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Adicionar parâmetros a comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o comando **settemperaturas** . Para obter a confirmação, você cria regras de interação usando as etapas a seguir.

1. Selecione o comando **settemperaturas** no painel esquerdo.
1. Adicione regras de interação selecionando **Adicionar** no painel central. Em seguida, selecione o comando **regras de interação**  >  **confirmar**.

    Essa ação adiciona três regras de interação. Essa regra solicita que o usuário confirme a data e a hora do alarme e espera uma confirmação (Sim/não) para a próxima vez.

    1. Modifique a regra de interação de **comando Confirm** de acordo com a configuração a seguir:
        1. Renomeie o **nome** para **confirmar a temperatura**.
        1. Adicione uma nova condição conforme a temperatura dos **parâmetros necessários**  >  **Temperature**.
        1. Adicionar uma nova ação como **tipo**  >  **Enviar resposta**  >  **de fala tem certeza de que deseja definir a temperatura como {temperatura} graus?**
        1. Deixe o valor padrão de **confirmação esperada do usuário** na seção **expectativas** .
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifique a regra de interação de **confirmação com êxito** para tratar de uma confirmação bem-sucedida (o usuário disse sim).
      
          1. Modificação de **nome** para **temperatura de confirmação bem-sucedida**.
          1. Deixe que a confirmação já existente **tenha sido bem-sucedida** .
          1. Adicione uma nova condição como **tipo**de temperatura de  >  **parâmetros necessários**  >  **Temperature**.
          1. Deixe o valor padrão do **estado de pós-execução** como **regras de conclusão de execução**.

    1. Modifique a regra de interação de **confirmação negada** para lidar com cenários quando a confirmação for negada (o usuário não disse).

          1. Modificar **nome** para **temperatura de confirmação negada**.
          1. Deixe a condição já existente com a **confirmação negada** .
          1. Adicione uma nova condição como **tipo**de temperatura de  >  **parâmetros necessários**  >  **Temperature**.
          1. Adicione uma nova ação como **tipo**  >  **Enviar resposta de fala**  >  **sem problema. Qual temperatura?**
          1. Deixe o valor padrão do **estado de pós-execução** como **aguardar a entrada do usuário**.

> [!IMPORTANT]
> Neste artigo, você usou a funcionalidade de confirmação interna. Você também pode adicionar manualmente as regras de interação uma a uma.
   

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.

- **Entrada**: defina a temperatura como 80 graus.
- **Saída**: Ok 80?
- **Entrada**: não.
- **Saída**: sem problema. Qual temperatura?
- **Entrada**: 83 graus.
- **Saída**: Ok 83?
- **Entrada**: Sim.
- **Saída**: Ok, definindo a temperatura como 83 graus.


## <a name="implement-corrections-in-a-command"></a>Implementar correções em um comando

Nesta seção, você configura uma correção de uma etapa, que é usada após a ação de preenchimento já ter sido executada. Você também verá um exemplo de como uma correção é habilitada por padrão, caso o comando ainda não seja atendido. Para adicionar uma correção quando o comando não for concluído, adicione o novo parâmetro **AlarmTone**.

Selecione o comando **setAlarm** no painel esquerdo e adicione o novo parâmetro **AlarmTone**.
        
- **Nome**  >  do **AlarmTone**
- **Tipo**  >  de **Cadeia de caracteres**
- **Valor padrão**  >  **Chimes**
- **Configuração**  >  do **Aceitar valores de entrada predefinidos do catálogo interno**
- Valores de entrada **predefinidos**  >  **Chimes**, **jingle**e **Echo** como entradas predefinidas individuais


Em seguida, atualize a resposta para o parâmetro **DateTime** como **pronto para definir o alarme com Tom como {AlarmTone}. Por que tempo?**. Em seguida, modifique a regra de conclusão da seguinte maneira:

1. Selecione a regra de conclusão **ConfirmationResponse**existente.
1. No painel direito, passe o mouse sobre a ação existente e selecione **Editar**.
1. Atualize a resposta de fala para **OK, o alarme definido para {DateTime}. O tom de alarme é {AlarmTone}.**

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.
Experimente o seguinte declarações:

- **Entrada**: defina um alarme.
- **Saída**: pronto para definir o alarme com Tom como Chimes. Por que tempo?
- **Entrada**: defina um alarme com o Tom como jingle para 9h, amanhã.
- **Saída**: Ok, alarme definido para 2020-05-30 09:00:00. O tom de alarme é jingle.

> [!IMPORTANT]
> O tom de alarme pode ser alterado sem nenhuma configuração explícita em um comando contínuo, por exemplo, quando o comando ainda não foi concluído. *Uma correção é habilitada por padrão para todos os parâmetros de comando, independentemente do número de desativação, caso o comando ainda seja atendido.*

### <a name="correction-when-command-is-completed"></a>Correção quando o comando é concluído

A plataforma de comandos personalizados também fornece a capacidade de uma correção de uma etapa, mesmo quando o comando tiver sido concluído. Esse recurso não está habilitado por padrão. Ele deve ser configurado explicitamente. Use as etapas a seguir para configurar uma correção de uma etapa.

1. No comando **setAlarm** , adicione uma regra de interação do tipo **Atualizar comando anterior** para atualizar o alarme definido anteriormente. Renomeie o **nome** padrão da regra de interação para **atualizar o alarme anterior**.
1. Deixe a condição padrão que o **comando anterior precisa ser atualizado** como está.
1. Adicione uma nova condição como **tipo**  >  DateTime de**parâmetro necessário**  >  **DateTime**.
1. Adicione uma nova ação como **tipo**  >  **Enviar resposta de fala**  >  **editor simples**  >  **atualizando a hora do alarme anterior para {DateTime}.**
1. Deixe o valor padrão do **estado de pós-execução** como **comando concluído**.

### <a name="try-out-the-changes"></a>Experimente as alterações

Selecione **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.

- **Entrada**: defina um alarme.
- **Saída**: pronto para definir o alarme com Tom como Chimes. Por que tempo?
- **Entrada**: defina um alarme com o Tom como jingle para 9h, amanhã.
- **Saída**: Ok, alarme definido para 2020-05-21 09:00:00. O tom de alarme é jingle.
- **Entrada**: não, 8:00.
- **Saída**: atualizando a hora do alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Em um aplicativo real, na seção **ações** dessa regra de correção, você também precisará enviar de volta uma atividade para o cliente ou chamar um ponto de extremidade http para atualizar a hora do alarme em seu sistema. Essa ação deve ser exclusivamente responsável por atualizar a hora do alarme e não qualquer outro atributo do comando. Nesse caso, esse seria o sinal de alarme.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como adicionar modelos de geração de linguagem para respostas de fala](./how-to-custom-commands-add-language-generation-templates.md)
