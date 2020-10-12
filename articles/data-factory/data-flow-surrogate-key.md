---
title: Transformação de chave substituta no fluxo de dados de mapeamento
description: Como usar a transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory para gerar valores de chave sequencial
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606288"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformação de chave substituta no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação chave substituta para adicionar um valor de chave de incremento a cada linha de dados. Isso é útil ao criar tabelas de dimensões em um modelo de dados analíticos de esquema em estrela. Em um esquema em estrela, cada membro em suas tabelas de dimensões requer uma chave exclusiva que é uma chave não comercial.

## <a name="configuration"></a>Configuração

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

**Coluna de chave:** O nome da coluna de chave substituta gerada.

**Valor inicial:** O valor de chave mais baixo que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Incrementar chaves de fontes existentes

Para iniciar sua sequência a partir de um valor que existe em uma origem, use uma transformação coluna derivada seguindo a transformação de chave substituta para adicionar os dois valores juntos:

![SK Add Max](media/data-flow/sk006.png "Adição máxima da transformação de chave substituta")

### <a name="increment-from-existing-maximum-value"></a>Incrementar a partir do valor máximo existente

Para propagar o valor de chave com o máximo anterior, há duas técnicas que você pode usar com base em onde estão os dados de origem.

#### <a name="database-sources"></a>Fontes de banco de dados

Use uma opção de consulta SQL para selecionar MAX () de sua origem. Por exemplo, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Consulta de chave substituta](media/data-flow/sk002.png "Consulta de transformação chave substituta")

#### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior estiver em um arquivo, use a `max()` função na transformação agregação para obter o valor máximo anterior:

![Arquivo de chave substituta](media/data-flow/sk008.png "Arquivo de chave substituta")

Em ambos os casos, você deve unir seus novos dados de entrada junto com sua fonte que contém o valor máximo anterior.

![Junção de chave substituta](media/data-flow/sk004.png "Junção de chave substituta")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exemplo

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

O script de fluxo de dados para a configuração de chave substituta acima está no trecho de código abaixo.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam as transformações de [junção](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) .
