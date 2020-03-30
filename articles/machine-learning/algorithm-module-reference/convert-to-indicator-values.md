---
title: Converter em valores de indicador
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Converter para valores indicadores no Azure Machine Learning para converter colunas que contenham valores categóricos em uma série de colunas de indicadores binários.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477656"
---
# <a name="convert-to-indicator-values"></a>Converter em valores de indicador
Este artigo descreve um módulo do designer de Machine Learning do Azure.

Use o módulo **Converter para valores indicadores** no designer Azure Machine Learning para converter colunas que contenham valores categóricos em uma série de colunas de indicadores binários.  

Este módulo também produz uma definição da transformação usada para converter em valores indicadores. Você pode reutilizar essa transformação em outros conjuntos de dados que tenham o mesmo esquema, usando o módulo [Aplicar Transformação.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Como configurar converter para valores indicadores

1.  Encontre o **Converter para valores indicadores** e arraste-o para o rascunho do pipeline. Você pode encontrar este módulo na categoria **Transformação de Dados.**
    > [!NOTE]
    > Você pode usar o módulo [Editar metadados](edit-metadata.md) antes do módulo **Converter para valores do indiciador** para marcar as colunas de destino como categóricas.

1. Conecte o módulo **Converter para valores indicadores** ao conjunto de dados que contém as colunas que deseja converter. 

1. Selecione **Editar coluna** para escolher uma ou mais colunas categóricas.

1. Selecione a opção **Substituir colunas categóricas** se quiser produzir **apenas** as novas colunas booleanas. Por padrão, essa opção é desativada.
    

    > [!TIP]
    >  Se você escolher a opção de substituir, a coluna de origem não será excluída ou modificada. Em vez disso, as novas colunas são geradas e apresentadas no conjunto de dados de saída, e a coluna de origem permanece disponível no espaço de trabalho. Se você precisar ver os dados originais, você pode usar o módulo [Adicionar colunas](add-columns.md) a qualquer momento para adicionar a coluna de origem de volta.

1. Envie o oleoduto.

## <a name="results"></a>Resultados

Suponha que você tenha uma coluna com pontuações que indiquem se um servidor tem uma alta, média ou baixa probabilidade de falha.  

| ID de servidor | Pontuação de falha |
| --------- | ------------- |
| 10301     | Baixo           |
| 10302     | Médio        |
| 10303     | Alta          |

Quando você aplica **Converter em Valores indicadores,** o designer converte uma única coluna de rótulos em várias colunas contendo valores booleanos:  

| ID de servidor | Pontuação de falha - Baixa | Pontuação de falha - Médio | Pontuação de falha - Alta |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Veja como funciona a conversão:  

-   Na coluna **'Falha'** que descreve o risco, há apenas três valores possíveis (Alto, Médio e Baixo) e sem valores ausentes. Então, exatamente três novas colunas são criadas.  

-   As novas colunas indicadoras são nomeadas com base nos títulos e valores da coluna de coluna, usando este padrão: * \<coluna de origem \<>- valor dos dados>*.  

-   Deve haver uma coluna de 1 em exatamente uma coluna indicadora e 0 em todas as outras colunas indicadoras, uma vez que cada servidor pode ter apenas uma classificação de risco.  

Agora você pode usar as três colunas indicadoras como características em um modelo de aprendizado de máquina.

O módulo retorna duas saídas:

- **Conjunto de dados de resultados**: Um conjunto de dados com colunas de valores indicadores convertidos. Colunas não selecionadas para limpeza também são "passadas".
- **Transformação de valores indicadores**: Uma transformação de dados usada para converter em valores indicadores, que podem ser salvas em seu espaço de trabalho e aplicadas a novos dados posteriormente.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Aplique uma operação de valores de indicador salvo a novos dados

Se você precisar repetir as operações de valores do indicador com frequência, você pode salvar suas etapas de manipulação de dados como uma *transformação* para reutilizá-lo com o mesmo conjunto de dados. Isso é útil se você deve frequentemente reimportar e, em seguida, limpar dados que têm o mesmo esquema.

1. Adicione o módulo [Aplicar transformação](apply-transformation.md) ao seu pipeline.

1. Adicione o conjunto de dados que deseja limpar e conecte o conjunto de dados à porta de entrada à mão direita.

1. Expanda o grupo **de Transformação** de Dados no painel esquerdo do designer. Localize a transformação salva e arraste-a para o pipeline.

1. Conecte a transformação salva à porta de entrada esquerda do [Apply Transformation](apply-transformation.md).

   Quando você aplica uma transformação salva, não é possível selecionar quais colunas transformar. Isso porque a transformação foi definida e se aplica automaticamente aos tipos de dados especificados na operação original.

1. Envie o oleoduto.
 
## <a name="technical-notes"></a>Observações técnicas  

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="usage-tips"></a>Dicas de uso

-   Somente as colunas que são marcadas como categóricas podem ser convertidas em colunas de indicador. Se você vir o erro a seguir, é provável que uma das colunas selecionadas não seja categórica:  

     Erro 0056: Coluna \<com nome da coluna> não está em uma categoria permitida.  

     Por padrão, a maioria das colunas de string são tratadas como recursos de seqüência, então você deve marcá-las explicitamente como categóricas usando [Edit Metadata](edit-metadata.md).  

-   Não há limite no número de colunas que você pode converter em colunas indicadoras. No entanto, como cada coluna de valores pode produzir várias colunas indicadoras, você pode querer converter e rever apenas algumas colunas de cada vez.  

-   Se a coluna contiver valores ausentes, uma coluna de indicador separada será criada para a categoria ausente, com este nome: * \<coluna de origem>- Ausente*  

-   Se a coluna que você converter para valores indicadores contiver números, eles devem ser marcados como categóricos como qualquer outra coluna de recurso. Depois disso, os números são tratados como valores discretos. Por exemplo, se você tiver uma coluna numérica com valores de MPG que variam de 25 a 30, uma nova coluna indicadora seria criada para cada valor discreto:  

    | Faça       | Rodovia mpg -25 | Rodovia mpg -26 | Rodovia mpg -27 | Rodovia mpg -28 | Rodovia mpg -29 | Rodovia mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Carros | 0               | 0               | 0               | 0               | 0               | 1               |

- Para evitar adicionar muitas dimensões ao seu conjunto de dados. Recomendamos que você primeiro verifique o número de valores na coluna e bin ou quantize os dados adequadamente.  


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
