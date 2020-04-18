---
title: Substitua a transformação chave no mapeamento do fluxo de dados
description: Como usar o fluxo de dados de mapeamento do Azure Data Factory Para gerar valores-chave seqüenciais
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606288"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Substitua a transformação chave no mapeamento do fluxo de dados 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação da chave de substituto para adicionar um valor-chave incrementado a cada linha de dados. Isso é útil ao projetar tabelas de dimensões em um modelo de dados analíticos de esquema estelar. Em um esquema estelar, cada membro em suas tabelas de dimensões requer uma chave única que é uma chave não-empresarial.

## <a name="configuration"></a>Configuração

![Transformação da chave substituta](media/data-flow/surrogate.png "Transformação da chave substituta")

**Coluna-chave:** O nome da coluna de tecla de substituto gerada.

**Valor inicial:** O menor valor-chave que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Teclas de incremento de fontes existentes

Para iniciar sua seqüência a partir de um valor que existe em uma fonte, use uma transformação de coluna derivada após sua transformação de chave de substituto para adicionar os dois valores juntos:

![SK adicionar Max](media/data-flow/sk006.png "Transformação de chave de substituto adicionar max")

### <a name="increment-from-existing-maximum-value"></a>Incremento do valor máximo existente

Para semear o valor-chave com o max anterior, existem duas técnicas que você pode usar com base em onde seus dados de origem estão.

#### <a name="database-sources"></a>Fontes de banco de dados

Use uma opção de consulta SQL para selecionar MAX() da sua fonte. Por exemplo,`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Consulta de chave de substituto](media/data-flow/sk002.png "Consulta de transformação de chave de substituto")

#### <a name="file-sources"></a>Fontes de arquivos

Se o valor máximo anterior estiver `max()` em um arquivo, use a função na transformação agregada para obter o valor máximo anterior:

![Arquivo-chave substituto](media/data-flow/sk008.png "Arquivo-chave substituto")

Em ambos os casos, você deve juntar seus novos dados de entrada juntamente com sua fonte que contém o valor máximo anterior.

![Adesão da chave substituta](media/data-flow/sk004.png "Adesão da chave substituta")

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

![Transformação da chave substituta](media/data-flow/surrogate.png "Transformação da chave substituta")

O script de fluxo de dados para a configuração de chave de substituto acima está no trecho de código abaixo.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam as transformações [de Coluna Sinuosa](data-flow-join.md) e [Derivada.](data-flow-derived-column.md)
