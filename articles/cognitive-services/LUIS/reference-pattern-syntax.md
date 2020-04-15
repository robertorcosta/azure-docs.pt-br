---
title: Referência de sintaxe padrão - LUIS
description: Crie entidades para extrair dados-chave de expressões de usuários em aplicativos de Compreensão de Idiomas (LUIS). Os dados extraídos são usados pelo aplicativo cliente.
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: cc24667f43dfedc032f52c40fc5f8fe5c80bad70
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382158"
---
# <a name="pattern-syntax"></a>Sintaxe de padrões

Sintaxe de padrão é um modelo para uma expressão. O modelo deve conter palavras e entidades que você deseja combinar, bem como palavras e [pontuação](luis-reference-application-settings.md#punctuation-normalization) que você deseja ignorar. **Não** é uma expressão regular.

> [!CAUTION]
> Os padrões incluem apenas pais de entidades aprendidas por máquina, não subcomponentes.

As entidades em padrões estão entre chaves, `{}`. Os padrões podem incluir entidades e entidades com funções. [Pattern.any](luis-concept-entity-types.md#patternany-entity) é uma entidade usada apenas em padrões.

A sintaxe padrão suporta a seguinte sintaxe:

|Função|Sintaxe|Nível de aninhamento|Exemplo|
|--|--|--|--|
|entidade| {}- suportes encaracolados|2|Onde está o formulário {entity-name}?|
|opcional|[] - colchetes quadrados<BR><BR>Há um limite de 3 em níveis de aninhamento de qualquer combinação de opcional e agrupamento |2|O ponto de interrogação é opcional [?]|
|agrupamento|() - parênteses|2|é (a \| b)|
|ou| \|- barra vertical (tubo)<br><br>Há um limite de 2 nas barras verticais (Or) em um grupo |-|Onde está o formulário ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|início e/ou fim da expressão|^ - caret|-|^começar a expressão<br>a expressão é feita^<br>^correspondência literal estrita de enunciado inteiro com {número} entidade^|

## <a name="nesting-syntax-in-patterns"></a>Sintaxe de aninhamento em padrões

A sintaxe **opcional,** com suportes quadrados, pode ser aninhada em dois níveis. Por exemplo: `[[this]is] a new form`. Este exemplo permite as seguintes expressões:

|Exemplo de expressão opcional aninhada|Explicação|
|--|--|
|esta é uma nova forma|corresponde a todas as palavras em padrão|
|é uma nova forma|corresponde a palavra opcional externa e palavras não opcionais no padrão|
|uma nova forma|corresponde apenas palavras necessárias|

A sintaxe **de agrupamento,** com parênteses, pode ser aninhada em dois níveis. Por exemplo: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Este recurso permite que qualquer uma das três entidades seja combinada.

Se entity1 é um Local com funções como origem (Seattle) e destino (Cairo) e Entidade 2 é um nome de construção conhecido de uma entidade de lista (RedWest-C), as seguintes expressões seriam mapeadas para este padrão:

|Exemplo de enunciado de agrupamento aninhado|Explicação|
|--|--|
|RedWest-C|corresponde entidade de agrupamento externo|
|Seattle|corresponde a uma das entidades de agrupamento interior|
|Cairo|corresponde a uma das entidades de agrupamento interior|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limites de aninhamento para grupos com sintaxe opcional

Uma combinação de **agrupamento** com sintaxe **opcional** tem um limite de 3 níveis de aninhamento.

|Permitido|Exemplo|
|--|--|
|Sim|( ( ( ( ( test1 &#x7c; test2 ) ] &#x7c; teste3 )|
|Não|( ( ( ( [ [ teste1 ] &#x7c; test2 ) ] &#x7c; teste3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limites de aninhamento para grupos com sintaxe ou ing

Uma combinação de **agrupamento** com sintaxe **ou ing** tem um limite de 2 barras verticais.

|Permitido|Exemplo|
|--|--|
|Sim|( teste1 teste &#x7c;2 &#x7c; (teste3 teste &#x7c;4 ) )|
|Não|( teste1 teste &#x7c;2 &#x7c; teste3 &#x7c; (teste4 &#x7c; test5 ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade em um modelo de padrão
Para adicionar uma entidade no modelo de padrão, coloque o nome de entidade entre chaves, como `Who does {Employee} manage?`.

|Padrão com entidade|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintaxe para adicionar uma entidade e função em um modelo de padrão
Uma função de entidade é denotada como `{entity:role}` com o nome de entidade, seguido por dois-pontos e, em seguida, o nome da função. Para adicionar uma entidade com uma função no modelo de padrão, coloque o nome de entidade e o nome da função entre chaves, como `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Padrão com funções de entidade|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintaxe para adicionar um pattern.any em um modelo de padrão
A entidade Pattern.any permite que você adicione uma entidade de comprimento variado para o padrão. O pattern.any pode ser qualquer comprimento desde que o modelo de padrão seja seguido.

Para adicionar uma entidade **Pattern.any** no modelo padrão, coloque a entidade Pattern.any com as chaves, como `How much does {Booktitle} cost and what format is it available in?`.

|Padrão com a entidade Pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Títulos de livro no padrão|
|--|
|Quanto custa **roubar este livro** e em que formato está disponível?|
|Quanto custa **perguntar** e em que formato está disponível?|
|Quanto custa **O curioso incidente do cachorro no tempo de noite** e em que formato está disponível?|

As palavras do título do livro não são confusas para LUIS porque LUIS sabe onde termina o título do livro, baseado no Padrão.qualquer entidade.

## <a name="explicit-lists"></a>Listas explícitas

criar uma [Lista Explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) através da API de autoria para permitir a exceção quando:

* Seu padrão contém um [Padrão.qualquer](luis-concept-entity-types.md#patternany-entity)
* E essa sintaxe padrão permite a possibilidade de uma extração de entidade incorreta com base no enunciado.

Por exemplo, supondo que você tem um padrão que com sintaxe opcional, `[]`, e sintaxe de entidade, `{}`, combinadas de uma forma para extrair dados incorretamente.

Considere o padrão '[localizar] email sobre {assunto} [de {pessoa}]'.

Nas expressões a seguir, as entidades **assunto** e **pessoa** são extraídas correta e incorretamente:

|Enunciado|Entidade|Extração correta|
|--|--|:--:|
|email sobre cachorros de Chris|assunto=cachorros<br>pessoa=Chris|✔|
|email sobre o homem de La Mancha|assunto=o homem<br>pessoa=La Mancha|X|

Na tabela anterior, o `the man from La Mancha` assunto deve ser (um título do livro), mas como o assunto inclui a palavra `from`opcional, o título é predito incorretamente.

Para corrigir esta expressão no padrão, adicione `the man from la mancha` como uma correspondência de lista explícita para a entidade {assunto} usando a [API de criação para lista explícita](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintaxe para marcar texto opcional em uma expressão de exemplo
Marque texto opcional na expressão usando a sintaxe colchete de expressão regular, `[]`. O texto opcional pode aninhar colchetes até apenas dois colchetes.

|Padrão com texto opcional|Significado|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`e `from {person}` são opcionais|
|"Você pode me ajudar[?]|A marca de pontuação é opcional|

As marcas de`?`pontuação `!` `.`( , ) devem ser ignoradas e você precisa ignorá-las usando a sintaxe de suporte quadrado em padrões.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Conceitos de padrões](luis-concept-patterns.md)

Entenda como o [sentimento](luis-reference-prebuilt-sentiment.md) é retornado na resposta .json.