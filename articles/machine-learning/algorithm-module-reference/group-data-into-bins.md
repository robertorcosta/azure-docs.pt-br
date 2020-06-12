---
title: Agrupar dados em compartimentos
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Agrupar dados em compartimentos para agrupar números ou alterar a distribuição de dados contínuos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: b33aa3d2ab22fc986100d6141dd03d5547ef1862
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853728"
---
# <a name="group-data-into-bins"></a>Agrupar dados em compartimentos

Este artigo descreve como usar o módulo [Agrupar dados em compartimentos](group-data-into-bins.md) no designer do Azure Machine Learning (versão prévia), para agrupar números ou alterar a distribuição de dados contínuos.

O módulo [Agrupar dados em compartimentos](group-data-into-bins.md) dá suporte a várias opções para dados compartimentalização. Você pode personalizar como as bordas de compartimento são definidas e como os valores são particionados nos compartimentos. Por exemplo, você pode:  

+ Digite manualmente uma série de valores para servir como limites de compartimento.  
+ Atribua valores aos compartimentos usando *quantis* ou classificações de percentil.  
+ Force uma distribuição uniforme de valores nos compartimentos.  

### <a name="more-about-binning-and-grouping"></a>Mais sobre compartimentalização e agrupamento

*Compartimentalização* ou agrupamento de dados (às vezes chamados de *quantização*) é uma ferramenta importante para preparar dados numéricos para aprendizado de máquina e é útil em cenários como estes:

+ Uma coluna de números contínuos tem muitos valores exclusivos para modelar com eficiência, de modo que você atribui automática ou manualmente os valores a grupos, para criar um conjunto menor de intervalos discretos.

+ Substitua uma coluna de números por valores categóricos que representem intervalos específicos.

    Por exemplo, você pode querer agrupar valores em uma coluna de idade especificando intervalos personalizados como 1 a 15, 16 a 22, 23 a 30 e assim por diante para os dados demográficos do usuário.

+ Um conjunto de dados tem alguns valores extremos, tudo bem fora do intervalo esperado, e esses valores têm uma influência muito grande no modelo treinado. Para reduzir a tendência no modelo, você pode transformar os dados em uma distribuição uniforme, usando o método quantis.

    Com esse método, o módulo [Agrupar dados em compartimentos](group-data-into-bins.md) determina os locais de compartimentos e as larguras de compartimento ideais para garantir que aproximadamente o mesmo número de amostras se enquadrem em cada compartimento. Em seguida, dependendo do método de normalização escolhido, os valores nos compartimentos são transformados em percentis ou mapeados para um número de compartimento.

### <a name="examples-of-binning"></a>Exemplos de compartimentalização

O diagrama a seguir mostra a distribuição de valores numéricos antes e depois da compartimentalização com o método **quantis**. Observe que, em comparação com os dados brutos à esquerda, os dados foram compartimentalizados e transformados em uma escala normal de unidade.  

'Um exemplo pode ser encontrado a partir do resultado desta execução de pipeline: https://ml.azure.com/visualinterface/authoring/Normal/87270db9-4651-448e-bd28-8ef7428084dc?wsid=%2Fsubscriptions%2Fe9b2ec51-5c94-4fa8-809a-dc1e695e4896%2Fresourcegroups%2Fmodule-ws-rg%2Fworkspaces%2Fmodule-prerelease-119&flight=cm&tid=72f988bf-86f1-41af-91ab-2d7cd011db47&smtendpoint=https%3A%2F%2Fsmt-test1.azureml-test.net '

Como há muitas maneiras de agrupar dados, tudo personalizável, recomendamos que você teste com diferentes métodos e valores. 

## <a name="how-to-configure-group-data-into-bins"></a>Como configurar Agrupar dados em compartimentos

1. Adicione o módulo **Agrupar dados em compartimentos** ao seu pipeline no designer (versão prévia). Você pode encontrar esse módulo na categoria **Transformação de Dados**.

2. Conecte o conjunto de dados que tem os valores numéricos ao compartimento.  A quantificação pode ser aplicada somente a colunas que contêm dados numéricos. 

    Se o conjunto de dados contiver colunas não numéricas, use o módulo [Selecionar Colunas no Conjunto de Dados](select-columns-in-dataset.md) para selecionar um subconjunto de colunas com o qual trabalhar.

3. Especifique o modo de compartimentalização. O modo de compartimentalização determina outros parâmetros; portanto, selecione a opção **Modo de compartimentalização** primeiro. Há suporte para os seguintes tipos de compartimentalização:

    **Quantis**: O método quantile atribui valores aos compartimentos com base nas classificações percentuais. Quantis também é conhecido como compartimentalização de mesma altura.

    **Mesma largura**: Com essa opção, você deve especificar o número total de compartimentos. Os valores da coluna de dados são colocados em compartimentos, de modo que cada um tenha o mesmo intervalo entre os valores inicial e final. Como resultado, alguns compartimentos podem ter mais valores se os dados estiverem concentrados em torno de um determinado ponto.

    **Bordas personalizadas**: Você pode especificar os valores que iniciam cada compartimento. O valor de borda sempre é o limite inferior do compartimento.  Por exemplo, suponha que você deseja agrupar valores em dois compartimentos, um com valores maiores que 0 e outro com valores menores que ou iguais a 0. Nesse caso, para bordas de compartimento, você digitará 0 em uma **Lista separada por vírgulas de bordas de compartimento**. A saída do módulo seria 1 e 2, indicando o índice de compartimento para cada valor de linha. Observe que a lista de valores separados por vírgulas deve estar em ordem crescente, como 1, 3, 5, 7.

4. **Número de compartimentos**: Se você estiver usando os modos de compartimentalização **Quantis** e **Mesma largura**, use essa opção para especificar quantos compartimentos ou *quantis* você deseja criar.

5. Para **Colunas a agrupar**, use o seletor de coluna para escolher as colunas que têm os valores que você deseja guardar. As colunas devem ser um tipo de dados numérico.

    A mesma regra compartimentalização é aplicada a todas as colunas aplicáveis que você escolher. Portanto, se você precisar agrupar colunas usando um método diferente, use uma instância separada de [Agrupar dados em compartimentos](group-data-into-bins.md) para cada conjunto de colunas.

    > [!WARNING]
    > Se você escolher uma coluna que não seja um tipo permitido, um erro de tempo de execução será gerado. O módulo retorna um erro assim que encontra qualquer coluna de um tipo não permitido. Se você receber um erro, examine todas as colunas selecionadas. O erro não lista todas as colunas inválidas.

6. Para o **Modo de saída**, indique como você deseja gerar os valores quantificados.

      + **Append**: Cria uma nova coluna com os valores guardados e anexa para aquela tabela de entrada.

      + **Inplace**: Substitui os valores originais com os novos valores no conjunto de dados.

      + **ResultOnly**: Retorna apenas as colunas de resultados.

7. Se você selecionar o modo de compartimentalização **Quantis**, use a opção **Normalização de quantis** para determinar como os valores são normalizados antes da classificação para quantis. Observe que a normalização de valores transforma os valores, mas não afeta o número final de compartimentos.

    Os seguintes tipos de normalização são compatíveis:

    + **Percent**: Os valores são normalizados dentro do intervalo [0,100]

    + **PQuantile**: Os valores são normalizados dentro do intervalo [0,1]

    + **QuantileIndex**:  Os valores são normalizados dentro do intervalo [1, número de compartimentos]

8. Se você escolher a opção **Bordas personalizadas**, digite uma lista separada por vírgulas de números a serem usados como *limites de compartimentalização* na caixa de texto + **Lista de limites de compartimentalização separados por vírgula**.  Os valores marcam o ponto que divide os compartimentos; portanto, se você digitar um valor de limite de compartimentalização, serão gerados dois compartimentos; se você digitar dois valores de limite de compartimentalização, três compartimentos serão gerados e assim por diante.

    Os valores devem ser classificados para que os compartimentos sejam criados, do menor para o maior.

10. **Colunas de marcas como categóricas**: Selecione esta opção para indicar que as colunas quantificadas devem ser tratadas como variáveis categóricas.

11. Enviar o pipeline.

### <a name="results"></a>Resultados

O módulo [Agrupar dados em compartimentos](group-data-into-bins.md) retorna um conjunto de dados em que cada elemento foi guardado de acordo com o modo especificado. 

Ele também retorna uma **Compartimentação de transformação**, que é uma função que pode ser passada para o módulo [Aplicar Transformação](apply-transformation.md) para guardar novos exemplos de dados usando o mesmo modo e os parâmetros de compartimentalização.  

> [!TIP]
> Lembre-se de que, se você usar a compartimentalização em seus dados de treinamento, deverá usar o mesmo método de compartimentalização nos dados que você usa para teste e previsão. Isso inclui o método de compartimentalização, os locais de compartimentos e as larguras de compartimento. 
> 
> Para garantir que os dados sempre sejam transformados usando o mesmo método de compartimentalização, recomendamos que você salve transformações de dados úteis e, em seguida, aplique-os a outros conjuntos de dados usando o módulo [Aplicar Transformação](apply-transformation.md).

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
