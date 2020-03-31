---
title: Aplicar operação matemática
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Aplicar Operação matemática no Azure Machine Learning para aplicar uma operação matemática aos valores da coluna em um pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456736"
---
# <a name="apply-math-operation"></a>Aplicar operação matemática

Este artigo descreve um módulo do azure Machine Learning designer (visualização).

Use a Operação Aplicar Matemática para criar cálculos aplicados a colunas numéricas no conjunto de dados de entrada. 

As operações matemáticas suportadas incluem funções aritméticas comuns, como multiplicação e divisão, funções trigonométricas, uma variedade de funções de arredondamento e funções especiais usadas na ciência de dados, como funções gama e erro.  

Depois de definir uma operação e executar o pipeline, os valores são adicionados ao seu conjunto de dados. Dependendo de como você configura o módulo, você pode:

+ Anexar os resultados ao seu conjunto de dados. Isso é particularmente útil quando você está verificando o resultado da operação.
+ Substitua os valores das colunas pelos novos valores computados.
+ Gerar uma nova coluna para resultados, e não mostrar os dados originais. 

Procure a operação que você precisa nestas categorias:  

- [Basic](#basic-math-operations)  
  
     As funções na categoria **Basic** podem ser usadas para manipular um único valor ou coluna de valores. Por exemplo, você pode obter o valor absoluto de todos os números em uma coluna, ou calcular a raiz quadrada de cada valor em uma coluna.  
  
-   [Comparar](#comparison-operations)  
  
      As funções na categoria **Comparar** são todas usadas para comparação: você pode fazer uma comparação par-wise dos valores em duas colunas, ou você pode comparar cada valor em uma coluna com uma constante especificada. Por exemplo, você pode comparar colunas para determinar se os valores eram os mesmos em dois conjuntos de dados. Ou, você pode usar uma constante, como um valor máximo permitido, para encontrar outliers em uma coluna numérica.  
  
-   [Operações](#arithmetic-operations)  
  
     A categoria **Operações** inclui as funções matemáticas básicas: adição, subtração, multiplicação e divisão. Você pode trabalhar com colunas ou constantes. Por exemplo, você pode adicionar o valor na Coluna A ao valor na Coluna B. Ou, você pode subtrair uma constante, como uma média calculada anteriormente, de cada valor na Coluna A.  
  
-   [Arredondamento](#rounding-operations)  
  
     A categoria **Arredondamento** inclui uma variedade de funções para a realização de operações como arredondamento, teto, piso e truncação para vários níveis de precisão. Você pode especificar o nível de precisão para números decimais e inteiros.  
  
-   [Especial](#special-math-functions)  
  
     A categoria **Especial** inclui funções matemáticas que são especialmente utilizadas na ciência de dados, como integrais elípticas e a função de erro gaussiano.  
  
-   [Trigonométricas](#trigonometric-functions)  
  
     A categoria **Trigonométrica** inclui todas as funções trigonométricas padrão. Por exemplo, você pode converter radianos em graus, ou funções computacionais, como a tangente em radianos ou graus.
     Essas funções são unárias, o que significa que eles tomam uma única coluna de valores como entrada, aplicam a função trigonométrica e retornam uma coluna de valores como resultado.  Portanto, você precisa ter certeza de que a coluna de entrada é o tipo apropriado e contém o tipo certo de valores para a operação especificada.   

## <a name="how-to-configure-apply-math-operation"></a>Como configurar aplicar operação matemática  

O módulo **Aplicar operação matemática** requer um conjunto de dados que contenha pelo menos uma coluna contendo apenas números. Os números podem ser discretos ou contínuos, mas devem ser de um tipo de dados numérico, não de uma seqüência.

Você pode aplicar a mesma operação a várias colunas numéricas, mas todas as colunas devem estar no mesmo conjunto de dados. 

Cada instância deste módulo pode realizar apenas um tipo de operação por vez. Para realizar operações matemáticas complexas, talvez seja necessário acorrentar várias instâncias do módulo **Aplicar operação matemática.**  
  
1.  Adicione o módulo **Aplicar operação matemática** ao seu pipeline.

1. Conecte um conjunto de dados que contenha pelo menos uma coluna numérica.  

1.  Selecione uma ou mais colunas de origem nas quais realizar o cálculo.   
  
    - Qualquer coluna que você escolher deve ser um tipo de dados numérico. 
    - A gama de dados deve ser válida para a operação matemática selecionada. Caso contrário, poderá ocorrer um erro ou o resultado NaN (não um número). Por exemplo, Ln(-1.0) é uma operação inválida `NaN`e resulta em um valor de .
  
1.  Clique **em Categoria** para selecionar o **tipo** de operação matemática a ser realizado.
    
1. Escolha uma operação específica da lista nessa categoria.
  
1.  Defina parâmetros adicionais exigidos por cada tipo de operação.  
  
1.  Use a opção **Modo Saída** para indicar como deseja que a operação matemática seja gerada: 

    - **Append**. Todas as colunas usadas como entradas estão incluídas no conjunto de dados de saída, mais uma coluna adicional é anexada que contém os resultados da operação matemática.
    - **Inplace**. Os valores nas colunas utilizadas como insumos são substituídos pelos novos valores calculados. 
    - **Somente resultados**. Uma única coluna é devolvida contendo os resultados da operação matemática.
  
1.  Envie o oleoduto.  
  
## <a name="results"></a>Resultados

Se você gerar os resultados usando as opções **Apêndice** ou **ResultOnly,** os títulos das colunas do conjunto de dados retornado indicam a operação e as colunas que foram usadas. Por exemplo, se você comparar duas colunas usando o operador **Equals,** os resultados ficariam assim:  
  
-   **Equals(Col2_Col1)**, indicando que você testou Col2 contra Col1.  
-   **Equals (Col2_$10)**, indicando que você comparou a coluna 2 com a constante 10.  

Mesmo se você usar a opção **Inplace,** os dados de origem não serão excluídos ou alterados; a coluna no conjunto de dados original ainda está disponível no designer. Para visualizar os dados originais, você pode conectar o módulo [Adicionar colunas](add-columns.md) e junte-os à saída de **Aplicar Operação Matemática**.  
    
## <a name="basic-math-operations"></a>Operações matemáticas básicas 

As funções na categoria **Básico** geralmente pegam um único valor de uma coluna, executam a operação predefinida e retornam um único valor. Para algumas funções, você pode especificar uma constante ou uma coluna definida como um segundo argumento.  
  
 O Azure Machine Learning suporta as seguintes funções na categoria **Básico:**  

### <a name="abs"></a>Abs

Retorna o valor absoluto das colunas selecionadas.  
  
### <a name="atan2"></a>Atan2

Retorna uma tangente inversa de quatro quadrantes.  

Selecione as colunas que contêm as coordenadas de ponto. Para o segundo argumento, que corresponde à coordenada x, você também pode especificar uma constante.  

Corresponde à função ATAN2 no Matlab.  

### <a name="conj"></a>Conj

Retorna o conjugado para os valores na coluna selecionada.  

### <a name="cuberoot"></a>CubeRoot

Calcula a raiz do cubo para os valores na coluna selecionada.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Calcula o fatorial duplo para valores na coluna selecionada. O fatorial duplo é uma extensão da função fatorial normal, e é denotado como x!!.  

### <a name="eps"></a>Eps

Retorna o tamanho do espaço entre o valor atual e o próximo número mais alto de precisão dupla. Corresponde à função EPS no Matlab.  
  
### <a name="exp"></a>Exp

Retorna e elevado ao poder do valor na coluna selecionada. Isso é o mesmo que a função Excel EXP.  

### <a name="exp2"></a>Exp2

Retorna a base 2 exponencial dos argumentos, resolvendo para y = x * 2<sup>t</sup> onde t é uma coluna de valores contendo expoentes.  

Em **'Coluna definida'** selecione a coluna que contém os valores expoentes t.

Para **Exp2** você pode especificar um segundo argumento x, que pode ser uma constante ou outra coluna de valores. No **segundo tipo de argumento,** indique se você fornecerá o multiplicador x como uma constante ou um valor em uma coluna.  

Por exemplo, se você selecionar {0,1,2,3,4,5} uma coluna com os valores tanto para o multiplicador quanto para o expoente, a função retorna {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Retorna o expoente negativo para valores na coluna selecionada.  

### <a name="factorial"></a>Fatorial
Retorna o fator para valores na coluna selecionada.  

### <a name="hypotenuse"></a>Hipotenusa
Calcula a hipotenusa para um triângulo no qual o comprimento de um lado é especificado como uma coluna de valores, e o comprimento do segundo lado é especificado como uma constante ou como duas colunas.  

### <a name="ln"></a>Ln

Retorna o logaritmo natural para os valores na coluna selecionada.  

### <a name="lnplus1"></a>LnPlus1

Retorna o logaritmo natural mais um para os valores na coluna selecionada.  

### <a name="log"></a>Log

Retorna o log dos valores na coluna selecionada, dada a base especificada.  

Você pode especificar a base (o segundo argumento) como uma constante ou selecionando outra coluna de valores.  

### <a name="log10"></a>Log10

Retorna os valores base de 10 logaritmos para a coluna selecionada.  

### <a name="log2"></a>Log2

Retorna os valores de logaritmo base 2 para a coluna selecionada.  

### <a name="nthroot"></a>NthRoot
Retorna a enésima raiz do valor, usando um n que você especifica.  

Selecione as colunas para as quais deseja calcular a raiz, usando a opção **ColunaSet.**  

No **segundo tipo de argumento,** selecione outra coluna que contenha a raiz ou especifique uma constante para usar como raiz.  

Se o segundo argumento for uma coluna, cada valor na coluna é usado como o valor de n para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para n na caixa de texto **segundo argumento.**
### <a name="pow"></a>Pow

Calcula X elevado ao poder de Y para cada um dos valores na coluna selecionada.  

Primeiro, selecione as colunas que contêm a **base**, que deve ser um flutuador, usando a opção **ColumnSet.**  

No **segundo tipo de argumento,** selecione a coluna que contém o expoente ou especifique uma constante para usar como expoente.  

Se o segundo argumento é uma coluna, cada valor na coluna é usado como o exponente para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para o expoente na caixa de texto **segundo argumento.**  

### <a name="sqrt"></a>Sqrt

Retorna a raiz quadrada dos valores na coluna selecionada.  

### <a name="sqrtpi"></a>SqrtPi

Para cada valor na coluna selecionada, multiplica o valor por pi e, em seguida, retorna a raiz quadrada do resultado.  

### <a name="square"></a>Square

Quadrados os valores na coluna selecionada.  

## <a name="comparison-operations"></a>Operadores de comparação  

Use as funções de comparação no designer azure Machine Learning a qualquer momento que você precisa testar dois conjuntos de valores uns contra os outros. Por exemplo, em um pipeline você pode precisar fazer essas operações de comparação:  

- Avalie um modelo de pontuação de probabilidade em relação a um valor limiar.
- Determine se dois conjuntos de resultados são os mesmos. Para cada linha que seja diferente, adicione um sinalizador FALSE que pode ser usado para posterior processamento ou filtragem.  

### <a name="equalto"></a>EqualTo

Retorna Verdadeiro se os valores forem os mesmos.  

### <a name="greaterthan"></a>GreaterThan

Retorna True se os valores no **conjunto Coluna forem** maiores do que a constante especificada, ou maiores do que os valores correspondentes na coluna de comparação.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Retorna Satisfazem se os valores no **conjunto Coluna forem** maiores ou iguais à constante especificada, ou maiores ou iguais aos valores correspondentes na coluna de comparação.  

### <a name="lessthan"></a>LessThan

Retorna True se os valores no **conjunto Coluna forem** inferiores à constante especificada, ou menor do que os valores correspondentes na coluna de comparação.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Retorna True se os valores no **conjunto Coluna forem** menores ou iguais à constante especificada, ou inferior ou igual aos valores correspondentes na coluna de comparação.  

### <a name="notequalto"></a>NotEqualTo

Retorna True se os valores em **Coluna definida** não forem iguais à constante ou coluna de comparação e retorna False se forem iguais.  

### <a name="pairmax"></a>PairMax

Retorna o valor maior; o valor na **Coluna definida**ou o valor na coluna constante ou comparação.  

### <a name="pairmin"></a>PairMin

Retorna o valor menor — o valor no **conjunto Coluna** ou o valor na coluna constante ou de comparação  
  
##  <a name="arithmetic-operations"></a>Arithmetic operations   

Inclui as operações aritméticas básicas: adição e subtração, divisão e multiplicação.  Como a maioria das operações é binária, exigindo dois números, primeiro você escolhe a operação e, em seguida, escolhe a coluna ou números para usar no primeiro e segundo argumentos.

A ordem em que você escolhe as colunas para divisão e subtração pode parecer contra-intuitiva; no entanto, para facilitar a compreensão dos resultados, o título da coluna fornece o nome da operação e a ordem em que as colunas foram usadas.

Operação|Núm1|Núm2|Coluna de resultados|Valor do resultado|
----|----|----|----|----
|Adição|1|5|Adicionar (Num2_Num1)| 4|
|Multiplicação|1|5|Múltiplo(Num2_Num1)|5|
|Subtração|1|5|Subtrair(Num2_Num1)|4|
|Subtração|0|1|Subtrair(Num2_Num1)|0|
|Divisão|1|5|Dividir(Num2_Num1)|5|
|Divisão|0|1|Dividir(Num2_Num1)|Infinito|

### <a name="add"></a>Adicionar

Especifique as colunas de origem usando **'Conjunto de colunas'** e adicione a esses valores um número especificado no **segundo argumento**.  

Para adicionar os valores em duas colunas, escolha uma coluna ou colunas usando **conjunto de colunas**e escolha uma segunda coluna usando **Segundo argumento**.  

### <a name="divide"></a>Dividir

Divide os valores em **Coluna definidos** por uma constante ou pelos valores de coluna definidos no **Segundo argumento**.  Em outras palavras, você escolhe o divisor primeiro, e depois o dividendo. O valor de saída é o quociente.

### <a name="multiply"></a>Multiplicar

Multiplica os valores na **Coluna definida** pelos valores de constante ou coluna especificados.  

### <a name="subtract"></a>Subtrair

Especifique a coluna de valores a serem operadas (o *minuend),* escolhendo uma coluna diferente, usando a opção **Definir** colunas. Em seguida, especifique o número a subtrair (o *subtrahend*) usando a lista de sosselado **do segundo argumento.** Você pode escolher uma constante ou uma coluna de valores.

##  <a name="rounding-operations"></a>Operações de arredondamento 

O designer de Machine Learning do Azure suporta uma variedade de operações de arredondamento. Para muitas operações, você deve especificar a quantidade de precisão a ser usada ao arredondar. Você pode usar um nível de precisão estática, especificado como uma constante, ou você pode aplicar um valor de precisão dinâmico obtido a partir de uma coluna de valores.  

- Se você usar uma constante, defina **O Tipo de Precisão** como **Constante** e digite o número de dígitos como um inteiro na caixa de texto **Constant Precision.** Se você digitar um não-inteiro, o módulo não levanta um erro, mas os resultados podem ser inesperados.  

- Para usar um valor de precisão diferente para cada linha do conjunto de dados, defina **O Tipo de Precisão** como Conjunto **de**Colunas e escolha a coluna que contém valores de precisão apropriados.  

### <a name="ceiling"></a>Ceiling

Retorna o teto para os valores na **Coluna definida**.  

### <a name="ceilingpower2"></a>CeilingPower2

Retorna o teto quadrado para os valores na **Coluna definida**.  

### <a name="floor"></a>Piso

Retorna a base para os valores na **Coluna definida**, até a precisão especificada.  

### <a name="mod"></a>Mod

Retorna a parte fracionária dos valores na **Coluna definida**, até a precisão especificada.  

### <a name="quotient"></a>Quociente

Retorna a parte fracionária dos valores na **Coluna definida**, até a precisão especificada.  

### <a name="remainder"></a>Restante

Retorna o restante para os valores na **Coluna definida**.  

### <a name="rounddigits"></a>RoundDigits

Retorna os valores na **Coluna definida**, arredondado pela regra de 4/5 para o número de dígitos especificado.  

### <a name="rounddown"></a>RoundDown

Retorna os valores na **Coluna definida**, arredondado para baixo até o número especificado de dígitos.  

### <a name="roundup"></a>RoundUp

Retorna os valores na **Coluna definida**, arredondado para cima até o número especificado de dígitos.  

### <a name="toeven"></a>ToEven

Retorna os valores na **Coluna definida**, arredondados para o próximo número inteiro par.  

### <a name="toodd"></a>ToOdd

Retorna os valores na **Coluna definida**, arredondados para o próximo número inteiro ímpar.  

### <a name="truncate"></a>Truncate

Trunca os valores na **Coluna definida** removendo todos os dígitos não permitidos pela precisão especificada.  
  
## <a name="special-math-functions"></a>Funções matemáticas especiais

Esta categoria inclui funções matemáticas especializadas frequentemente usadas na ciência de dados. Salvo observação em contrário, a função é unária e retorna o cálculo especificado para cada valor na coluna ou colunas selecionadas.  

### <a name="beta"></a>Beta

Retorna o valor da função beta de Euler.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Devolve o valor da integral elíptica incompleta.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Retorna o valor da integral elíptica completa (K).  

### <a name="erf"></a>Erf

Retorna o valor da função de erro.  

A função de erro (também chamada de função de erro Gauss) é uma função especial de forma sigmoide que é usada na probabilidade para descrever a difusão.  

### <a name="erfc"></a>Erfc

Retorna o valor da função de erro complementar.  

Erfc é definido como 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled

Retorna o valor da função de erro dimensionada.  

Uma versão dimensionada da função de erro pode ser usada para evitar estouro aritmético.  

### <a name="erfinverse"></a>ErfInverse

Retorna o valor da função erf inversa.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Devolve o valor do Ei integral exponencial.  

### <a name="gamma"></a>Gama

Retorna o valor da função gama.  

### <a name="gammaln"></a>GammaLn

Retorna o logaritmo natural da função gama.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Retorna o valor da função gama incompleta regularizada.  

Esta função tem um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Retorna o valor da função gama incompleta regularizada inversa.  

Esta função tem um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Retorna o valor da função gama incompleta regularizada.  

Esta função tem um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Retorna o valor da função gama incompleta regularizada generalizada inversa.

Esta função tem um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="polygamma"></a>PolyGamma

Retorna o valor da função poligama.  

Esta função tem um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.

##  <a name="trigonometric-functions"></a>Funções trigonométricas 

Esta categoria inclui a maioria das funções trigonométricas trigonométricas importantes e inversas. Todas as funções trigonométricas são unárias e não requerem nenhum argumento adicional.  

### <a name="acos"></a>Acos

Calcula o arcocosino para os valores da coluna.  

### <a name="acosdegree"></a>AcosDegree

Calcula o arccosine dos valores da coluna, em graus.  

### <a name="acosh"></a>Acosh

Calcula o arcocosino hiperbólico dos valores da coluna.  

### <a name="acot"></a>Acot

Calcula o arccotangent dos valores da coluna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcula o arccotangent dos valores da coluna, em graus.  

### <a name="acoth"></a>Acoth

Calcula o arquivamento hiperbólico dos valores da coluna.  

### <a name="acsc"></a>Acsc

Calcula o arccosecante dos valores da coluna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcula o arccosecante dos valores da coluna, em graus.  
### <a name="asec"></a>Asec

Calcula o arcsecante dos valores da coluna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcula o arcsecante dos valores da coluna, em graus.  

### <a name="asech"></a>Asech

Calcula o arcosecante hiperbólico dos valores da coluna.  

### <a name="asin"></a>Asin

Calcula o arco dos valores da coluna.  

### <a name="asindegrees"></a>AsinDegrees

Calcula o arco dos valores da coluna, em graus.  

### <a name="asinh"></a>Asinh

Calcula o arcosina hiperbólica para os valores da coluna.  

### <a name="atan"></a>Atan

Calcula o arcotangente dos valores da coluna.  

### <a name="atandegrees"></a>AtanDegrees

Calcula o arcotangente dos valores da coluna, em graus.  

### <a name="atanh"></a>Atanh

Calcula o arcotangent hiperbólico dos valores da coluna.  

### <a name="cos"></a>Cos

Calcula o cosseno dos valores da coluna.  

### <a name="cosdegrees"></a>CosDegrees

Calcula o cosseno para os valores da coluna, em graus.  

### <a name="cosh"></a>Cosh

Calcula o cosseno hiperbólico para os valores da coluna.  

### <a name="cot"></a>Cot

Calcula o cotangent para os valores da coluna.  

### <a name="cotdegrees"></a>CotDegrees

Calcula o cotangente para os valores da coluna, em graus.  

### <a name="coth"></a>Coth
Calcula o cotangente hiperbólico para os valores da coluna.  

### <a name="csc"></a>Csc

Calcula o cosecante para os valores da coluna.  

### <a name="cscdegrees"></a>CscDegrees

Calcula o cosecante para os valores da coluna, em graus.  

### <a name="csch"></a>Csch

Calcula o cosecante hiperbólico para os valores da coluna.  

### <a name="degreestoradians"></a>DegreesToRadians

Converte graus em radianos.  

### <a name="sec"></a>Sec

Calcula o secante dos valores da coluna.  

### <a name="asecdegrees"></a>aSecDegrees

Calcula o secante para os valores da coluna, em graus.  

### <a name="asech"></a>aSech

Calcula o secante hiperbólico dos valores da coluna.  

### <a name="sign"></a>Assinar

Retorna o sinal dos valores da coluna.  

### <a name="sin"></a>Sin

Calcula o seno dos valores da coluna.  

### <a name="sinc"></a>Sinc

Calcula o valor do seno-cosseno dos valores da coluna.  

### <a name="sindegrees"></a>SinDegrees

Calcula o seno para os valores da coluna, em graus.  

### <a name="sinh"></a>Sinh

Calcula o seno hiperbólico dos valores da coluna.  

### <a name="tan"></a>Tan

Calcula a tangente dos valores da coluna.  

### <a name="tandegrees"></a>TanDegrees

Calcula a tangente para o argumento, em graus.  

### <a name="tanh"></a>Tanh

Calcula a tangente hiperbólica dos valores da coluna.  
  
## <a name="technical-notes"></a>Observações técnicas

Tenha cuidado ao selecionar mais de uma coluna como segundo operador. Os resultados são fáceis de entender se a operação é simples, como adicionar uma constante a todas as colunas. 

Suponha que seu conjunto de dados tenha várias colunas e adicione o conjunto de dados a si mesmo. Nos resultados, cada coluna é adicionada a si mesma, da seguinte forma:  
  
|Núm1|Núm2|Num3|Adicionar (Num1_Num1)|Adicionar (Num2_Num2)|Adicionar (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se você precisar realizar cálculos mais complexos, você pode acorrentar várias instâncias de **Aplicar operação matemática**. Por exemplo, você pode adicionar duas colunas usando uma instância de **Aplicar Operação Matemática** e usar outra instância de **Aplicar Operação Matemática** para dividir a soma por uma constante para obter a média.  
  
Alternativamente, use um dos seguintes módulos para fazer todos os cálculos de uma só vez, usando script SQL, R ou Python:
 
+ [Executar script R](execute-r-script.md)
+ [Executar Script do Python](execute-python-script.md)
+ [Aplicar transformação de SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
