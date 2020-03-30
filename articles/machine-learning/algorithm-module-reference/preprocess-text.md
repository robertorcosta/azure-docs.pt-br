---
title: 'Texto de pré-processo: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Texto de Pré Processo no Azure Machine Learning para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477486"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use o módulo **Texto de pré-processo** para limpar e simplificar o texto. Ele suporta essas operações comuns de processamento de texto:

* Remoção de stop-words
* Usando expressões regulares para procurar e substituir strings de destino específicas
* Lemmatização, que converte múltiplas palavras relacionadas a uma única forma canônica
* Normalização de casos
* Remoção de certas classes de caracteres, como números, caracteres especiais e sequências de caracteres repetidos, como "aaaa"
* Identificação e remoção de e-mails e URLs

O módulo **Texto de Pré-Processo** atualmente só suporta inglês.

## <a name="configure-text-preprocessing"></a>Configurar pré-processamento de texto  

1.  Adicione o módulo **Texto de pré-processo** ao seu pipeline no Azure Machine Learning. Você pode encontrar este módulo em **Text Analytics**.

1. Conecte um conjunto de dados que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma na lista de isto **do idioma.**

1. **Coluna de texto para limpar**: Selecione a coluna que deseja pré-processar.

1. **Remover palavras de parada**: Selecione esta opção se quiser aplicar uma lista de palavras de ponto predefinidas na coluna de texto. 

    As listas de palavras de parada são dependentes de idiomas e personalizáveis.

1. **Lemmatização**: Selecione esta opção se quiser que as palavras sejam representadas em sua forma canônica. Esta opção é útil para reduzir o número de ocorrências únicas de tokens de texto semelhantes.

    O processo de lematização é altamente dependente da linguagem..

1. **Detectar frases**: Selecione esta opção se quiser que o módulo insira uma marca de limite de frase ao realizar a análise.

    Este módulo usa uma série `|||` de três caracteres de tubo para representar o exterminador de sentença.

1. Realize operações opcionais de encontrar e substituir usando expressões regulares.

    * **Expressão regular personalizada**: Defina o texto que você está procurando.
    * **Seqüência de substituição personalizada**: Defina um único valor de substituição.

1. **Normalize a caixa em minúsculas**: Selecione esta opção se quiser converter caracteres maiúsculos ASCII em suas formas minúsculas.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas é considerada duas palavras diferentes.

1. Você também pode remover os seguintes tipos de caracteres ou seqüências de caracteres do texto de saída processada:

    * **Remover números**: Selecione esta opção para remover todos os caracteres numéricos para o idioma especificado. Os números de identificação são dependentes de domínio e dependentes de linguagem. Se caracteres numéricos são parte integrante de uma palavra conhecida, o número pode não ser removido.
    
    * **Remova caracteres especiais**: Use esta opção para remover quaisquer caracteres especiais não alfanuméricos.
    
    * **Remova caracteres duplicados**: Selecione esta opção para remover caracteres extras em qualquer seqüência que se repita por mais de duas vezes. Por exemplo, uma sequência como "aaaaa" seria reduzida a "aa".
    
    * **Remover endereços de e-mail**: Selecione esta opção para remover qualquer seqüência do formato `<string>@<string>`.  
    * **Remover URLs**: Selecione esta opção para remover qualquer `http`seqüência que inclua os seguintes prefixos de URL: , `https`, `ftp``www`
    
1. **Expanda contrações verbos**: Esta opção se aplica apenas a linguagens que usam contrações verbos; atualmente, apenas inglês. 

    Por exemplo, ao selecionar essa opção, você poderia substituir a frase *"não ficaria lá"* por *"não ficaria lá".*

1. **Normalize os cortes de barras para cortes**: `\\` Selecione esta opção para mapear todas as instâncias de . `/`

1. **Tokens divididos em caracteres especiais**: Selecione esta opção `&` `-`se quiser quebrar palavras em caracteres como , e assim por diante. Esta opção também pode reduzir os caracteres especiais quando se repete mais de duas vezes. 

    Por exemplo, `MS---WORD` a seqüência seria separada `MS`em `-`três `WORD`tokens, e .

1. Envie o oleoduto.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 