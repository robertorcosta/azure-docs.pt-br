---
title: 'Instruções: desenvolver aplicativos de comandos personalizados – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste "como", você aprenderá a desenvolver e personalizar os aplicativos de comandos personalizados. Os comandos personalizados facilitam a criação de aplicativos avançados de comando de voz otimizados para experiências de interação de primeira a voz e são mais adequados para os cenários de conclusão de tarefas ou de comando e controle, particularmente com correspondência de dispositivos de Internet das Coisas (IoT), ambientes de ambiente e dispositivos sem periféricos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632568"
---
# <a name="develop-custom-commands-applications"></a>Desenvolver aplicativos de comandos personalizados

Neste "como", você aprenderá a desenvolver e configurar aplicativos de comandos personalizados. Os comandos personalizados facilitam a criação de aplicativos avançados de comando de voz otimizados para experiências de interação de primeira a voz e são mais adequados para os cenários de conclusão de tarefas ou de comando e controle, particularmente com correspondência de dispositivos de Internet das Coisas (IoT), ambientes de ambiente e dispositivos sem periféricos.

Neste artigo, você cria um aplicativo que pode ativar e desativar uma TV, definir a temperatura e definir um alarme. Depois de criar esses comandos básicos, as seguintes opções para personalizar comandos são cobertas:

* Adicionando parâmetros a comandos
* Adicionando configurações a parâmetros de comando
* Criando regras de interação
* Criando modelos de geração de linguagem para respostas de fala
* Usando voz personalizada 

## <a name="create-application-with-simple-commands"></a>Criar aplicativo com comandos simples

Primeiro, comece criando um aplicativo de comandos personalizados vazio. Para obter detalhes, consulte o guia de [início rápido](quickstart-custom-commands-application.md). Desta vez, em vez de importar um projeto, você cria um projeto em branco.

1. Na caixa **nome** , insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa de sua escolha).
1. Na lista **idioma** , selecione **Inglês (Estados Unidos)**.
1. Selecione ou crie um recurso LUIS de sua escolha.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o recurso de criação que você selecionou na janela **novo projeto** e definir um recurso de previsão. O recurso de previsão é usado para reconhecimento quando o aplicativo de comandos personalizados é publicado. Você não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

### <a name="add-turnon-command"></a>Adicionar comando de ativação

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

#### <a name="add-example-sentences"></a>Adicionar sentenças de exemplo

Vamos começar com a seção de **sentenças de exemplo** e fornecer um exemplo do que o usuário pode dizer.

1. Selecione a seção **sentenças de exemplo** no painel central.
1. No painel mais à direita, Adicione exemplos:

    ```
    turn on the tv
    ```

1.  Selecione **salvar** na parte superior do painel.

Por enquanto, não temos parâmetros, portanto, podemos mudar para a seção de **regras de conclusão** .

#### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Em seguida, o comando precisa ter uma regra de conclusão. Essa regra informa ao usuário que uma ação de preenchimento está sendo executada. Para ler mais sobre regras e regras de conclusão, acesse [referências](./custom-commands-references.md).

1. Selecione regra de conclusão padrão **concluída** e edite-a da seguinte maneira:

    
    | Configuração    | Valor sugerido                          | Descrição                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | ConfirmationResponse                  | Um nome que descreve a finalidade da regra          |
    | **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
    | **Ações**    | Enviar resposta de fala > editor simples > primeira variação > `Ok, turning the tv on` | A ação a ser tomada quando a condição da regra for verdadeira |

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta de fala](media/custom-commands/create-speech-response-action.png)

1. Selecione **salvar** para salvar a ação.
1. De volta à seção **regras de conclusão** , selecione **salvar** para salvar todas as alterações. 

    > [!NOTE]
    > Não é necessário usar a regra de conclusão padrão que vem com o comando. Se necessário, você pode excluir a regra de conclusão padrão existente e adicionar sua própria regra.

### <a name="add-settemperature-command"></a>Adicionar comando settemperatura

Agora, adicione mais um comando **settemperaturas** que usará um único expressão, `set the temperature to 40 degrees` e responda com a mensagem `Ok, setting temperature to 40 degrees` .

Siga as etapas conforme ilustrado para o comando de **ativação** para criar um novo comando usando a sentença de exemplo "**definir a temperatura como 40 graus**".

Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome  | ConfirmationResponse                  |
| Condições | Nenhum                                     |
| Actions    | Enviar resposta de fala > editor simples > primeira variação > `Ok, setting temperature to 40 degrees` |

Selecione **salvar** para salvar todas as alterações no comando.

### <a name="add-setalarm-command"></a>Adicionar comando setAlarm

Crie um novo comando **setAlarm** usando a sentença de exemplo "**set a Alarm for 9 AM amanhã**". Em seguida, edite as regras de conclusão **realizadas** existentes da seguinte maneira:

| Configuração    | Valor sugerido                          |
| ---------- | ---------------------------------------- |
| Nome da Regra  | ConfirmationResponse                  |
| Condições | Nenhum                                     |
| Actions    | Enviar resposta de fala > editor simples > primeira variação >`Ok, setting an alarm for 9 am tomorrow` |

Selecione **salvar** para salvar todas as alterações no comando.

### <a name="try-it-out"></a>Experimente

Teste o comportamento usando o painel testar chat. Selecione o ícone de **treinamento** presente na parte superior do painel direito. Quando o treinamento for concluído, selecione **testar**. Experimente os exemplos de expressão a seguir por meio de voz ou texto:

- Digite: defina a temperatura como 40 graus
- Resposta esperada: Ok, definindo a temperatura como 40 graus
- Você digita: ativar a TV
- Resposta esperada: Ok, ativando a TV
- Você digita: definir um alarme para 9h, amanhã
- Resposta esperada: Ok, configurando um alarme para 9h, amanhã

> [!div class="mx-imgBorder"]
> ![Teste com o Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> No painel de teste, você pode selecionar **Ativar detalhes** para obter informações sobre como essa entrada de voz/texto foi processada.

## <a name="add-parameters-to-commands"></a>Adicionar parâmetros a comandos

Nesta seção, você aprenderá a adicionar parâmetros aos seus comandos. Os parâmetros são informações requeridas pelos comandos para concluir uma tarefa. Em cenários complexos, os parâmetros também podem ser usados para definir condições que disparam ações personalizadas.

### <a name="configure-parameters-for-turnon-command"></a>Configurar parâmetros para o comando de ativação

Comece editando o comando de **ativação** existente para ativar e desativar vários dispositivos.

1. Agora que o comando agora tratará os cenários on e off, renomeie o comando para **TurnOnOff**.
   1. No painel esquerdo, selecione o comando **Ativar** e, em seguida, selecione o botão de reticências (...) ao lado do **comando novo** na parte superior do painel.
   
   1. Selecione **renomear**. Nas janelas de **comando renomear** , altere o **nome** para **TurnOnOff**.

1. Em seguida, você adiciona um novo parâmetro a esse comando, que representa se o usuário deseja ativar ou desativar o dispositivo.
   1. Selecione  **Adicionar** presente na parte superior do painel central. Na lista suspensa, selecione **parâmetro**.
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
       | Resposta para o parâmetro obrigatório      |Editor simples > `On or Off?`      | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido |
       | Type               | String          | O tipo de parâmetro, como número, Cadeia de caracteres, data/hora ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cadeias de caracteres, isso limita as entradas a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | `on`, `off`           | Conjunto de valores possíveis e seus aliases         |
       
        
   1. Para adicionar valores de entrada predefinidos, selecione **Adicionar uma entrada predefinida** e na janela **novo item**  , digite o **nome** conforme fornecido na tabela acima. Nesse caso, não estamos usando aliases, portanto, você pode deixá-lo em branco.
   
      > [!div class="mx-imgBorder"]
      > ![Criar parâmetro](media/custom-commands/create-on-off-parameter.png)

   1. Selecione **salvar** para salvar todas as configurações do parâmetro.
 
#### <a name="add-subjectdevice-parameter"></a>Adicionar parâmetro SubjectDevice 

1. Em seguida, selecione **Adicionar** novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos que podem ser controlados usando esse comando. Use a configuração a seguir.


    | Configuração            | Valor sugerido       |
    | ------------------ | --------------------- |
    | Nome               | `SubjectDevice`         |
    | É global          | unchecked             |
    | Obrigatório           | checked               |
    | Resposta para o parâmetro obrigatório     | Editor simples > `Which device do you want to control?`    | 
    | Type               | String                |          |
    | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
    | Valores de entrada predefinidos | `tv`, `fan`               |
    | Aliases ( `tv` )      | `television`, `telly`     |

1. Selecione **Salvar**

#### <a name="modify-example-sentences"></a>Modificar sentenças de exemplo

Para comandos com parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

* Informações completas do parâmetro- `turn {OnOff} the {SubjectDevice}`
* Informações de parâmetro parcial- `turn it {OnOff}`
* Nenhuma informação de parâmetro- `turn something`

As frases de exemplo com um grau diferente de informações permitem que o aplicativo de comandos personalizados resolva as resoluções de uma imagem e as resoluções de vários desligamentos com informações parciais.

Com isso em mente, edite as sentenças de exemplo para usar os parâmetros conforme sugerido abaixo:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecione **Salvar**.

> [!TIP]
> No editor de sentenças de exemplo, use chaves para fazer referência aos parâmetros. - `turn {OnOff} the {SubjectDevice}` Use a guia para preenchimento automático apoiado por parâmetros criados anteriormente.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificar regras de conclusão para incluir parâmetros

Modifique a regra de conclusão existente **ConfirmationResponse**.

1. Na seção **condições** , selecione **Adicionar uma condição**.
1. Na janela **nova condição** , na lista **tipo** , selecione **parâmetros necessários**. Na lista de verificação abaixo, verifique **Onoff** e **SubjectDevice**.
1. Deixe **IsGlobal** como desmarcado.
1. Selecione **Criar**.
1. Na seção **ações** , edite a ação **Enviar resposta de fala** existente passando o mouse sobre a ação e selecionando o botão Editar. Desta vez, fazer uso dos parâmetros **Onoff** e **SubjectDevice** recém-criados

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecione **Salvar**.

Experimente as alterações selecionando o ícone de **treinamento** na parte superior do painel direito. Quando o treinamento for concluído, selecione **testar**. Uma janela **testar seu aplicativo** será exibida. Tente as seguintes interações.

- Entrada: desligar a TV
- Saída: Ok, desligando a TV
- Entrada: desligar a televisão
- Saída: Ok, desligando a TV
- Entrada: Desative-a
- Saída: qual dispositivo você deseja controlar?
- Entrada: a TV
- Saída: Ok, desligando a TV

### <a name="configure-parameters-for-settemperature-command"></a>Configurar parâmetros para comando settemperatura

Modifique o comando **settemperaturas** para habilitá-lo para definir a temperatura conforme direcionado pelo usuário.

Adicionar nova **temperatura** de parâmetro com a seguinte configuração

| Configuração      | Valor sugerido     |
| ------------------ | ----------------|
| Nome               | `Temperature`           |
| Obrigatório           | checked         |
| Resposta para o parâmetro obrigatório      | Editor simples > `What temperature would you like?`
| Tipo               | Número          |


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
| Actions           | Enviar > de resposta de fala `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Configurar parâmetros para o comando setAlarm

Adicione um parâmetro chamado **DateTime** com a configuração a seguir.

   | Configuração                           | Valor sugerido                     | 
   | --------------------------------- | ----------------------------------------|
   | Nome                              | `DateTime`                               |
   | Obrigatório                          | checked                                 |
   | Resposta para o parâmetro obrigatório   | Editor simples > `For what time?`            | 
   | Tipo                              | DateTime                                |
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
   | Actions    | Enviar resposta de fala- `Ok, alarm set for {DateTime}`  |

Teste todos os três comandos juntos usando declarações relacionados a comandos diferentes. Observe que você pode alternar entre os diferentes comandos.

- Entrada: definir um alarme
- Saída: por que tempo?
- Entrada: ativar a TV
- Saída: Ok, ligando a TV
- Entrada: definir um alarme
- Saída: por que tempo?
- Entrada: 17:00
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Adicionar configurações aos parâmetros de comandos

Nesta seção, você aprenderá mais sobre a configuração avançada de parâmetros, incluindo:

 - Como os valores de parâmetro podem pertencer a um conjunto definido externamente para o aplicativo de comandos personalizados
 - Como adicionar cláusulas de validação no valor dos parâmetros

### <a name="configure-parameter-as-external-catalog-entity"></a>Configurar o parâmetro como uma entidade de catálogo externo

Os comandos personalizados permitem que você configure parâmetros de tipo de cadeia de caracteres para se referir a catálogos externos hospedados em um ponto de extremidade da Web. Isso permite que você atualize o catálogo externo independentemente sem fazer edições no aplicativo de comandos personalizados. Essa abordagem é útil em casos em que as entradas do catálogo podem ser grandes no número.

Reutilize o parâmetro **SubjectDevice** do comando **TurnOnOff** . A configuração atual para esse parâmetro é **aceitar entradas predefinidas do catálogo interno**. Isso se refere à lista estática de dispositivos, conforme definido na configuração do parâmetro. Queremos mover esse conteúdo para uma fonte de dados externa que pode ser atualizada de forma independente.

Para fazer isso, comece adicionando um novo ponto de extremidade da Web. Vá para a seção pontos de extremidade **da Web** no painel à esquerda e adicione um novo Web EndPoint com a configuração a seguir.

| Configuração | Valor sugerido |
|----|----|
| Nome | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Método | GET |


Se o valor sugerido para a URL não funcionar para você, você precisará configurar e hospedar um ponto de extremidade simples da Web que retorna um JSON que consiste em uma lista dos dispositivos que podem ser controlados. O ponto de extremidade da Web deve retornar um JSON formatado da seguinte maneira:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Em seguida, vá para a página Configurações de parâmetro **SubjectDevice** e altere as propriedades para o seguinte.

| Configuração | Valor sugerido |
| ----| ---- |
| Configuração | Aceitar entradas predefinidas do catálogo externo |                               
| Ponto de extremidade do catálogo | dispositivos |
| Método | GET |

Em seguida, selecione **Salvar**.

> [!IMPORTANT]
> Você não verá uma opção para configurar um parâmetro para aceitar entradas de um catálogo externo, a menos que tenha o ponto de extremidade da Web definido na seção **ponto de extremidade da Web** no painel esquerdo.

Experimente selecionando **treinar** e aguarde a conclusão do treinamento. Quando o treinamento for concluído, selecione **testar** e tente algumas interações.

* Entrada: ativar
* Saída: qual dispositivo você deseja controlar?
* Entrada: luzes
* Saída: Ok, acendendo as luzes

> [!NOTE]
> Observe como você pode controlar todos os dispositivos hospedados no ponto de extremidade da Web agora. Você ainda precisa treinar o aplicativo para testar as novas alterações e publicar o aplicativo novamente.

### <a name="add-validation-to-parameters"></a>Adicionar validação aos parâmetros

**Validações** são construções aplicáveis a determinados tipos de parâmetro que permitem que você configure restrições no valor do parâmetro e solicite a correção se os valores para não ficarem dentro das restrições. Para obter uma lista completa de tipos de parâmetro que estendem a construção de validação, acesse [referências](./custom-commands-references.md)

Teste validações usando o comando **settemperaturas** . Use as etapas a seguir para adicionar uma validação para o parâmetro de **temperatura** .

1. Selecione o comando **settemperaturas** no painel esquerdo.
1. Selecione  **temperatura** no painel central.
1. Selecione **Adicionar uma validação** presente no painel direito.
1. Na janela **nova validação** , configure a validação da seguinte maneira e selecione **criar**.


    | Configuração do parâmetro | Valor sugerido | Descrição |
    | ---- | ---- | ---- |
    | Valor mínimo | `60` | Para parâmetros numéricos, o valor mínimo que esse parâmetro pode assumir |
    | Valor máximo | `80` | Para parâmetros numéricos, o valor máximo que esse parâmetro pode assumir |
    | Resposta de falha |  Editor simples > primeira > de variação `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Solicitar um novo valor se a validação falhar |

    > [!div class="mx-imgBorder"]
    > ![Adicionar uma validação de intervalo](media/custom-commands/add-validations-temperature.png)

Experimente selecionando o ícone de **treinamento** presente na parte superior do painel direito. Quando o treinamento for concluído, selecione **testar** e tente algumas interações:

- Entrada: defina a temperatura como 72 graus
- Saída: Ok, definindo a temperatura como 72 graus
- Entrada: defina a temperatura como 45 graus
- Saída: Desculpe, só posso definir a temperatura entre 60 e 80 graus
- Entrada: torne-o 72 graus
- Saída: Ok, definindo a temperatura como 72 graus

## <a name="add-interaction-rules"></a>Adicionar regras de interação

Regras de interação são *regras adicionais* para lidar com situações específicas ou complexas. Embora você esteja livre para criar suas próprias regras de interação personalizadas, neste exemplo, você faz uso de regras de interação para os seguintes cenários de destino:

* Confirmando comandos
* Adicionando uma correção de uma etapa aos comandos

Para saber mais sobre regras de interação, vá para a seção [referências](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Adicionar confirmações a um comando

Para adicionar uma confirmação, use o comando **settemperaturas** . Para obter a confirmação, você cria regras de interação usando as etapas a seguir.

1. Selecione o comando **settemperaturas** no painel esquerdo.
1. Adicione regras de interação selecionando **Adicionar** no painel central. Em seguida, selecione o comando **regras de interação**  >  **confirmar**.

    Essa ação adiciona três regras de interação que solicitarão que o usuário confirme a data e a hora do alarme e espera uma confirmação (Sim/não) para a próxima rodada.

    1. Modifique a regra de interação de **comando Confirm** de acordo com a configuração a seguir:
        1. Renomeie o **nome** para **confirmar a temperatura**.
        1. Adicione uma nova condição conforme a temperatura dos **parâmetros necessários**  >  .
        1. Adicionar uma nova ação como **tipo**  >  **Enviar resposta**  >  **de fala tem certeza de que deseja definir a temperatura como {temperatura} graus?**
        1. Deixe o valor padrão de **confirmação esperada do usuário** na seção **expectativas** .
      
         > [!div class="mx-imgBorder"]
         > ![Criar resposta de parâmetro necessária](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifique a regra de interação de **confirmação com êxito** para tratar de uma confirmação bem-sucedida (o usuário disse sim).
      
          1. Modificação de **nome** para **temperatura de confirmação bem-sucedida**.
          1. Deixe que a confirmação já existente **tenha sido bem-sucedida** .
          1. Adicione uma nova condição como **tipo** de temperatura de  >  **parâmetros necessários**  >  .
          1. Deixe o valor padrão do **estado de pós-execução** como **regras de conclusão de execução**.

    1. Modifique a regra de interação de **confirmação negada** para lidar com cenários quando a confirmação for negada (o usuário não disse).

          1. Modificar **nome** para **temperatura de confirmação negada**.
          1. Deixe a condição já existente com a **confirmação negada** .
          1. Adicione uma nova condição como **tipo** de temperatura de  >  **parâmetros necessários**  >  .
          1. Adicione uma nova ação como **tipo**  >  **Enviar resposta de fala**  >  **sem problema. Qual temperatura?**
          1. Deixe o valor padrão do **estado de pós-execução** como **aguardar a entrada do usuário**.

> [!IMPORTANT]
> Neste artigo, você usou a funcionalidade de confirmação interna. Você também pode adicionar manualmente as regras de interação uma a uma.
   
Experimente as alterações selecionando **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.

- **Entrada**: defina a temperatura como 80 graus
- **Saída**: tem certeza de que deseja definir a temperatura como 80 graus?
- **Entrada**: não
- **Saída**: sem problema. Qual temperatura?
- **Entrada**: 72 graus
- **Saída**: tem certeza de que deseja definir a temperatura como 72 graus?
- **Entrada**: Sim
- **Saída**: Ok, definindo a temperatura como 83 graus

### <a name="implement-corrections-in-a-command"></a>Implementar correções em um comando

Nesta seção, você configura uma correção de uma etapa, que é usada após a ação de preenchimento já ter sido executada. Você também verá um exemplo de como uma correção é habilitada por padrão, caso o comando ainda não seja atendido. Para adicionar uma correção quando o comando não for concluído, adicione o novo parâmetro **AlarmTone**.

Selecione o comando **setAlarm** no painel esquerdo e adicione o novo parâmetro **AlarmTone**.
        
- **Nome**  >  do **AlarmTone**
- **Tipo**  >  de **Cadeia de caracteres**
- **Valor padrão**  >  **Chimes**
- **Configuração**  >  do **Aceitar valores de entrada predefinidos do catálogo interno**
- Valores de entrada **predefinidos**  >  **Chimes**, **jingle** e **Echo** como entradas predefinidas individuais


Em seguida, atualize a resposta para o parâmetro **DateTime** como **pronto para definir o alarme com Tom como {AlarmTone}. Por que tempo?**. Em seguida, modifique a regra de conclusão da seguinte maneira:

1. Selecione a regra de conclusão **ConfirmationResponse** existente.
1. No painel direito, passe o mouse sobre a ação existente e selecione **Editar**.
1. Atualize a resposta de fala para **OK, o alarme definido para {DateTime}. O tom de alarme é {AlarmTone}.**

> [!IMPORTANT]
> O tom de alarme pode ser alterado sem nenhuma configuração explícita em um comando contínuo, por exemplo, quando o comando ainda não foi concluído. *Uma correção é habilitada por padrão para todos os parâmetros de comando, independentemente do número de desativação, caso o comando ainda seja atendido.*

#### <a name="correction-when-command-is-completed"></a>Correção quando o comando é concluído

A plataforma de comandos personalizados também fornece a capacidade de uma correção de uma etapa, mesmo quando o comando tiver sido concluído. Esse recurso não está habilitado por padrão. Ele deve ser configurado explicitamente. Use as etapas a seguir para configurar uma correção de uma etapa.

1. No comando **setAlarm** , adicione uma regra de interação do tipo **Atualizar comando anterior** para atualizar o alarme definido anteriormente. Renomeie o **nome** padrão da regra de interação para **atualizar o alarme anterior**.
1. Deixe a condição padrão que o **comando anterior precisa ser atualizado** como está.
1. Adicione uma nova condição como **tipo**  >  DateTime de **parâmetro necessário**  >  .
1. Adicione uma nova ação como **tipo**  >  **Enviar resposta de fala**  >  **editor simples**  >  **atualizando a hora do alarme anterior para {DateTime}.**
1. Deixe o valor padrão do **estado de pós-execução** como **comando concluído**.

Experimente as alterações selecionando **treinar**, aguarde até que o treinamento seja concluído e selecione **testar**.

- **Entrada**: defina um alarme.
- **Saída**: pronto para definir o alarme com Tom como Chimes. Por que tempo?
- **Entrada**: defina um alarme com o Tom como jingle para 9h, amanhã.
- **Saída**: Ok, alarme definido para 2020-05-21 09:00:00. O tom de alarme é jingle.
- **Entrada**: não, 8:00.
- **Saída**: atualizando a hora do alarme anterior para 2020-05-29 08:00.

> [!NOTE]
> Em um aplicativo real, na seção **ações** dessa regra de correção, você também precisará enviar de volta uma atividade para o cliente ou chamar um ponto de extremidade http para atualizar a hora do alarme em seu sistema. Essa ação deve ser exclusivamente responsável por atualizar a hora do alarme e não qualquer outro atributo do comando. Nesse caso, esse seria o sinal de alarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de linguagem para respostas de fala

Os modelos de geração de linguagem permitem que você personalize as respostas enviadas ao cliente e introduza a variação nas respostas. A personalização da geração de idioma pode ser obtida por:

* Uso de modelos de geração de linguagem
* Uso de expressões adaptáveis

Os modelos de comandos personalizados são baseados nos [modelos de LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)do BotFramework. Como os comandos personalizados criam um novo modelo LG quando necessário (ou seja, para respostas de fala em parâmetros ou ações), não é necessário especificar o nome do modelo LG. Então, em vez de definir seu modelo como:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Você só precisa definir o corpo do modelo sem o nome, da seguinte maneira.

> [!div class="mx-imgBorder"]
> ![exemplo de editor de modelo](./media/custom-commands/template-editor-example.png)


Essa alteração introduz a variação para as respostas de fala que estão sendo enviadas ao cliente. Portanto, para o mesmo expressão, a resposta de fala correspondente seria separada aleatoriamente das opções fornecidas.

Aproveitar os modelos LG também permite que você defina respostas complexas de fala para comandos usando expressões adaptáveis. Você pode consultar o [formato de modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) para obter mais detalhes. Por padrão, os comandos personalizados dão suporte a todos os recursos com as seguintes diferenças secundárias:

* Nas entidades de modelos LG são representadas como $ {EntityName}. Em comandos personalizados, não usamos entidades, mas parâmetros podem ser usados como variáveis com qualquer uma dessas representações $ {parameterName} ou {parameterName}
* Não há suporte para composição e expansão de modelo em comandos personalizados. Isso ocorre porque você nunca edita o `.lg` arquivo diretamente, mas apenas as respostas de modelos criados automaticamente.
* Não há suporte para funções personalizadas injetadas por LG em comandos personalizados. Funções predefinidas ainda têm suporte.
* Não há suporte para as opções (estrito, replaceNull & lineBreakStyle) em comandos personalizados.

### <a name="add-template-responses-to-turnonoff-command"></a>Adicionar respostas de modelo ao comando TurnOnOff

Modifique o comando **TurnOnOff** para adicionar um novo parâmetro com a seguinte configuração:

| Configuração            | Valor sugerido       | 
| ------------------ | --------------------- | 
| Nome               | `SubjectContext`         | 
| É global          | unchecked             | 
| Obrigatório           | unchecked               | 
| Type               | String                |
| Valor padrão      | `all` |
| Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
| Valores de entrada predefinidos | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Modificar regra de conclusão

Edite a seção **ações** da regra de conclusão existente **ConfirmationResponse**. No pop-up **Editar ação** , alterne para o **Editor de modelos** e substitua o texto pelo exemplo a seguir.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Treine** e **teste** seu aplicativo da seguinte maneira. Observe a variação de respostas devido ao uso de várias alternativas do valor do modelo e também ao uso de expressões adaptáveis.

* Entrada: ativar a TV
* Saída: Ok, ligando a TV
* Entrada: ativar a TV
* Saída: concluído, ativado na TV
* Entrada: desative as luzes
* Saída: Ok, desligando todas as luzes
* Entrada: desligar as luzes de sala
* Saída: Ok, desligando as luzes de sala

## <a name="use-custom-voice"></a>Usar a Voz Personalizada

Outra maneira de personalizar respostas de comandos personalizados é selecionar uma voz de saída personalizada. Use as etapas a seguir para alternar a voz padrão para uma voz personalizada.

1. Em seu aplicativo de comandos personalizados, selecione **configurações** no painel esquerdo.
1. Selecione **voz personalizada** no painel central.
1. Selecione a voz pública ou personalizada desejada da tabela.
1. Selecione **Salvar**.

> [!div class="mx-imgBorder"]
> ![Sentenças de exemplo com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas**, os **tipos neurais** estão disponíveis somente para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neural por região/ponto de extremidade](./regions.md#standard-and-neural-voices).
> - Para **vozes personalizadas**, elas podem ser criadas na página projeto de voz personalizado. Consulte [introdução à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [integrar seu aplicativo de comandos personalizados](how-to-custom-commands-setup-speech-sdk.md) a um aplicativo cliente usando o SDK de fala.
* [Configure a implantação contínua](how-to-custom-commands-deploy-cicd.md) para seu aplicativo de comandos personalizados com o Azure DevOps. 
                      