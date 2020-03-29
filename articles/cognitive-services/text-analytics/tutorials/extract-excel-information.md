---
title: Extrair informações no Excel usando análise de texto e automatização de energia
titleSuffix: Azure Cognitive Services
description: Aprenda a extrair texto do Excel sem ter que escrever código, usando Análise de Texto e Automatização de Energia.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201183"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrair informações no Excel usando análise de texto e automatização de energia 

Neste tutorial, você criará um fluxo power automate para extrair texto em uma planilha do Excel sem ter que escrever código. 

Esse fluxo pegará uma planilha de questões relatadas sobre um complexo de apartamentos, e as classificará em duas categorias: encanamento e outra. Também extrairá os nomes e números de telefone dos inquilinos que os enviaram. Por fim, o fluxo anexará essas informações à planilha do Excel. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Use o Power Automate para criar um fluxo
> * Carregar dados do Excel do OneDrive para Negócios
> * Extrair texto do Excel e enviá-lo para a API de análise de texto 
> * Use as informações da API para atualizar uma planilha do Excel.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Microsoft Azure. [Inicie uma avaliação gratuita](https://azure.microsoft.com/free/) ou [entre](https://portal.azure.com/).
- Um recurso de análise de texto. Se você não tiver um, você pode [criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e usar o nível gratuito para completar este tutorial.
- A [chave e o ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerado para você durante a inscrição.
- Uma planilha contendo problemas de inquilinos. Dados de exemplo são fornecidos no GitHub
- Escritório 365, com OneDrive para Negócios.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Adicione o arquivo Excel ao OneDrive for Business

Baixe o exemplo do arquivo Excel do [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Este arquivo deve ser armazenado em sua conta OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exemplos do arquivo Excel.":::

Os problemas são relatados em texto bruto. Usaremos o Reconhecimento de Entidade nomeado da API de Análise de Texto para extrair o nome e o número de telefone da pessoa. Em seguida, o fluxo procurará a palavra "encanamento" na descrição para categorizar os problemas. 

## <a name="create-a-new-power-automate-workflow"></a>Crie um novo fluxo de trabalho power automate

Acesse o [site do Power Automate](https://preview.flow.microsoft.com/)e faça login. Em seguida, clique **em Criar** e **Agendar fluxo**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="A tela de criação de fluxo.":::


Na **Página de fluxo programada,** inicialize seu fluxo com os seguintes campos:

|Campo |Valor  |
|---------|---------|
|**Nome do fluxo**     | **Revisão agendada** ou outro nome.         |
|**Começando**     |  Digite a data e a hora atuais.       |
|**Repita cada**     | **1 hora**        |

## <a name="add-variables-to-the-flow"></a>Adicionar variáveis ao fluxo

> [!NOTE]
> Se você quiser ver uma imagem do fluxo concluído, você pode baixá-la no [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Crie variáveis que representem as informações que serão adicionadas ao arquivo Excel. Clique em **Nova etapa** e procure a **variável Initialize**. Faça isso quatro vezes, para criar quatro variáveis.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicializar variáveis.":::

Adicione as seguintes informações às variáveis criadas. Eles representam as colunas do arquivo Excel. Se alguma variável for colapsada, você pode clicar nelas para expandi-las.

| Ação |Nome   | Type | Valor |
|---------|---------|---|---|
| Inicializar variável | var_person | String | Person |
| Inicializar variável 2 | var_phone | String | Phone_Number |
| Inicializar variável 3 | var_plumbing | String | Encanamento |
| Inicializar variável 4 | var_other | String | Outros | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informações contidas nas variáveis de fluxo":::

## <a name="read-the-excel-file"></a>Leia o arquivo excel

Clique em **Nova etapa** e **digite Excel,** em seguida, selecione **Listas de linhas presentes em uma tabela** da lista de ações.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="adicionar linhas excel.":::

Adicione o arquivo Excel ao fluxo preenchendo os campos nesta ação. Este tutorial requer que o arquivo tenha sido carregado no OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="adicionar linhas excel.":::

Clique em **Nova etapa** e adicione uma Aplicar **a cada** ação.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="adicionar um comando aplicar.":::

Clique em **Selecionar uma saída da etapa anterior**. Na caixa de conteúdo Dinâmico que aparece, selecione **o valor**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecione saída do arquivo excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Envie uma solicitação para a API de análise de texto

Se você ainda não o fez, você precisa criar um [recurso de Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure.

### <a name="create-a-text-analytics-connection"></a>Crie uma conexão de análise de texto

No **Aplicar a cada um,** clique **em Adicionar uma ação**. Vá para a **página-chave e ponto final** do seu recurso text analytics no portal Azure e obtenha a chave e o ponto final do recurso Text Analytics.

No seu fluxo, insira as seguintes informações para criar uma nova conexão Text Analytics.

> [!NOTE]
> Se você já criou uma conexão Text Analytics e deseja alterar os detalhes da sua conexão, clique na elipse no canto superior direito e clique em **+ Adicionar nova conexão**.

| Campo           | Valor                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nome da Conexão | Um nome para a conexão com o recurso Text Analytics. Por exemplo, `TAforPowerAutomate`. |
| Chave de conta     | A chave para o recurso Text Analytics.                                                                                   |
| URL do site        | O ponto final para o recurso Text Analytics.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="extract-the-excel-content"></a>Extrair o conteúdo do excel 

Depois que a conexão for criada, procure análise **de texto** e selecione **Entidades**. Isso extrairá informações da coluna de descrição do problema.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Clique no **campo Texto** e selecione **Descrição** nas janelas de conteúdo dinâmica que aparecem. Inscreva-se `en` para o idioma. (Clique em Mostrar opções avançadas se você não ver o Idioma)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


## <a name="extract-the-person-name"></a>Extrair o nome da pessoa

Em seguida, encontraremos o tipo de entidade da pessoa na saída do Text Analytics. Dentro do **Aplicar a cada,** clique **em Adicionar uma ação**e crie outra Aplicar a **cada** ação. Clique na caixa de texto e selecione **Entidades** na janela Conteúdo dinâmico que aparece.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Dentro do recém-criado **Aplicar a cada 2** ações, clique em Adicionar uma **ação**e adicione um controle **de Condição.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na janela Condição, clique na primeira caixa de texto. Na janela Conteúdo Dinâmico, procure **por Tipo de Entidades** e selecione-o.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Certifique-se de que a segunda caixa está **definida como igual a**. Em seguida, selecione a `var_person` terceira caixa e procure na janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na condição **If yes,** digite o Excel e selecione **Atualizar uma linha**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Digite as informações do Excel e atualize os campos **Coluna Chave,** **Valor-chave** e **Nome da Pessoa.** Isso anexará o nome detectado pela API à planilha Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="get-the-phone-number"></a>Pegue o número de telefone.

Minimize a **aplicação a cada 2** ações clicando no nome. Em seguida, adicione outro **Aplicar a cada** ação, como antes. ele será nomeado **Aplicar a cada 3**. Selecione a caixa de texto e adicione **entidades** como saída para esta ação. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

**Dentro Aplicar a cada 3**, adicione um controle de **condição.** Ele será nomeado **Condição 2**. Na primeira caixa de texto, procure e adicione **Tipo de Entidades** na janela de conteúdo Dinâmico. Certifique-se de que a caixa central está definida como **igual a**. Em seguida, na caixa `var_phone`de texto certa, digite . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na condição **Se sim,** adicione **uma ação Atualizar uma linha.** Em seguida, digite as informações como fizemos acima, para a coluna de números de telefone da folha do Excel. Isso anexará o número de telefone detectado pela API à planilha excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


## <a name="get-the-plumbing-issues"></a>Obter os problemas de encanamento

Minimizar **Aplicar a cada 3** clicando no nome. Em seguida, crie outro **Aplicar a cada um** na ação pai. Selecione a caixa de texto e adicione **Entidades** como saída para esta ação na janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


Em seguida, o fluxo verificará se a descrição do problema da linha de tabela do Excel contém a palavra "encanamento". Se sim, ele adicionará "encanamento" na coluna IssueType. Se não, entraremos no "outro".

Dentro do **Aplicar a cada 4** ações, adicione um Controle de **Condição.** Ele será nomeado **Condição 3**. Na primeira caixa de texto, procure e adicione **Descrição** do arquivo Excel, usando a janela de conteúdo Dinâmico. Certifique-se de que a caixa central diz **que contém**. Em seguida, na caixa de `var_plumbing`texto certa, encontre e selecione . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


Na condição **Se sim,** clique em **Adicionar uma ação**e selecione Atualizar uma **linha**. Em seguida, digite a informação como antes. Na coluna IssueType, `var_plumbing`selecione . Isso aplicará uma etiqueta de "encanamento" na linha.

Na **condição Se não** houver condição, clique em **Adicionar uma ação**e selecione Atualizar uma **linha**. Em seguida, digite a informação como antes. Na coluna IssueType, `var_other`selecione . Isso aplicará um rótulo "outro" à linha.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

No canto superior direito da tela, clique em **Salvar**e depois **teste**. Selecione **eu executarei a ação do gatilho.** Clique **em Salvar & Teste,** Executar **fluxo,** depois **feito**.

O arquivo Excel será atualizado em sua conta OneDrive. Vai parecer o abaixo.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A planilha atualizada do Excel.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar mais soluções](../text-analytics-user-scenarios.md)
