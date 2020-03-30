---
title: 'Dados ausentes limpos: referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Clean Missing Data no Azure Machine Learning para remover, substituir ou inferir valores ausentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477690"
---
# <a name="clean-missing-data-module"></a>Módulo de dados ausentes limpos

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para remover, substituir ou inferir valores ausentes. 

Os cientistas de dados geralmente verificam dados para valores perdidos e, em seguida, realizam várias operações para corrigir os dados ou inserir novos valores. O objetivo dessas operações de limpeza é evitar problemas causados pela falta de dados que possam surgir ao treinar um modelo. 

Este módulo suporta vários tipos de operações para "limpar" valores ausentes, incluindo:

+ Substituindo valores faltantes por um espaço reservado, médio ou outro valor
+ Removendo completamente linhas e colunas que têm valores ausentes
+ Inferindo valores baseados em métodos estatísticos


O uso deste módulo não altera o conjunto de dados de origem. Em vez disso, ele cria um novo conjunto de dados em seu espaço de trabalho que você pode usar no fluxo de trabalho subsequente. Você também pode salvar o novo conjunto de dados limpo para reutilização.

Este módulo também produz uma definição da transformação usada para limpar os valores faltantes. Você pode reutilizar essa transformação em outros conjuntos de dados que tenham o mesmo esquema, usando o módulo [Aplicar transformação.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Como usar dados que faltam limpos

Este módulo permite definir uma operação de limpeza. Você também pode salvar a operação de limpeza para que você possa aplicá-la mais tarde a novos dados. Veja as seções a seguir de como criar e salvar um processo de limpeza: 
 
+ [Para substituir os valores faltantes](#replace-missing-values)
  
+ [Para aplicar uma transformação de limpeza a novos dados](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> O método de limpeza que você usa para lidar com valores perdidos pode afetar drasticamente seus resultados. Recomendamos que você experimente com diferentes métodos. Considere tanto a justificativa para o uso de um determinado método, quanto a qualidade dos resultados.

### <a name="replace-missing-values"></a>Substituir valores ausentes  

Cada vez que você aplica o módulo ['Limpar dados faltantes'](./clean-missing-data.md) a um conjunto de dados, a mesma operação de limpeza é aplicada a todas as colunas selecionadas. Portanto, se você precisar limpar diferentes colunas usando diferentes métodos, use instâncias separadas do módulo.

1.  Adicione o módulo ['Limpar dados faltantes'](./clean-missing-data.md) ao seu pipeline e conecte o conjunto de dados que tem valores ausentes.  
  
2.  Para **que as colunas sejam limpas,** escolha as colunas que contêm os valores que você deseja alterar. Você pode escolher várias colunas, mas deve usar o mesmo método de substituição em todas as colunas selecionadas. Portanto, normalmente você precisa limpar colunas de strings e colunas numéricas separadamente.

    Por exemplo, para verificar se faltam valores em todas as colunas numéricas:

    1. Selecione o módulo **'Limpar dados faltantes'** e clique em **Editar coluna** no painel direito do módulo.

    3. Em **Incluir,** **selecione ''Incluir', selecione 'Tipos de coluna'** na lista de desímpara das vertentes e, em seguida, **selecione Numérico**. 
  
    Qualquer método de limpeza ou substituição que você escolher deve ser aplicável a **todas as** colunas da seleção. Se os dados em qualquer coluna forem incompatíveis com a operação especificada, o módulo reameda um erro e interrompe o pipeline.
  
3.  Para **a taxa de valor faltante mínima,** especifique o número mínimo de valores faltantes necessários para a operação a ser realizada.  
  
    Você usa esta opção em combinação com **a relação de valor faltante máximo** para definir as condições em que uma operação de limpeza é realizada no conjunto de dados. Se houver muitas ou poucas linhas que faltam valores, a operação não pode ser realizada. 
  
    O número digitado representa a **razão** de valores faltantes para todos os valores da coluna. Por padrão, a **propriedade de taxa de valor faltante mínima** é definida como 0. Isso significa que os valores perdidos são limpos mesmo que haja apenas um valor em falta. 

    > [!WARNING]
    > Esta condição deve ser atendida por cada coluna para que a operação especificada seja aplicada. Por exemplo, suponha que você selecionou três colunas e, em seguida, defina a razão mínima de valores faltantes para 0,2 (20%), mas apenas uma coluna realmente tem 20% de valores faltantes. Neste caso, a operação de limpeza se aplicaria apenas à coluna com mais de 20% de valores faltantes. Portanto, as outras colunas ficariam inalteradas.
    > 
    > Se você tiver alguma dúvida sobre se os valores ausentes foram alterados, selecione a opção **Gerar coluna de indicador de valores ausentes**. Uma coluna é anexada ao conjunto de dados para indicar se cada coluna atendeu ou não aos critérios especificados para as faixas mínima e máxima.  
  
4. Para **a razão de valor faltante máxima,** especifique o número máximo de valores faltantes que podem estar presentes para a operação a ser realizada.   
  
    Por exemplo, você pode querer realizar a substituição de valor ausente apenas se 30% ou menos das linhas contiverem valores ausentes, mas deixe os valores como está se mais de 30% das linhas tiverem valores faltantes.  
  
    Você define o número como a proporção de valores ausentes para todos os valores na coluna. Por padrão, a **razão de valor faltante máximo** é definida como 1. Isso significa que os valores faltantes são limpos mesmo que 100% dos valores da coluna estejam faltando.  
  
   
  
5. Para **o modo de limpeza,** selecione uma das seguintes opções para substituir ou remover valores ausentes:  
  
  
    + **Valor de substituição personalizado**: Use esta opção para especificar um valor de espaço reservado (como um 0 ou NA) que se aplica a todos os valores faltantes. O valor especificado como substituição deve ser compatível com o tipo de dados da coluna.
  
    + **Substitua por média**: Calcula a média da coluna e usa a média como valor de substituição para cada valor ausente na coluna.  
  
        Aplica-se apenas a colunas que tenham tipos de dados Inteiro, Duplo ou Booleano.  
  
    + **Substitua por mediana**: Calcula o valor mediano da coluna e usa o valor mediano como substituto de qualquer valor ausente na coluna.  
  
        Aplica-se apenas a colunas que tenham tipos de dados Inteiros ou Duplos. 
  
    + **Substitua pelo modo**: Calcula o modo para a coluna e usa o modo como valor de substituição para cada valor perdido na coluna.  
  
        Aplica-se às colunas que têm tipos de dados Inteiro, Duplo, Booleano ou Categórico. 
  
    + **Remova toda a linha**: Remove completamente qualquer linha no conjunto de dados que tenha um ou mais valores ausentes. Isso será útil se o valor ausente puder ser considerado como ausente aleatoriamente.  
  
    + **Remova a coluna inteira:** Remove completamente qualquer coluna no conjunto de dados que tenha um ou mais valores ausentes.  
  
    
  
6. O **valor de substituição da** opção está disponível se você tiver selecionado a opção, **valor de substituição personalizado**. Digite um novo valor para usar como valor de substituição para todos os valores faltantes na coluna.  
  
    Observe que você pode usar esta opção apenas em colunas que tenham o Inteiro, Duplo, Booleano ou String.
  
7. **Gerar coluna indicadora de valor ausente**: Selecione esta opção se quiser dar alguma indicação se os valores na coluna atenderam aos critérios de falta de limpeza de valor. Esta opção é particularmente útil quando você está configurando uma nova operação de limpeza e deseja ter certeza de que funciona como projetado.
  
8. Envie o oleoduto.

### <a name="results"></a>Resultados

O módulo retorna duas saídas:  

-   **Conjunto de dados limpos**: Um conjunto de dados composto pelas colunas selecionadas, com valores ausentes manipulados conforme especificado, juntamente com uma coluna indicadora, se você selecionou essa opção.  

    Colunas não selecionadas para limpeza também são "passadas".  
  
-  **Transformação de limpeza**: Uma transformação de dados usada para limpeza, que pode ser salva em seu espaço de trabalho e aplicada a novos dados posteriormente.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Aplique uma operação de limpeza salva a novos dados  

Se você precisar repetir as operações de limpeza com frequência, recomendamos que você guarde sua receita para a limpeza de dados como uma *transformação,* para reutilizar com o mesmo conjunto de dados. Salvar uma transformação de limpeza é particularmente útil se você deve frequentemente reimportar e, em seguida, limpar dados que têm o mesmo esquema.  
      
1.  Adicione o módulo [Aplicar transformação](./apply-transformation.md) ao seu pipeline.  
  
2.  Adicione o conjunto de dados que deseja limpar e conecte o conjunto de dados à porta de entrada à mão direita.  
  
3.  Expanda o grupo **Transforms** no painel esquerdo do designer. Localize a transformação salva e arraste-a para o pipeline.  

4.  Conecte a transformação salva à porta de entrada esquerda do [Apply Transformation](./apply-transformation.md). 

    Quando você aplica uma transformação salva, não é possível selecionar as colunas às quais a transformação é aplicada. Isso porque a transformação já foi definida e se aplica automaticamente às colunas especificadas na operação original.

    No entanto, suponha que você criou uma transformação em um subconjunto de colunas numéricas. Você pode aplicar essa transformação a um conjunto de dados de tipos de colunas mistas sem levantar um erro, porque os valores faltantes são alterados apenas nas colunas numéricas correspondentes.

6.  Envie o oleoduto.  

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 