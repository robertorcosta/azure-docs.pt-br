---
title: Executar operações em dados
description: Converter, gerenciar e manipular os formatos e saídas de dados nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283934"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Executar operações de dados nos Aplicativos Lógicos do Azure

Este artigo mostra como você pode trabalhar com dados nos aplicativos lógicos adicionando ações para esses dados e mais:

* Crie tabelas a partir de matrizes.
* Crie matrizes a partir de outras matrizes com base em uma condição.
* Crie tokens amigáveis a partir de propriedades de objeto JSON (Notação de Objeto JavaScript) para poder usar facilmente essas propriedades no fluxo de trabalho.

Se você não encontrar a ação que deseja aqui, tente navegar nas várias [funções de manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que o Azure Logic Apps fornece.

Essas tabelas resumem as operações de dados que você pode usar e estão organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição é exibida em ordem alfabética.

**Ações de matriz** 

Essas ações ajudam você a trabalhar com dados em matrizes.

| Ação | Descrição |
|--------|-------------|
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela CSV (valores separados por vírgula) a partir de uma matriz. |
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. |
| [**Filtrar matriz**](#filter-array-action) | Crie um subconjunto de matriz a partir de uma matriz com base no filtro especificado ou condição específica. |
| [**Unir**](#join-action) | Crie uma cadeia de caracteres a partir de todos os itens em uma matriz e separe cada item com o caractere especificado. |
| [**Selecione**](#select-action) | Crie uma matriz a partir de propriedades especificadas para todos os itens em uma matriz diferente. |
||| 

**Ações de JSON**

Essas ações ajudam você a trabalhar com dados em formato JSON (JavaScript Object Notation).

| Ação | Descrição |
|--------|-------------|
| [**Redigir**](#compose-action) | Crie uma mensagem ou cadeia de caracteres a partir de várias entradas que podem ter diversos tipos de dados. Em seguida, é possível usar essa cadeia de caracteres como uma única entrada, em vez de inserir repetidamente as mesmas entradas. Por exemplo, você pode criar uma única mensagem JSON a partir de várias entradas. |
| [**Analisar JSON**](#parse-json-action) | Crie tokens de dados amigáveis para propriedades em conteúdo JSON para poder usar as propriedades nos aplicativos lógicos com mais facilidade. |
|||

Para criar transformações JSON mais complexas, consulte [Executar transformações JSON avançadas com modelos Liquid](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico em que você precisa da operação para trabalhar com dados

  Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [quickstart: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Operações de dados estão disponíveis apenas como ações, portanto, para poder usar essas ações, inicie o aplicativo lógico com um gatilho e inclua quaisquer outras ações necessárias para criar as saídas que você quer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Ação para compor

Para construir uma única saída, como um objeto JSON a partir de várias entradas, você pode usar a ação **Compor.** As entradas podem ter vários tipos, como inteiros, boolianos, matrizes, objetos JSON e outro tipo nativo com suporte dos Aplicativos Lógicos do Azure, por exemplo, binário e XML. Em seguida, você pode usar a saída em ações que seguem após a ação **Redigir**. A ação **Redigir** também pode evitar que você insira repetidamente as mesmas entradas ao criar o fluxo de trabalho do aplicativo lógico.

Por exemplo, você pode construir uma mensagem JSON a partir de diversas variáveis, como variáveis de cadeia de caracteres que armazenam os nomes e sobrenomes das pessoas e uma variável de inteiro que armazena a idade das pessoas. Aqui, a ação **Redigir** aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para testar um exemplo, siga estas etapas usando o Designer do Aplicativo Lógico. Ou então, se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Redigir** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Redigir](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e várias ações **Inicializar variável**. Essas ações são configuradas para a criação de duas variáveis de cadeia de caracteres e uma variável de inteiro. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para ação "Compor"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. No aplicativo lógico em que você quer criar a saída, siga uma destas etapas: 

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para ação "Compor"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `compose` como o filtro. Na lista de ações, selecione a ação **Compor.**

   ![Selecionar ação "Redigir"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Na caixa **Entradas**, forneça as entradas desejadas para criar a saída.

   Neste exemplo, ao clicar dentro da caixa **Entradas**, a lista de conteúdo dinâmico é exibida para que você possa selecionar as variáveis criadas anteriormente:

   ![Selecione entradas para usar para a ação "Compor"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está o exemplo concluído da ação **Redigir**: 

   ![Exemplo acabado para ação "Compor"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição de fluxo de trabalho subjacente, consulte a [Ação redigir](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Redigir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Redigir**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Redigir**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Redigir**, selecione **Saída**.

   Este exemplo usa a ação **Enviar um e-mail** e inclui os campos **Saída** no corpo e assunto do e-mail:

   ![Campos "Saída" para a ação "Compor"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Redigir"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de tabela CSV

Para criar uma tabela CSV (Comma-separated value, valor separado por comma) que tenha as propriedades e valores dos objetos JSON (JavaScript Object Notation, notação de objetos javascript) em uma matriz, use a ação **de tabela Criar CSV.** Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela CSV**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Criar tabela CSV** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. No aplicativo lógico em que você quer criar a tabela CSV, siga uma destas etapas: 

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `create csv table` como o filtro. Na lista de ações, selecione a ação **Criar tabela CSV.**

   ![Selecionar ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela CSV**.

   Aqui está o exemplo concluído da ação **Criar tabela CSV**: 

   ![Exemplo acabado para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="customize-table-format"></a>Personalizar o formato da tabela

Por padrão, a propriedade **Colunas** é definida para criar automaticamente as colunas de tabela com base nos itens do array. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **Colunas** e selecione **Personalizado**.

1. Na propriedade **Cabeçalho,** especifique o texto de cabeçalho personalizado a ser usado em vez disso.

1. Na propriedade **Valor,** especifique o valor personalizado a ser usado.

Para retornar valores da matriz, [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item) você pode usar a função com a ação **de tabela Criar CSV.** Em `For_each` um loop, você pode usar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você queira colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para devolver apenas esses valores, siga estas etapas para trabalhar na exibição do designer ou na exibição de código. Aqui está o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho em vista de designer

Na ação, mantenha a coluna **Cabeçalho** vazia. Em cada linha na coluna **Valor,** desfaça cada propriedade de array desejada. Cada linha em **Valor** retorna todos os valores para a propriedade de matriz especificada e se torna uma coluna em sua tabela.

1. Em **Valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista de conteúdo dinâmico, selecione **Expressão**.

1. No editor de expressões, digite esta expressão que especifica o valor de propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo: 

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereferência "Descrição" para "Criar tabela CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de array desejada. Quando você termina, sua ação se parece com este exemplo:

   ![função "item()" em "Criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para exibição de código e volte para a exibição do designer e, em seguida, reabra a ação em colapso:

   A ação **da tabela Criar CSV** agora aparece como este exemplo:

   !["Criar tabela CSV" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Trabalho na visualização de código

Na definição JSON da ação, `columns` dentro da `header` matriz, defina a propriedade como uma seqüência vazia. Para `value` cada propriedade, desreferenciar cada propriedade de array que você deseja.

1. Na barra de ferramentas do designer, selecione **'Exibição de código '''''Código'.**

1. No editor de código, na `columns` matriz de `header` ação, `value` adicione a propriedade vazia e esta expressão para cada coluna de valores de matriz que você deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo: 

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Volte para a visão do designer e reabra a ação em colapso.

   A ação **de tabela Criar CSV** agora aparece como este exemplo, e as expressões resolveram para versões mais descritivas:

   !["Criar tabela CSV" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela CSV** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela CSV**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela CSV**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela CSV**, selecione **Saída**. 

   Este exemplo usa o Office 365 Outlook Envie uma ação **de e-mail** e inclua o campo **Saída** no corpo do e-mail:

   ![Campos "Saída" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar ação de tabela HTML

Para criar uma tabela HTML que tenha as propriedades e valores dos objetos JSON (JavaScript Object Notation, notação de objetos javascript) em uma matriz, use a ação **de tabela Criar HTML.** Em seguida, é possível usar a tabela resultante em ações que seguem a ação **Criar tabela HTML**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Criar tabela HTML** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. No aplicativo lógico em que você quer criar uma tabela CSV, siga uma destas etapas:

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `create html table` como o filtro. Na lista de ações, selecione a ação **Criar tabela HTML.**

   ![Selecionar a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar a tabela.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON, para poder selecionar essas propriedades como entradas, use a ação [Analisar JSON](#parse-json-action) antes de chamar a ação **Criar tabela HTML**.

   Aqui está o exemplo concluído da ação **Criar tabela HTML**:

   ![Exemplo acabado para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="customize-table-format"></a>Personalizar o formato da tabela

Por padrão, a propriedade **Colunas** é definida para criar automaticamente as colunas de tabela com base nos itens do array. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **Colunas** e selecione **Personalizado**.

1. Na propriedade **Cabeçalho,** especifique o texto de cabeçalho personalizado a ser usado em vez disso.

1. Na propriedade **Valor,** especifique o valor personalizado a ser usado.

Para retornar valores da matriz, [ `item()` ](../logic-apps/workflow-definition-language-functions-reference.md#item) você pode usar a função com a ação **de tabela Criar HTML.** Em `For_each` um loop, você pode usar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você queira colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para devolver apenas esses valores, siga estas etapas para trabalhar na exibição do designer ou na exibição de código. Aqui está o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho em vista de designer

Na ação, mantenha a coluna **Cabeçalho** vazia. Em cada linha na coluna **Valor,** desfaça cada propriedade de array desejada. Cada linha em **Valor** retorna todos os valores para a propriedade especificada e se torna uma coluna em sua tabela.

1. Em **Valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista de conteúdo dinâmico, selecione **Expressão**.

1. No editor de expressões, digite esta expressão que especifica o valor de propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo: 

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Desreferenciar a propriedade na ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de array desejada. Quando você termina, sua ação se parece com este exemplo:

   ![função "item()" em "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para exibição de código e volte para a exibição do designer e, em seguida, reabra a ação em colapso:

   A ação **de tabela Criar HTML** agora aparece como este exemplo:

   !["Criar tabela HTML" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Trabalho na visualização de código

Na definição JSON da ação, `columns` dentro da `header` matriz, defina a propriedade como uma seqüência vazia. Para `value` cada propriedade, desreferenciar cada propriedade de array que você deseja.

1. Na barra de ferramentas do designer, selecione **'Exibição de código '''''Código'.**

1. No editor de código, na `columns` matriz de `header` ação, `value` adicione a propriedade vazia e esta expressão para cada coluna de valores de matriz que você deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo: 

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Volte para a visão do designer e reabra a ação em colapso.

   A ação **de tabela Criar HTML** agora aparece como este exemplo, e as expressões resolveram para versões mais descritivas:

   !["Criar tabela HTML" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Criar tabela HTML** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Criar tabela HTML**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Criar tabela HTML**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Criar tabela HTML**, selecione **Saída**. 

   Este exemplo usa o Office 365 Outlook Envie uma ação **de e-mail** e inclua o campo **Saída** no corpo do e-mail:

   ![Campos de "Saída" para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ao incluir a saída da tabela HTML em uma ação de e-mail, certifique-se de definir a propriedade **Is HTML** como **Sim** nas opções avançadas da ação de e-mail. Dessa forma, a ação de e-mail formata corretamente a tabela HTML.

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com resultados de "Criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação filtrar matriz

Para criar uma matriz menor que tenha itens, que atendam a critérios específicos, a partir de uma matriz existente, use a ação **'Filter array'.** Em seguida, é possível usar a matriz filtrada em ações que seguem após a ação **Filtrar matriz**.

> [!NOTE]
> Qualquer texto de filtro que você usa nessa condição diferencia maiúsculas de minúsculas. Além disso, essa ação não pode alterar o formato ou os componentes dos itens na matriz. 
> 
> Para ações que usam a saída de matriz da ação **Filtrar matriz**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível.
> 
> Se você chamar um ponto final HTTP e receber uma resposta JSON, use a ação **Parse JSON** para processar a resposta JSON. 
> Caso contrário, a ação **da matriz filter** pode ler apenas o corpo de resposta e não a estrutura da carga útil JSON.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Filtrar matriz** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Filtrar matriz](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   > [!NOTE]
   > Embora este exemplo use uma matriz de inteiros simples, essa ação é especialmente útil para matrizes de objeto JSON em que é possível filtrar com base em propriedades e valores dos objetos.

   ![Aplicativo de lógica de amostra inicial para ação "Filter array"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. No aplicativo lógico em que você quer criar a matriz filtrada, siga uma destas etapas: 

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para ação "Filter array"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira `filter array` como o filtro. Na lista de ações, selecione a ação **'Filtro' de matriz.**

   ![Selecionar a ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Na caixa **De**, forneça a matriz ou a expressão que você quer para criar o filtro.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a saída de matriz para criar a matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Para a condição, especifique os itens da matriz para comparar, selecione o operador de comparação e o valor de comparação.

   Este exemplo `item()` usa a função para acessar cada item na matriz, enquanto a ação **filter array** pesquisa itens de array cujo valor é maior que um:

   ![Exemplo acabado para ação "Filter array"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação consultar](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Filtrar matriz** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Filtrar matriz**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Filtrar matriz**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for aberta, selecione **Expressão**. Para obter a saída de matriz da ação **Filtrar matriz**, digite esta expressão que inclui o nome da ação **Filtrar matriz**:

   `@actionBody('Filter_array')`

   Este exemplo usa o Office 365 Outlook Envie uma ação de **e-mail** e inclua as saídas da expressão **actionBody('Filter_array')** no corpo do e-mail:

   ![Saídas de ação da ação "Filter array"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Ação unir

Para criar uma string que tenha todos os itens de uma matriz e separe esses itens com um caractere delimitador específico, use a ação **Participar.** Em seguida, é possível usar a cadeia de caracteres em ações que seguem após a ação **Unir**.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Unir** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Unir](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. Esta ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para ação "Join"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. No aplicativo lógico em que você quer criar a cadeia de caracteres a partir de uma matriz, siga uma destas etapas:

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para ação "Participar"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira `join` como o filtro. Na lista de ações, selecione esta ação: **Unir**

   ![Selecione a ação "Participar"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Na caixa **De**, forneça a matriz que contém os itens que você quer unir como uma cadeia de caracteres.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:  

   ![Selecionar a saída de matriz para criar a cadeia de caracteres](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Na caixa **Unir com**, insira o caractere desejado para separar cada item da matriz. 

   Este exemplo usa dois-pontos (:) como separador.

   ![Fornecer o caractere separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte a [Ação unir](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Unir** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Unir**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Unir**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, embaixo da ação **Unir**, selecione **Saída**. 

   Este exemplo usa o Office 365 Outlook Envie uma ação **de e-mail** e inclua o campo **Saída** no corpo do e-mail:

   ![Campos "Saída" para a ação "Participar"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Unir"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Ação analisar JSON

Para referenciar ou acessar propriedades no conteúdo JSON (JavaScript Object Notation, notação de objeto sinuoso), você pode criar campos ou tokens fáceis de usar para essas propriedades usando a ação **Parse JSON.** Dessa forma, é possível selecionar as propriedades na lista de conteúdo dinâmica ao especificar entradas para o aplicativo lógico. Para esta ação, é possível fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo de JSON de exemplo ou carga.

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Analisar JSON** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Analisar JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para ação "Parse JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. No aplicativo lógico em que você quer analisar o conteúdo JSON, siga uma destas etapas:

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para ação "Parse JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira `parse json` como o filtro. Na lista de ações, selecione **a ação Parse JSON.**

   ![Selecionar a ação "Analisar JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Na caixa **Conteúdo**, forneça o conteúdo JSON que você quer analisar.

   Neste exemplo, ao clicar dentro da caixa **Conteúdo**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar o objeto JSON para ação Analisar JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Insira o esquema JSON que descreve o conteúdo do JSON que você está analisando.

   Neste exemplo, aqui está o esquema JSON:

   ![Fornecer o esquema JSON para o objeto JSON a ser analisado](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se você não tiver o esquema, é possível gerar esse esquema a partir do conteúdo JSON ou da *carga* que estiver analisando. 
   
   1. Na ação **Analisar JSON**, selecione a opção **Usar carga de amostra para gerar esquema**.

   1. Em **Enter ou colar uma amostra de carga JSON,** forneça o conteúdo JSON e selecione **Feito**.

      ![Inserir o conteúdo do JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição no fluxo de trabalho subjacente, consulte [Ação analisar JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Analisar JSON** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Analisar JSON**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Analisar JSON**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico é aberta, na ação **Analisar JSON**, será possível selecionar as propriedades do conteúdo JSON analisado.

   Este exemplo usa o Office 365 Outlook Envie uma ação **de e-mail** e inclua os campos **Primeiro Nome,** **Sobrenome**e **E-mail** no corpo do e-mail:

   ![Propriedades JSON na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Aqui está a ação de e-mail concluída:

   ![Exemplo acabado para ação de e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**. 

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "Parse JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Ação selecionar

Para criar uma matriz que tenha objetos JSON construídos a partir de valores em uma matriz existente, use a ação **Select.** Por exemplo, é possível criar um objeto JSON para cada valor em uma matriz de inteiros, especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E embora seja possível alterar os componentes nesses objetos JSON, a matriz de saída sempre tem o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para ações que usam a saída de matriz da ação **Selecionar**, essas ações devem aceitar matrizes como entrada, ou talvez seja necessário transformar a matriz de saída em outro formato compatível. 

Se você preferir trabalhar no editor de exibição de código, é possível copiar as definições da ação **Selecionar** e **Inicializar variável** de exemplo deste artigo na definição do fluxo de trabalho subjacente do próprio aplicativo lógico: [Exemplos de código de operação de dados - Selecionar](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho **Recorrência** e uma ação **Inicializar variável**. A ação está configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar o aplicativo lógico posteriormente, é possível executar manualmente o aplicativo sem aguardar que o gatilho seja disparado.

   ![Aplicativo de lógica de amostra inicial para ação "Select"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. No aplicativo lógico em que você quer criar a matriz, siga uma destas etapas: 

   * Para adicionar uma ação a última etapa, selecione **Nova etapa**.

     ![Selecione "Novo passo" para ação "Selecionar"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Para adicionar uma ação entre as etapas, mova o mouse**+** sobre a seta de conexão para que o sinal de mais () apareça. Selecione o sinal de adição e **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `select` como o filtro. Na lista de ações, selecione a **ação Selecionar.**

   ![Selecionar a ação "Selecionar"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Na caixa **De**, especifique a matriz de origem desejada.

   Neste exemplo, ao clicar dentro da caixa **De**, a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar a matriz de origem para a ação Selecionar](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Na coluna esquerda da caixa **Mapear**, forneça o nome de propriedade que você quer atribuir cada valor na matriz de origem. Na coluna direita, especifique uma expressão que representa o valor que você quer atribuir a propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada `item()` valor na matriz inteiro usando a função em uma expressão que acessa cada item de matriz. 

   ![Especificar a propriedade e os valores do objeto JSON para criar o array](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação concluída:

   ![Exemplo acabado para ação "Selecionar"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Para obter mais informações sobre essa ação na definição do fluxo de trabalho subjacente, consulte [Ação selecionar](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para confirmar se a ação **Selecionar** cria os resultados esperados, envie uma notificação a você mesmo que inclua a saída da ação **Selecionar**.

1. No aplicativo lógico, adicione uma ação que possa enviar a você os resultados da ação **Selecionar**.

1. Nessa ação, clique em qualquer lugar em que você quer exibir os resultados. Quando a lista de conteúdo dinâmico for aberta, selecione **Expressão**. Para obter a saída de matriz da ação **Selecionar**, insira esta expressão que inclui o nome da ação **Selecionar**:

   `@actionBody('Select')`

   Este exemplo usa o Office 365 Outlook Envie uma ação `@actionBody('Select')` de **e-mail** e inclua as saídas da expressão no corpo do e-mail:

   ![Saídas de ação da ação "Select"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Agora, execute manualmente o aplicativo lógico. Na barra de ferramentas do designer, selecione **Executar**.

   Com base no conector de e-mail que você usou, aqui estão os resultados obtidos:

   ![E-mail com os resultados da ação "'Selecionar"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
