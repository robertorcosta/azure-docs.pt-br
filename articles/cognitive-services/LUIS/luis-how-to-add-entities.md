---
title: Adicionar entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Crie entidades para extrair dados-chave de expressões de usuários em aplicativos de Compreensão de Idiomas (LUIS). Os dados da entidade extraídos são usados pelo aplicativo do cliente para fazer as solicitações do cliente fullfil.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220903"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados 

Crie entidades para extrair dados-chave de expressões de usuários em aplicativos de Compreensão de Idiomas (LUIS). Os dados da entidade extraídos são usados pelo aplicativo do cliente para solicitar o cliente fullfil.

A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. As entidades descrevem informações relevantes para a intenção e, às vezes, elas são essenciais para seu aplicativo realizar sua tarefa. Você pode criar entidades quando você adiciona um enunciado de exemplo a uma intenção ou além de (antes ou depois) adicionar um enunciado de exemplo a uma intenção.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planejar entidades, em seguida, criar e rotular

Entidades aprendidas por máquina podem ser criadas a partir das expressões de exemplo ou criadas a partir da página **Entidades.** 

Em geral, uma prática recomendada é gastar tempo planejando as entidades antes de criar uma entidade aprendida por máquina no portal. Em seguida, crie a entidade aprendida pela máquina a partir da expressão de exemplo com tantos detalhes nos subcomponentes e descritores e restrições como você sabe na época. O [tutorial de entidade descomposável](tutorial-machine-learned-entity.md) demonstra como usar este método. 

Como parte do planejamento das entidades, você pode saber que precisa de entidades que combinem texto (como entidades pré-construídas, entidades de expressão regulares ou entidades de lista). Você pode criá-las a partir da página **Entidades** antes de serem rotuladas em enunciados de exemplo. 

Ao rotular, você pode rotular entidades individuais e construir uma entidade aprendida por máquina sinuosa. Ou você pode começar com uma entidade de aprendizagem de máquina suspica e se decompor em entidades infantis. 

> [!TIP] 
>Rotular todas as palavras que podem indicar uma entidade, mesmo que as palavras não sejam usadas quando extraídas no aplicativo cliente. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Criando uma entidade antes ou com rotulagem

Use a tabela a seguir para entender quais entidades criam ou adicionam cada entidade ao aplicativo. 

|Tipo de entidade|Onde criar entidade no portal LUIS|
|--|--|
|Entidade com aprendizado de máquina|Entidades ou Detalhes de Intenção|
|Entidade de lista|Entidades ou Detalhes de Intenção|
|Entidade de expressão regular|Entidades|
|Entidade pattern.any|Entidades|
|Entidade predefinida|Entidades|
|Entidade de domínio pré-construída|Entidades|

Você pode criar todas as entidades a partir da página **Entidades,** ou pode criar algumas das entidades como parte da rotulagem da entidade na enunciação de exemplo na página **detalhe satisfaz a intenção.** Você só pode _rotular_ uma entidade em uma expressão de exemplo da página **detalhes da Intenção.** 

## <a name="create-a-machine-learned-entity"></a>Crie uma entidade aprendida por máquina

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Crie uma entidade de correspondência de texto

Use entidades que combinam texto fornecem várias maneiras de extrair dados:

|Entidades que combinam texto|Finalidade|
|--|--|
|[Entidade de lista](#add-list-entities-for-exact-matches)|lista de nomes canônicos, juntamente com sinônimos como formas alternativas|
|Entidade de expressão regular|combinar texto usando uma entidade de expressão regular|
|[Entidade predefinida](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|corresponder a tipos de dados comuns, como número, e-mail, data|
|Entidade de domínio pré-construída|corresponder usando domínios de assunto selecionados|
|[Pattern.any](#add-a-patternany-entity)| para combinar entidades que podem ser facilmente confundidas com o texto circundante|  

As entidades pré-construídas trabalham sem fornecer dados de treinamento personalizados. As outras entidades precisam que você forneça dados de treinamento do cliente (como itens da entidade da Lista) ou uma expressão (como uma expressão ou padrão regular.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

1. No portal LUIS, acesse a seção **Gerenciar** e, em seguida, a página **Entidades.** 
1. Selecione **+ Criar,** em seguida, selecione o tipo de entidade. 
1. Continue configurando a entidade e selecione **Criar** quando terminar. 

### <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades de lista para correspondências exatas

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas. Enquanto você, como autor, pode mudar a lista, LUIS não vai crescer ou encolher a lista. Você também pode importar para uma entidade de lista existente usando um formato de lista .json (reference-entity-list.md#example-json-to-import-into-list-entity). 

A lista a seguir demonstra o nome canônico e os sinônimos. 

|Cor - nome do item da lista|Cor - sinônimos|
|--|--|
|Vermelho|carmesim, sangue, maçã, carro de bombeiros|
|Azul|céu, azul, cobalto|
|Verde|kelly, limão|

Use o procedimento para criar uma entidade de lista. Uma vez que a entidade de lista é criada, você não precisa rotular expressões de exemplo em uma intenção. Itens de lista e sinônimos são combinados usando texto exato. 

1. Na seção **Construir,** selecione **Entidades** no painel esquerdo e selecione **+ Criar**.

1. Na caixa de diálogo **Criar um tipo de entidade,** digite o nome da entidade, como `Colors` e selecione **Lista**.
1. Na caixa de diálogo **Criar uma entidade de lista,** na nova **sublista....**, digite o nome do item da lista, como `Green`, em seguida, adicione sinônimos.

    > [!div class="mx-imgBorder"]
    > ![Crie uma lista de cores como uma entidade de lista na página de detalhes Entidade.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Quando terminar de adicionar itens de lista e sinônimos, selecione **Criar**.

    Quando terminar com um grupo de alterações no aplicativo, lembre-se **de treinar** o aplicativo. Não treine o aplicativo após uma única mudança. 

    > [!NOTE]
    > Este procedimento demonstra a criação e rotulagem de uma entidade de lista a partir de uma enunciada de exemplo na página **detalhes da Intenção.** Você também pode criar a mesma entidade a partir da página **Entidades.**

## <a name="add-a-role-for-an-entity"></a>Adicione um papel para uma entidade

Um papel é um subtipo nomeado de uma entidade, baseado no contexto. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicione um papel para distinguir diferentes contextos

Na expressão a seguir, há dois locais, e cada um é especificado `to` semanticamente pelas palavras ao seu redor como e `from`: 

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, `origin` adicione `destination` e papéis a uma entidade geografiaV2 pré-construída.

1. Na seção **Build**, selecione **Entities** no painel esquerdo.

1. Selecione **+ Adicione entidade pré-construída**. Selecione **geografiaV2** e selecione **Feito**. Isso adiciona uma entidade pré-construída ao aplicativo.
    
    Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir esse problema. 

1. Selecione a entidade geografia v2 recém-adicionada na lista de entidades da página **Entidades.** 
1. Para adicionar uma nova **+** função, selecione ao lado **de Nenhuma função adicionada**.
1. Na **função Tipo...** caixa de texto, `Origin` digite o nome da função e digite. Adicione um nome `Destination` de segunda função de então digite. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição da função Origem à entidade Local](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    O papel é adicionado à entidade pré-construída, mas não é adicionado a quaisquer declarações usando essa entidade. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Rotular texto com um papel em um enunciado de exemplo

1. Vá para a página de detalhes da Intenção, que tem frases de exemplo que usam a função. 
1. Para rotular com a função, selecione o rótulo entidade (linha sólida em texto) no enunciado de exemplo e selecione Exibir na paleta de **entidades** na lista suspenso. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de seleção de exibição na paleta de entidades](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    A paleta de entidades abre para a direita. 

1. Selecione a entidade, depois vá para a parte inferior da paleta e selecione a função. 

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de seleção de exibição na paleta de entidades](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Adicione um padrão.qualquer entidade

[Pattern.any](luis-concept-entity-types.md) entities são válidos apenas em [padrões,](luis-how-to-model-intent-pattern.md)não em declarações de exemplo de intenções. Esse tipo de entidade ajuda o LUIS a encontrar o fim de entidades de comprimento variável e escolha de palavras. Como essa entidade é usada em um padrão, o LUIS sabe onde o final da entidade está no modelo de emissão.

### <a name="steps-to-create-a-patternany-entity"></a>Etapas para criar um padrão.qualquer entidade

1. Na seção **Construir,** selecione **Entidades** no painel esquerdo e selecione **+ Criar**.

1. Na caixa de diálogo **Escolher um tipo de entidade,** digite o nome da entidade na caixa **Nome** e selecione **'Padrão.Qualquer um'** como o **Tipo,** em seguida, **selecione Criar**.

    Uma vez que você [cria um enunciado padrão](luis-how-to-model-intent-pattern.md) usando essa entidade, a entidade é extraída com um algoritmo combinado de correspondência de texto e aprendizado de máquina. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Crie um enunciado de modelo padrão para usar o padrão.qualquer entidade

Para usar a entidade pattern.any, adicione um padrão na página **Padrões**, na seção **Melhorar desempenho do aplicativo**, com a sintaxe correta de chave, como `Where is **{HumanResourcesFormTitle}** on the server?`.

Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir esse problema. 

## <a name="do-not-change-entity-type"></a>Não mude o tipo de entidade

O LUIS não permite alterar o tipo da entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Depois que a entidade é criada, em cada enunciado, remova o antigo nome da entidade rotulada e adicione o novo nome da entidade. Depois que todos os enunciados tiverem sido remarcados, exclua a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Usar modelos predefinidos](howto-add-prebuilt-models.md) 

Saiba mais sobre:
* Como [treinar](luis-how-to-train.md)
* Como [testar](luis-interactive-test.md)
* Como [publicar](luis-how-to-publish-app.md)
* Padrões:
    * [Conceitos](luis-concept-patterns.md)
    * [Sintaxe](reference-pattern-syntax.md)
* [Entidades pré-construídas Repositório GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Conceitos de Extração de Dados](luis-concept-data-extraction.md)


 
