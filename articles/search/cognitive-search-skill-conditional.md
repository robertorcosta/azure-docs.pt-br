---
title: Habilidade cognitiva condicional
titleSuffix: Azure Cognitive Search
description: A habilidade condicional no Azure Cognitive Search permite filtrar, criar padrões e mesclar valores em uma definição de skillset.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792041"
---
# <a name="conditional-cognitive-skill"></a>Habilidade cognitiva condicional

A habilidade **condicional** permite cenários de Pesquisa Cognitiva Do Azure que requerem uma operação booleana para determinar os dados a serem atribuídos a uma saída. Esses cenários incluem filtragem, atribuição de um valor padrão e fusão de dados com base em uma condição.

O seguinte pseudocódigo demonstra o que a habilidade condicional realiza:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Essa habilidade não está vinculada a uma API de Serviços Cognitivos do Azure, e você não é cobrado por usá-la. No entanto, você ainda deve [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso "Livre" que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Essa habilidade é especial porque seus insumos são campos avaliados.

Os seguintes itens são valores válidos de uma expressão:

-   Caminhos de anotação (caminhos nas expressões devem ser delimitados por "$() e ")")
 <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literais (strings, números, verdadeiros, falsos, nulos) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressões que usam operadores de comparação (==, !=, >=, >, <=, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressões que usam operadores booleanos (&&, ||, !, ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressões que usam operadores numéricos \*(+, -, , /, %) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Como a habilidade condicional suporta a avaliação, você pode usá-la em cenários de transformação menor. Por exemplo, veja [a definição de habilidade 4](#transformation-example).

## <a name="skill-inputs"></a>Entradas de habilidades
As entradas diferenciam maiúsculas de minúsculas.

| Entrada   | Descrição |
|-------------|-------------|
| condition   | Essa entrada é um [campo avaliado](#evaluated-fields) que representa a condição de avaliação. Esta condição deve avaliar a um valor booleano *(verdadeiro* ou *falso).*   <br/>  Exemplos: <br/> "= verdadeiro" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a retornar se a condição for avaliada como *verdadeira*. As seqüências de constantes devem ser devolvidas entre aspas simples (' e '). <br/>Valores amostrais: <br/> "= 'contrato'"<br/>"= $(/document/contractType)" <br/> "= $/document/entities/\*)" <br/> |
| whenFalse   | Esta entrada é um [campo avaliado](#evaluated-fields) que representa o valor a ser devolvido se a condição for avaliada como *falsa*. <br/>Valores amostrais: <br/> "= 'contrato'"<br/>"= $(/document/contractType)" <br/> "= $/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída que é simplesmente chamada de "saída". Ele retorna o valor *quandoFalsa* se a condição for falsa ou *quandoTrue* se a condição for verdadeira.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Definição de habilidade de amostra 1: Filtrar documentos para devolver apenas documentos franceses

A saída a seguir retorna uma matriz de frases ("/document/frenchSentences") se o idioma do documento for francês. Se a língua não for francesa, o valor é definido como *nulo*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Se "/document/frenchSentences" for usado como *o contexto* de outra habilidade, essa habilidade só será executada se "/document/frenchSentences" não for definida *como nula*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Definição de habilidade de amostra 2: Defina um valor padrão para um valor que não existe

A saída a seguir cria uma anotação ("/document/languageWithDefault") definida para o idioma do documento ou para "es" se o idioma não estiver definido.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Definição de habilidade de amostra 3: Mesclar valores de dois campos em um

Neste exemplo, algumas frases têm uma propriedade *frenchSentiment.* Sempre que a propriedade *frenchSentiment* for nula, queremos usar o valor *do englishSentiment.* Atribuímos a saída a um membro chamado *sentimento* ("/documento/sentimento/*/sentimento").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Exemplo de transformação
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Definição de habilidade de amostra 4: Transformação de dados em um único campo

Neste exemplo, recebemos um *sentimento* que é entre 0 e 1. Queremos transformá-lo para ficar entre -1 e 1. Podemos usar a habilidade condicional para fazer essa pequena transformação.

Neste exemplo, não usamos o aspecto condicional da habilidade porque a condição é sempre *verdadeira.*

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Considerações especiais
Alguns parâmetros são avaliados, então você precisa ser especialmente cuidadoso para seguir o padrão documentado. As expressões devem começar com um sinal de iguais. Um caminho deve ser delimitado por "$( e ")". Certifique-se de colocar as cordas entre aspas. Isso ajuda o avaliador a distinguir entre cordas e caminhos reais e operadores. Além disso, certifique-se de colocar espaço em branco em torno dos operadores (por exemplo, um "*" em um caminho significa algo diferente do que multiplicar).


## <a name="next-steps"></a>Próximas etapas

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
