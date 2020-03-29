---
title: Design de aplicativo iterativo - LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422594"
---
# <a name="iterative-app-design-for-luis"></a>Design de aplicativo iterativo para LUIS

Um aplicativo de Compreensão de Idiomas (LUIS) aprende e executa de forma mais eficiente com a iteração. Aqui está um ciclo típico de iteração:

* Criar nova versão
* Edite o esquema do aplicativo LUIS. Isso inclui:
    * Intenções com enunciados de exemplo
    * Entidades
    * Recursos
* Treinar, testar e publicar
    * Teste no ponto final da previsão para aprendizado ativo
* Coletar dados de consultas de ponto final

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Construindo um esquema LUIS

O esquema de um aplicativo define o que o usuário está pedindo (a _intenção_ ou _intenção_ ) e quais partes da intenção fornecem detalhes (chamadas entidades ) que são _usados_para ajudar a determinar a resposta. 

O esquema do aplicativo deve ser específico para os domínios do aplicativo para determinar palavras e frases relevantes, bem como determinar o ordenamento típico de palavras. 

As enunciações de exemplo representam entradas do usuário, como fala ou texto reconhecidos, que o aplicativo espera em tempo de execução. 

O esquema requer intenções, e _deve ter_ entidades. 

### <a name="example-schema-of-intents"></a>Exemplo esquema de intenções

O esquema mais comum é um esquema de intenções organizado com intenções. Este tipo de esquema usa LUIS para determinar a intenção de um usuário. 

O tipo de esquema de intenção pode ter entidades se ajudar luis a determinar a intenção do usuário. Por exemplo, uma entidade de transporte (como um descritor para uma intenção) ajuda o LUIS a determinar uma intenção de envio. 

### <a name="example-schema-of-entities"></a>Exemplo de esquema de entidades

Um esquema de entidade se concentra nas entidades, que são os dados extraídos das declarações dos usuários. Por exemplo, se um usuário dissesse: "Eu gostaria de pedir três pizzas." Há duas entidades que seriam extraídas: _três_ e _pizzas._ Estes são usados para ajudar a cumprir a intenção, que era fazer um pedido. 

Para um esquema de entidade, a intenção do enunciado é menos importante para a aplicação do cliente. 

Um método comum de organizar um esquema de entidade é adicionar todos os exemplos de expressões à intenção **De Nenhum.** 

### <a name="example-of-a-mixed-schema"></a>Exemplo de um esquema misto

O esquema mais poderoso e maduro é um esquema de intenções com uma gama completa de entidades e recursos. Esse esquema pode começar como um esquema de intenção ou entidade e crescer para incluir conceitos de ambos, já que o aplicativo do cliente precisa dessas informações. 

## <a name="add-example-utterances-to-intents"></a>Adicione expressões de exemplo a intenções

LUIS precisa de alguns exemplos de declarações em cada **intenção.** As declarações de exemplo precisam de variação suficiente da escolha da palavra e da ordem das palavras para poder determinar para qual intenção a enunciada é destinada. 

> [!CAUTION]
> Não adicione expressões de exemplo em massa. Comece com 15 a 30 exemplos específicos e variados. 

Cada enunciado de exemplo precisa ter quaisquer **dados necessários para extrair** projetados e rotulados com **entidades**. 

|Elemento Key|Finalidade|
|--|--|
|Intencional|**Classifique** as declarações do usuário em uma única intenção ou ação. Os exemplos incluem `BookFlight` e `GetWeather`.|
|Entidade|**Extrair** dados do enunciado necessário para completar a intenção. Exemplos incluem data e hora da viagem e localização.|

Um aplicativo LUIS pode ser projetado para ignorar declarações que não são relevantes para o domínio de um aplicativo, atribuindo a expressão à intenção **De Nenhum.**

## <a name="test-and-train-your-app"></a>Teste e treine seu aplicativo

Depois de ter de 15 a 30 diferentes expressões de exemplo em cada intenção, com as entidades exigidas rotuladas, você precisa testar e [treinar](luis-how-to-train.md) seu aplicativo LUIS. 

## <a name="publish-to-a-prediction-endpoint"></a>Publicar para um ponto final de previsão

O aplicativo LUIS deve ser publicado para que esteja disponível para você nas [regiões de ponto final](luis-reference-regions.md)de previsão da lista .

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

Você pode testar seu aplicativo LUIS publicado a partir do ponto final de previsão HTTPS. O teste do ponto final da previsão permite que luis escolha quaisquer expressões com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Crie uma nova versão para cada ciclo

Cada versão é um instantâneo no tempo do aplicativo LUIS. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para uma versão mais antiga do que tentar remover intenções e expressões para um estado anterior.

A ID da versão é composta de caracteres, dígitos ou '.' e não pode ser maior do que 10 caracteres.

A versão inicial (0.1) é a versão ativa padrão. 

### <a name="begin-by-cloning-an-existing-version"></a>Comece clonando uma versão existente

Clonar uma versão existente para usar como ponto de partida para cada nova versão. Depois de clonar uma versão, a nova versão se torna a versão **ativa.** 

### <a name="publishing-slots"></a>Slots de publicação

Você pode publicar para os slots de palco e/ou de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isso é útil para verificar alterações antes de publicar na produção, que está disponível para bots ou outros aplicativos de chamada LUIS. 

As versões treinadas não estão disponíveis automaticamente no [ponto final](luis-glossary.md#endpoint)do seu aplicativo LUIS . Você deve [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que ela esteja disponível no ponto final do aplicativo LUIS. Você pode publicar em **Staging** and **Production**, dando-lhe duas versões do aplicativo disponíveis no ponto final. Se mais versões do aplicativo precisarem estar disponíveis em um ponto final, você deve exportar a versão e reimportá-la para um novo aplicativo. O novo aplicativo tem uma ID do aplicativo diferente.

### <a name="import-and-export-a-version"></a>Importar e exportar uma versão

Uma versão pode ser importada no nível do aplicativo. Essa versão se torna a versão ativa `versionId` e usa o ID da versão na propriedade do arquivo do aplicativo. Você também pode importar para um aplicativo existente, no nível da versão. A nova versão torna-se a versão ativa. 

Uma versão também pode ser exportada no nível do aplicativo ou da versão. A única diferença é que a versão exportada do nível do aplicativo é a versão ativa no momento, enquanto no nível da versão, é possível escolher qualquer versão para ser exportada na página **[Configurações](luis-how-to-manage-versions.md)**. 

O arquivo exportado **não** contém:

* Informações aprendidas por máquina, porque o aplicativo é retreinado depois de importado
* Informações do contribuinte

Para fazer backup do seu esquema de aplicativo LUIS, exporte uma versão do [portal LUIS](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gerenciar alterações de contribuintes com versões e colaboradores

O LUIS usa o conceito de contribuintes para um aplicativo, fornecendo permissões de nível de recurso do Azure. Combine esse conceito com a versão para fornecer colaboração direcionada. 

Use as seguintes técnicas para gerenciar as alterações de contribuinte saem do seu aplicativo.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerenciar várias versões dentro do mesmo aplicativo

Comece por [clonagem](luis-how-to-manage-versions.md#clone-a-version) de uma versão base para cada autor. 

Cada autor faz alterações em sua própria versão do aplicativo. Quando o autor estiver satisfeito com o modelo, exporte as novas versões para arquivos JSON.  

Aplicativos exportados, arquivos .json ou .lu, podem ser comparados para alterações. Combine os arquivos para criar um único arquivo da nova versão. Alterar `versionId` a propriedade para significar a nova versão mesclada. Importe essa versão para o aplicativo original. 

Esse método permite que você tenha uma versão ativa, uma versão do estágio e uma versão publicada. Você pode comparar os resultados da versão ativa com uma versão publicada (estágio ou produção) no [painel de teste interativo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerenciar várias versões como aplicativos

[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa o aplicativo é o proprietário da versão. Quando ela terminar de modificar o aplicativo, exporte a versão. 

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados com a exportação base quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

Saiba mais sobre a autoria de contribuições de [colaboradores](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Revisar as declarações de ponto final para iniciar o novo ciclo iterativo

Quando você terminar com um ciclo de iteração, você pode repetir o processo. Comece com [a revisão de predições de enunciados](luis-how-to-review-endpoint-utterances.md) de ponto final LUIS marcado com baixa confiança. Verifique estas expressões para verificar tanto a intenção predita correta quanto a entidade correta e completa extraída. Depois de revisar e aceitar alterações, a lista de revisão deve estar vazia.  

## <a name="next-steps"></a>Próximas etapas

Conheça conceitos sobre [colaboração](luis-concept-keys.md).
