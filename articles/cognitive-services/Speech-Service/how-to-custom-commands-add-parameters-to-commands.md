---
title: 'Como: adicionar comandos simples a comandos personalizados serviço de fala de aplicativo'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a adicionar parâmetros a comandos personalizados
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284142"
---
# <a name="add-parameters-to-commands"></a>Adicionar parâmetros a comandos

Neste artigo, você aprenderá a adicionar parâmetros a comandos personalizados. Os parâmetros são informações requeridas pelos comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Como: criar aplicativo com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Configurar parâmetros para o comando de ativação

Edite o comando de **ativação** existente para ativar e desativar vários dispositivos.

1. Como o comando agora tratará do cenário on e off, renomeie o comando para **TurnOnOff**.
   1. No painel esquerdo, selecione o comando **Ativar** e, em seguida, selecione o botão de reticências (...) ao lado do **comando novo** na parte superior do painel.
   
   1. Selecione **renomear**. Nas janelas de **comando renomear** , altere o **nome** para **TurnOnOff**.

1. Em seguida, você adiciona um novo parâmetro a esse comando, que representa se o usuário deseja ativar ou desativar o dispositivo.
   1. Selecione **Adicionar** presente na parte superior do painel central. Na lista suspensa, selecione **parâmetro**.
   1. No painel direito, na seção **parâmetros** , adicione o valor na caixa **nome** como **Onoff**.
   1. Selecione **obrigatório**. Na janela **Adicionar resposta para um parâmetro necessário** , selecione **editor simples**. Na **primeira variação**, adicione
        ```
        On or Off?
        ```
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetro necessária](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Agora, configuramos as propriedades de parâmetros. Para obter uma explicação de todas as propriedades de configuração de um comando, acesse [referências](./custom-commands-references.md). Configure as propriedades do parâmetro da seguinte maneira:
      

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | `OnOff`           | Um nome descritivo para o parâmetro                                                                           |
       | É global          | unchecked       | Caixa de seleção que indica se um valor para esse parâmetro é globalmente aplicado a todos os comandos no aplicativo|
       | Obrigatório           | checked         | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
       | Resposta para o parâmetro obrigatório      |Editor simples >`On or Off?`      | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
       | Type               | String          | O tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cadeias de caracteres, isso limita as entradas a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | `on`, `off`           | Conjunto de valores possíveis e seus aliases         |
       
        
   1. Para adicionar valores de entrada predefinidos, selecione **Adicionar uma entrada predefinida** e na janela **novo item** , digite o **nome** conforme fornecido na tabela acima. Nesse caso, não estamos usando aliases, portanto, você pode deixá-lo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Criar parâmetro](media/custom-commands/create-on-off-parameter.png)

   1. Selecione **salvar** para salvar todas as configurações do parâmetro.
 
 ### <a name="add-subjectdevice-parameter"></a>Adicionar parâmetro SubjectDevice 

   1. Em seguida, selecione **Adicionar** novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados usando esse comando. Use a configuração a seguir.
   

       | Configuração            | Valor sugerido       |
       | ------------------ | --------------------- |
       | Nome               | `SubjectDevice`         |
       | É global          | unchecked             |
       | Obrigatório           | checked               |
       | Resposta para o parâmetro obrigatório     | Editor simples >`Which device do you want to control?`    | 
       | Type               | String                |          |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
       | Valores de entrada predefinidos | `tv`, `fan`               |
       | Aliases ( `tv` )      | `television`, `telly`     |

   1. Selecione **Salvar**

### <a name="modify-example-sentences"></a>Modificar sentenças de exemplo

Para comandos com parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

* Informações completas do parâmetro-`turn {OnOff} the {SubjectDevice}`
* Informações de parâmetro parcial-`turn it {OnOff}`
* Nenhuma informação de parâmetro-`turn something`

As frases de exemplo com um grau diferente de informações permitem que o aplicativo de comandos personalizados resolva as resoluções de uma imagem e as resoluções de vários desligamentos com informações parciais.

Com isso em mente, edite as sentenças de exemplo para usar os parâmetros conforme sugerido abaixo:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Clique em **Salvar**.

> [!TIP]
> No editor de sentenças de exemplo, use chaves para fazer referência aos parâmetros. - `turn {OnOff} the {SubjectDevice}`Use a guia para preenchimento automático apoiado por parâmetros criados anteriormente.

### <a name="modify-completion-rules-to-include-parameters"></a>Modificar regras de conclusão para incluir parâmetros

Modifique a regra de conclusão existente **ConfirmationResponse**.

1. Na seção **condições** , selecione **Adicionar uma condição**.
1. Na janela **nova condição** , na lista **tipo** , selecione **parâmetros necessários**. Na lista de verificação abaixo, verifique **Onoff** e **SubjectDevice**.
1. Deixe **IsGlobal** como desmarcado.
1. Selecione **Criar**.
1. Na seção **ações** , edite a ação **Enviar resposta de fala** existente passando o mouse sobre a ação e selecionando o botão Editar. Desta vez, fazer uso dos parâmetros **Onoff** e **SubjectDevice** recém-criados

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Clique em **Salvar**.

### <a name="try-it-out"></a>Experimente
1. Selecione o ícone de **treinamento** na parte superior do painel direito.

1. Quando o treinamento for concluído, selecione **testar**. Uma janela **testar seu aplicativo** será exibida.
 Experimente algumas interações.

    - Entrada: desligar a TV
    - Saída: Ok, desligando a TV
    - Entrada: desligar a televisão
    - Saída: Ok, desligando a TV
    - Entrada: Desative-a
    - Saída: qual dispositivo você deseja controlar?
    - Entrada: a TV
    - Saída: Ok, desligando a TV

## <a name="configure-parameters-for-settemperature-command"></a>Configurar parâmetros para comando settemperatura

Modifique o comando **settemperaturas** para habilitá-lo para definir a temperatura conforme direcionado pelo usuário.

Adicionar nova **temperatura** de parâmetro com a seguinte configuração

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Nome               | `Temperature`           |
| Obrigatório           | checked         |
| Resposta para o parâmetro obrigatório      | Editor simples >`What temperature would you like?`
| Type               | Número          |


Edite o exemplo declarações para os valores a seguir.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Edite as regras de conclusão existentes de acordo com a configuração a seguir.

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Condições         | Temperatura > parâmetro obrigatório           |
| Ações           | Enviar > de resposta de fala`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Experimente

**Treine** e **teste** as alterações com algumas interações.

- Entrada: definir temperatura
- Saída: qual temperatura você deseja?
- Entrada: 50 graus
- Saída: Ok, definindo a temperatura como 50 graus

## <a name="configure-parameters-to-the-setalarm-command"></a>Configurar parâmetros para o comando setAlarm

Adicione um parâmetro chamado **DateTime** com a configuração a seguir.

   | Configuração                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | Nome                              | `DateTime`                               |
   | Obrigatório                          | checked                                 |
   | Resposta para o parâmetro obrigatório   | Editor simples >`For what time?`            | 
   | Type                              | Datetime                                |
   | Padrões de data                     | Se a data estiver ausente, use hoje mesmo            |
   | Padrões de tempo                     | Se o tempo estiver ausente, use o início do dia     |


> [!NOTE]
> Neste artigo, predominantemente utilizamos os tipos de parâmetro String, Number e DateTime. Para obter uma lista de todos os tipos de parâmetro com suporte e suas propriedades, acesse [referências](./custom-commands-references.md).


Edite o exemplo declarações para os valores a seguir.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Edite as regras de conclusão existentes de acordo com a configuração a seguir.

   | Configuração    | Valor sugerido                               |
   | ---------- | ------------------------------------------------------- |
   | Ações    | Enviar resposta de fala-`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Experimente

**Treine** e **teste** as alterações.
- Entrada: Definir alarme para amanhã às 12h
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: definir um alarme
- Saída: qual hora?
- Entrada: 17:00
- Saída: Ok, alarme definido para 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Experimente todos os comandos

Teste todos os três comandos juntos usando declarações relacionados a comandos diferentes. Observe que você pode alternar entre os diferentes comandos.

- Entrada: definir um alarme
- Saída: por que tempo?
- Entrada: ativar a TV
- Saída: Ok, ligando a TV
- Entrada: definir um alarme
- Saída: por que tempo?
- Entrada: 17:00
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: adicionar configurações aos parâmetros de comandos](./how-to-custom-commands-add-parameter-configuration.md)
