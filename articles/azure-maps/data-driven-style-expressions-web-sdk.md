---
title: Expressões de estilo baseadas em dados no Web SDK do Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre como usar expressões de estilo baseadas em dados no Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3f15033095b02dd35c2d8d7bda60ca184df64c9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475012"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo orientadas por dados (Web SDK)

Expressões permitem que você aplique lógica de negócios a opções de estilo que observem as propriedades definidas em cada forma em uma fonte de dados. Expressões podem filtrar dados em uma fonte de dados ou em uma camada. Expressões podem consistir em lógica condicional, como se-declarações. E, eles podem ser usados para manipular dados usando: operadores de cordas, operadores lógicos e operadores matemáticos.

Estilos orientados a dados reduzem a quantidade de código necessária para implementar a lógica de negócios em torno do estilo. Quando usado com camadas, as expressões são avaliadas no tempo de renderização em um segmento separado. Essa funcionalidade oferece maior desempenho em comparação com a avaliação da lógica de negócios no segmento de ia de usuário.

Este vídeo fornece uma visão geral do estilo baseado em dados no Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma string que especifica o nome do operador de expressão. Por exemplo, "+" ou "case". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma seqüência, número, booleano, ou `null`), ou outra matriz de expressão. O pseudocódigo a seguir define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O Web SDK do Azure Maps suporta muitos tipos de expressões. Expressões podem ser usadas por conta própria ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressão agregada](#aggregate-expression) | Uma expressão que define um cálculo que é processado sobre um `clusterProperties` conjunto de `DataSource`dados e pode ser usado com a opção de um . |
| [Expressões boolianas](#boolean-expressions) | Expressões booleanas fornecem um conjunto de expressões booleanas para avaliar comparações booleanas. |
| [Expressões coloridas](#color-expressions) | Expressões coloridas facilitam a criação e a manipulação de valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais fornecem operações lógicas que são como se-declarações. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados da propriedade em um recurso. |
| [Expressões Interpoladas e Passo](#interpolate-and-step-expressions) | As expressões interpoladas e de passo podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolada. |
| [Expressões específicas em camadas](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece operadores matemáticos para executar cálculos baseados em dados dentro da estrutura de expressão. |
| [Expressões do operador de cordas](#string-operator-expressions) | As expressões do operador de strings executam operações de conversão em strings, como concatenar e converter o estojo. |
| [Tipo expressões](#type-expressions) | Expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como strings, números e valores booleanos. |
| [Expressões de ligação variável](#variable-binding-expressions) | Expressões de ligação variável armazenam os resultados de um cálculo em uma variável e referenciados em outro lugar em uma expressão várias vezes sem ter que recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento da renderização. |

Todos os exemplos deste documento usam o seguinte recurso para demonstrar diferentes maneiras pelas quais os diferentes tipos de expressões podem ser usados. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

Expressões de dados fornecem acesso aos dados da propriedade em um recurso. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | objeto | Recupera um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria do recurso: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Obtém o valor da propriedade das propriedades do recurso atual. Retorna nulo se a propriedade solicitada estiver faltando. |
| `['get', string, object]` | value | Obtém o valor da propriedade das propriedades do objeto fornecido. Retorna nulo se a propriedade solicitada estiver faltando. |
| `['has', string]` | booleano | Determina se as propriedades de um recurso têm a propriedade especificada. |
| `['has', string, object]` | booleano | Determina se as propriedades do objeto têm a propriedade especificada. |
| `['id']` | value | Obtém o ID do recurso se ele tiver um. |
| `['length', string | array]` | número | Obtém o comprimento de uma seqüência ou uma matriz. |
| `['in', boolean | string | number, array]` | booleano | Determina se um item existe em uma matriz |
| `['in', substring, string]` | booleano | Determina se uma substring existe em uma string |

**Exemplos**

As propriedades de um recurso podem ser acessadas diretamente em uma expressão usando uma `get` expressão. Este exemplo usa o valor "zoneColor" do recurso para especificar a propriedade de cor de uma camada de bolha. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem, se `zoneColor` todos os recursos de ponto tiverem a propriedade. Se não o fizerem, a cor provavelmente voltará para "preto". Para modificar a cor de `case` recuo, `has` use uma expressão em combinação com a expressão para verificar se a propriedade existe. Se a propriedade não existir, retorne uma cor de recuo.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

As camadas de bolha e símbolo renderizarão as coordenadas de todas as formas em uma fonte de dados, por padrão. Esse comportamento pode destacar os vértices de um polígono ou de uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de `['geometry-type']` geometria dos recursos que renderiza, usando uma expressão dentro de uma expressão booleana. O exemplo a seguir limita uma `Point` camada de bolha para que apenas os recursos sejam renderizados.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo a `Point` `MultiPoint` seguir permite que ambos e recursos sejam renderizados. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, o contorno dos Polígonos renderizará em camadas de linha. Para desativar esse comportamento em uma camada de `LineString` linha, adicione um filtro que só permite e `MultiLineString` apresenta recursos.  

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem operadores matemáticos para realizar cálculos baseados em dados dentro da estrutura de expressão.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números pelo segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados juntos. |
| `['/', number, number]` | número | Divide o primeiro número pelo segundo número. |
| `['%', number, number]` | número | Calcula o restante ao dividir o primeiro número pelo segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula o arccosine do número especificado. |
| `['asin', number]` | número | Calcula o arco do número especificado. |
| `['atan', number]` | número | Calcula o arcotangent do número especificado. |
| `['ceil', number]` | número | Arredonda o número para o próximo inteiro inteiro. |
| `['cos', number]` | número | Calcula o cos do número especificado. |
| `['e']` | número | Retorna a `e`constante matemática. |
| `['floor', number]` | número | Arredonda o número para o inteiro inteiro anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Retorna a `ln(2)`constante matemática. |
| `['log10', number]` | número | Calcula o logaritmo base-dez do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo base dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto de números especificados. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto de números especificados. |
| `['pi']` | número | Retorna a `PI`constante matemática. |
| `['round', number]` | número | Arredonda o número para o inteiro mais próximo. Os valores da metade são arredondados para longe do zero. Por exemplo, `['round', -1.5]` avalia para -2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |

## <a name="aggregate-expression"></a>Expressão agregada

Uma expressão agregada define um cálculo que é processado sobre um `clusterProperties` conjunto `DataSource`de dados e pode ser usado com a opção de um . A saída dessas expressões deve ser um número ou um booleano. 

Uma expressão agregada leva em três valores: um valor do operador, e um valor inicial, e uma expressão para recuperar um imóvel de cada recurso em um dado para aplicar a operação agregada. Esta expressão tem o seguinte formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operador: Uma função de expressão que é então aplicada `mapExpression` contra todos os valores calculados pelo para cada ponto no cluster. Operadores suportados: 
    - Para `+`números: `*` `max`, ,`min`
    - Para booleanos: `all`,`any`
- initialValue: Um valor inicial no qual o primeiro valor calculado é agregado contra.
- mapExpression: Uma expressão aplicada contra cada ponto do conjunto de dados.

**Exemplos**

Se todos os recursos em `revenue` um conjunto de dados tiverem uma propriedade, que é um número. Em seguida, a receita total de todos os pontos em um cluster, que são criados a partir do conjunto de dados, pode ser calculada. Este cálculo é feito usando a seguinte expressão agregada:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Expressões boolianas

Expressões booleanas fornecem um conjunto de expressões booleanas para avaliar comparações booleanas.

Ao comparar valores, a comparação é estritamente digitada. Valores de diferentes tipos são sempre considerados desiguais. Casos em que os tipos são conhecidos por serem diferentes na hora da análise são considerados inválidos e produzirão um erro de análise. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['! ', boolean]` | booleano | Negação lógica. Retorna `true` se a `false`entrada `false` for , `true`e se a entrada for . |
| `['!= ', value, value]` | booleano | Retorna `true` se os valores `false` de entrada não forem iguais, caso contrário. |
| `['<', value, value]` | booleano | Retorna `true` se a primeira entrada for `false` estritamente menor que a segunda, caso contrário. Os argumentos são necessários para serem ambas as cordas ou ambos os números. |
| `['<=', value, value]` | booleano | Retorna `true` se a primeira entrada for menor `false` ou igual à segunda, caso contrário. Os argumentos são necessários para serem ambas as cordas ou ambos os números. |
| `['==', value, value]` | booleano | Retorna `true` se os valores `false` de entrada forem iguais, caso contrário. Os argumentos são necessários para serem ambas as cordas ou ambos os números. |
| `['>', value, value]` | booleano | Retorna `true` se a primeira entrada for `false` estritamente maior que a segunda, caso contrário. Os argumentos são necessários para serem ambas as cordas ou ambos os números. |
| `['>=' value, value]` | booleano | Retorna `true` se a primeira entrada for maior `false` ou igual à segunda, caso contrário. Os argumentos são necessários para serem ambas as cordas ou ambos os números. |
| `['all', boolean, boolean, …]` | booleano | Retorna `true` se todas as `true` `false` entradas forem, caso contrário. |
| `['any', boolean, boolean, …]` | booleano | Retorna `true` se alguma das `true`entradas for, `false` caso contrário. |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais fornecem operações lógicas que são como se-declarações.

As seguintes expressões realizam operações lógicas condicionais nos dados de entrada. Por exemplo, `case` a expressão fornece lógica "se/então/else", enquanto a `match` expressão é como uma "declaração de switch". 

### <a name="case-expression"></a>Expressão do caso

Uma `case` expressão é um tipo de expressão condicional que fornece lógica "se/então/else". Este tipo de expressão passa por uma lista de condições booleanas. Ele retorna o valor de saída da primeira condição booleana para avaliar a verdade.

O pseudocódigo a seguir define `case` a estrutura da expressão. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplo**

O exemplo a seguir passa por diferentes condições `true`booleanas até encontrar uma que avalie e, em seguida, devolva esse valor associado. Se nenhuma condição booleana avaliar , `true`um valor de recuo será devolvido. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expressão de correspondência

Uma `match` expressão é um tipo de expressão condicional que fornece a lógica de switch-statement like. A entrada pode ser `['get', 'entityType']` qualquer expressão como essa retorna uma seqüência ou um número. Cada rótulo deve ser um único valor literal ou uma matriz de valores literais, cujos valores devem ser todas as strings ou todos os números. A entrada corresponde se algum dos valores na matriz corresponder. Cada rótulo deve ser único. Se o tipo de entrada não corresponder ao tipo dos rótulos, o resultado será o valor de recuo.

O pseudocódigo a seguir define `match` a estrutura da expressão. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplos**

O exemplo a `entityType` seguir analisa a propriedade de um recurso Point em uma camada de bolha procura uma correspondência. Se encontrar uma correspondência, esse valor especificado é devolvido ou ele retorna o valor de recuo.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

O exemplo a seguir usa uma matriz para listar um conjunto de rótulos que devem retornar o mesmo valor. Essa abordagem é muito mais eficiente do que listar cada rótulo individualmente. Neste caso, se `entityType` o imóvel for "restaurante" ou "grocery_store", a cor "vermelha" será devolvida.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

O exemplo a seguir usa uma expressão de correspondência para executar um filtro do tipo "in array" ou "array contains". Neste caso, a expressão filtra dados que têm um valor de ID que está em uma lista de IDs permitidos. Ao usar expressões com filtros, o resultado precisa ser um valor booleano.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Expressão coalescea

Uma `coalesce` expressão passa por um conjunto de expressões até que o primeiro valor não nulo seja obtido e devolva esse valor. 

O pseudocódigo a seguir define `coalesce` a estrutura da expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo a `coalesce` seguir usa `textField` uma expressão para definir a opção de uma camada de símbolo. Se `title` a propriedade estiver ausente do `null`recurso ou definida para `subtitle` , a expressão `null`tentará procurar a propriedade, se estiver faltando ou , ela voltará para uma seqüência vazia. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

O exemplo a `coalesce` seguir usa uma expressão para recuperar o primeiro ícone de imagem disponível no sprite do mapa a partir de uma lista de nomes de imagem especificados.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Tipo expressões

Expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como strings, números e valores booleanos.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | objeto \| matriz | Retorna uma matriz literal ou valor do objeto. Use esta expressão para evitar que uma matriz ou objeto seja avaliado como uma expressão. Isso é necessário quando uma matriz ou objeto precisa ser devolvido por uma expressão. |
| `['image', string]` | string | Verifica se uma identificação de imagem especificada é carregada no sprite de imagem dos mapas. Se for, o ID é devolvido, caso contrário nulo é devolvido. |
| `['to-boolean', value]` | booleano | Converte o valor de entrada em um booleano. O resultado `false` é quando a entrada `0` `false`é `null`uma `NaN`seqüência vazia, ou ; caso contrário, sua `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | cor | Converte o valor de entrada em uma cor. Se vários valores forem fornecidos, cada um será avaliado em ordem até que a primeira conversão bem sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada em um número, se possível. Se a `null` entrada `false`for ou , o resultado é 0. Se a `true`entrada for, o resultado é 1. Se a entrada for uma seqüência, ela será convertida em um número usando a função de seqüência de string [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) da Especificação de Idioma ECMAScript. Se vários valores forem fornecidos, cada um será avaliado em ordem até que a primeira conversão bem sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-string', value]` | string | Converte o valor de entrada em uma seqüência. Se a `null`entrada for, `""`o resultado é . Se a entrada for booleana, o resultado é `"true"` ou `"false"`. Se a entrada for um número, ela será convertida em uma seqüência usando a função [número ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) da Especificação de Idioma ECMAScript. Se a entrada for uma cor, ela será convertida em `"rgba(r,g,b,a)"`cadeia de cor CSS RGBA . Caso contrário, a entrada é convertida em uma seqüência usando a função [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) da Especificação de Idioma ECMAScript. |
| `['typeof', value]` | string | Retorna uma seqüência descrevendo o tipo do valor dado. |

> [!TIP]
> Se uma mensagem `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` de erro semelhante a aparecer no console do navegador, então isso significa que há uma expressão em algum lugar do seu código que tem uma matriz que não tem uma string para o seu primeiro valor. Se você quiser que a expressão retorne uma `literal` matriz, enrole a matriz com a expressão. O exemplo a `offset` seguir define a opção de ícone de uma camada de `match` símbolo, que precisa ser uma matriz `entityType` contendo dois números, usando uma expressão para escolher entre dois valores de deslocamento com base no valor da propriedade do recurso de ponto.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressões coloridas

Expressões coloridas facilitam a criação e a manipulação de valores de cor.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | cor | Cria um valor de cor a partir de componentes `255` *vermelhos,* *verdes*e *azuis* que devem variar entre `0` e , e um componente alfa de `1`. Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['rgba', number, number, number, number]` | cor | Cria um valor de cor a partir de componentes *vermelhos,* *verdes,* `0` *azuis* que devem variar entre `0` e `255`, e um componente alfa dentro de uma faixa de e `1`. Se algum componente estiver fora de alcance, a expressão é um erro. |
| `['to-rgba']` | \[número, número, número, número\] | Retorna uma matriz de quatro elementos contendo os componentes *vermelho,* *verde,* *azul*e *alfa* da cor de entrada, nessa ordem. |

**Exemplo**

O exemplo a seguir cria um valor de `255`cor RGB que tem um valor *vermelho* de `temperature` , e valores *verdes* e *azuis* que são calculados multiplicando-se `2.5` pelo valor da propriedade. À medida que a temperatura muda, a cor mudará para diferentes tons de *vermelho.*

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Expressões do operador de cordas

As expressões do operador de strings executam operações de conversão em strings, como concatenar e converter o estojo. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena várias cordas juntas. Cada valor deve ser uma seqüência. Use `to-string` a expressão de tipo para converter outros tipos de valor em string, se necessário. |
| `['downcase', string]` | string | Converte a cadeia de caracteres especificada em minúsculas. |
| `['upcase', string]` | string | Converte a cadeia de caracteres especificada em maiúsculas. |

**Exemplo**

O exemplo a `temperature` seguir converte a propriedade do recurso de ponto em uma string e, em seguida, concatena "°F" até o final dele.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

A expressão acima rende um pino no mapa com o texto "64°F" sobreposto em cima dele, como mostrado na imagem abaixo.

<center>

![Exemplo](media/how-to-expressions/string-operator-expression.png) de expressão do operador de cordas</center>

## <a name="interpolate-and-step-expressions"></a>Expressões Interpoladas e Passo

As expressões interpoladas e de passo podem ser usadas para calcular valores ao longo de uma função de curva ou passo interpolada. Essas expressões tomam uma expressão que retorna um valor numérico como sua entrada, por exemplo. `['get',  'temperature']` O valor de entrada é avaliado em relação a pares de valores de entrada e saída, para determinar o valor que melhor se encaixa na função de curva ou passo interpolada. Os valores de saída são chamados de "stops". Os valores de entrada para cada parada devem ser um número e estar em ordem crescente. Os valores de saída devem ser um número, e matriz de números, ou uma cor.

### <a name="interpolate-expression"></a>Expressão interpolada

Uma `interpolate` expressão pode ser usada para calcular um conjunto contínuo e suave de valores interpolando entre os valores de parada. Uma `interpolate` expressão que retorna valores de cor produz um gradiente de cor no qual os valores de resultado são selecionados.

Existem três tipos de métodos de interpolação que podem ser usados em uma `interpolate` expressão:
 
* `['linear']`- Interpola linearmente entre o par de paradas.
* `['exponential', base]`- Interpola exponencialmente entre as paradas. O `base` valor controla a taxa em que a saída aumenta. Valores mais altos fazem com que a saída aumente mais para a extremidade alta da faixa. Um `base` valor próximo a 1 produz uma saída que aumenta mais linearmente.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpolados utilizando uma [curva bezier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controle dado.

Aqui está um exemplo de como esses diferentes tipos de interpolações se parecem. 

| Linear  | Exponencial | Bézier cúbica |
|---------|-------------|--------------|
| ![Gráfico de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação de Bezier cúbico](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo a seguir define `interpolate` a estrutura da expressão. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exemplo**

O exemplo a `linear interpolate` seguir usa `color` uma expressão para definir `temperature` a propriedade de uma camada de bolha com base na propriedade do recurso de ponto. Se `temperature` o valor for inferior a 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, o amarelo será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvido. Se for 80 ou mais, "vermelho" será devolvido.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão](media/how-to-expressions/interpolate-expression-example.png) interpolada</center>

### <a name="step-expression"></a>Expressão de passo

Uma `step` expressão pode ser usada para calcular valores de resultado discretos e escalonados avaliando uma [função em forma de peça definida](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) por paradas. 

O pseudocódigo a seguir define `step` a estrutura da expressão. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

As expressões de passo retornam o valor de saída da parada pouco antes do valor de entrada ou o primeiro valor de entrada se a entrada for menor que a primeira parada. 

**Exemplo**

O exemplo a `step` seguir usa `color` uma expressão para definir `temperature` a propriedade de uma camada de bolha com base na propriedade do recurso de ponto. Se `temperature` o valor for inferior a 60, "azul" será devolvido. Se estiver entre 60 e menos de 70, "amarelo" será devolvido. Se estiver entre 70 e menos de 80, "laranja" será devolvido. Se for 80 ou mais, "vermelho" será devolvido.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de passo](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões específicas da camada

Expressões especiais que só se aplicam a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade do mapa de calor

Uma expressão de densidade de mapa de calor recupera o valor de densidade `['heatmap-density']`do mapa de calor para cada pixel em uma camada de mapa de calor e é definida como . Este valor é `0` um `1`número entre e . É usado em combinação `interpolation` com `step` uma ou expressão para definir o gradiente de cor usado para colorir o mapa de calor. Esta expressão só pode ser usada na opção de [cor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) da camada do mapa de calor.

> [!TIP]
> A cor no índice 0, em uma expressão de interpolação ou na cor padrão de uma cor de passo, define a cor da área onde não há dados. A cor no índice 0 pode ser usada para definir uma cor de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto.

**Exemplo**

Este exemplo usa uma expressão de interpolação de forro para criar um gradiente de cor suave para renderizar o mapa de calor. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Além de usar um gradiente suave para colorir um mapa de calor, as `step` cores podem ser especificadas dentro de um conjunto de intervalos usando uma expressão. Usando `step` uma expressão para colorir o mapa de calor, a densidade em intervalos que se assemelha a um mapa de contorno ou estilo de radar.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Para obter mais informações, consulte [a documentação de adicionar uma camada de mapa de calor.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Expressão de progresso da linha

Uma expressão de progresso de linha recupera o progresso ao longo `['line-progress']`de uma linha gradiente em uma camada de linha e é definida como . Este valor é um número entre 0 e 1. É usado em combinação `interpolation` com `step` uma ou expressão. Esta expressão só pode ser usada com a [opção strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) da camada de linha. 

> [!NOTE]
> A `strokeGradient` opção da camada `lineMetrics` de linha requer a opção `true`da fonte de dados a ser definida como .

**Exemplo**

Este exemplo `['line-progress']` usa a expressão para aplicar um gradiente de cor ao traçado de uma linha.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Veja o exemplo ao vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão do formato de campo de texto

A expressão de formato de `textField` campo de texto `textOptions` pode ser usada com a opção da propriedade camadas de símbolo para fornecer formatação de texto mista. Esta expressão permite que um conjunto de strings de entrada e opções de formatação sejam especificados. As seguintes opções podem ser especificadas para cada string de entrada nesta expressão.

 * `'font-scale'`- Especifica o fator de dimensionamento para o tamanho da fonte. Se especificado, este valor `size` substituirá `textOptions` a propriedade do para a seqüência individual.
 * `'text-font'`- Especifica uma ou mais famílias de fontes que devem ser usadas para esta string. Se especificado, este valor `font` substituirá `textOptions` a propriedade do para a seqüência individual.
 * `'text-color'`- Especifica uma cor para aplicar a um texto ao renderizar. 

O pseudocódigo a seguir define a estrutura da expressão do formato de campo de texto. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Exemplo**

O exemplo a seguir formata o campo de texto adicionando `title` uma fonte em negrito e aumentando o tamanho da fonte da propriedade do recurso. Este exemplo também `subtitle` adiciona a propriedade do recurso em uma nova linha, com um tamanho de fonte reduzido e vermelho colorido.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Esta camada renderizará o recurso de ponto como mostrado na imagem abaixo:
 
<center>

![Recurso Imagem de Ponto com](media/how-to-expressions/text-field-format-expression.png) campo de texto formatado</center>

### <a name="number-format-expression"></a>Expressão do formato de número

A `number-format` expressão só pode `textField` ser usada com a opção de uma camada de símbolo. Esta expressão converte o número fornecido em uma seqüência formatada. Esta expressão envolve a função [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) do JavaScript e suporta o seguinte conjunto de opções.

 * `locale`- Especifique esta opção para converter números em strings de forma alinhada com o idioma especificado. Passe uma [tag de idioma BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) para esta opção.
 * `currency`- Converter o número em uma seqüência representando uma moeda. Os valores possíveis são os [códigos monetários ISO 4217,](https://en.wikipedia.org/wiki/ISO_4217)como "USD" para o dólar americano, "EUR" para o euro ou "CNY" para o RMB chinês.
 * `'min-fraction-digits'`- Especifica o número mínimo de casas decimais a serem incluemos na versão string do número.
 * `'max-fraction-digits'`- Especifica o número máximo de casas decimais a serem incluemos na versão string do número.

O pseudocódigo a seguir define a estrutura da expressão do formato de campo de texto. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exemplo**

O exemplo a `number-format` seguir usa `revenue` uma expressão para modificar a `textField` forma como a propriedade do recurso de ponto é renderizada na opção de uma camada de símbolo de tal forma que ele aparece como um valor em dólar americano.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Esta camada renderizará o recurso de ponto como mostrado na imagem abaixo:

<center>

![Exemplo de](media/how-to-expressions/number-format-expression.png) expressão de formato de número</center>

### <a name="image-expression"></a>Expressão da imagem

Uma expressão de imagem `image` pode `textField` ser usada com as `fillPattern` opções de uma camada de símbolo e a opção da camada de polígono. Esta expressão verifica se a imagem solicitada existe no estilo e `null`retornará o nome da imagem resolvido ou, dependendo se a imagem está ou não no estilo. Esse processo de validação é síncrono e requer que a imagem tenha sido adicionada ao estilo antes de solicitá-la no argumento da imagem.

**Exemplo**

O exemplo a `image` seguir usa uma expressão para adicionar um ícone em linha com texto em uma camada de símbolo. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Esta camada renderizará o campo de texto na camada símbolo, conforme mostrado na imagem abaixo:

<center>

![Exemplo de](media/how-to-expressions/image-expression.png) expressão de imagem</center>

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom `['zoom']`atual do mapa no momento da renderização e é definida como . Esta expressão retorna um número entre o intervalo de nível de zoom mínimo e máximo do mapa. Os controles interativos de mapa do Azure Maps para web e Android suportam 25 níveis de zoom, numerados de 0 a 24. O `zoom` uso da expressão permite que os estilos sejam modificados dinamicamente à medida que o nível de zoom do mapa é alterado. A `zoom` expressão só pode `interpolate` `step` ser usada com e expressões.

**Exemplo**

Por padrão, os raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados se agregam juntos e a camada do mapa de calor parece diferente. Uma `zoom` expressão pode ser usada para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro de pixels vertical e horizontalmente do que o nível de zoom anterior. Escalar o raio, de tal forma que dobra a cada nível de zoom, criará um mapa de calor que parece consistente em todos os níveis de zoom. Pode ser realizado `zoom` usando a `base 2 exponential interpolation` expressão com uma expressão como mostrado abaixo. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Veja o exemplo ao vivo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de ligação variável

Expressões de ligação variável armazenam os resultados de um cálculo em uma variável. Assim, que os resultados do cálculo podem ser referenciados em outro lugar em uma expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expression | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'vamos',<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor1: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;valor2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Armazena um ou mais valores como `var` variáveis para uso pela expressão na expressão da criança que retorna o resultado. |
| `['var', name: string]` | any | Faz referência a uma variável `let` criada usando a expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita `case` em relação à razão de temperatura e, em seguida, usa uma expressão para avaliar diferentes operações booleanas sobre este valor. A `let` expressão é usada para armazenar a receita em relação à razão de temperatura, de modo que ela só precisa ser calculada uma vez. A `var` expressão faz referência a essa variável quantas vezes for necessário sem ter que recalculá-la.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter mais amostras de código que implementem expressões:

> [!div class="nextstepaction"] 
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicione uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que suportam expressões:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções de camadas de linha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Opções de camadas de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
