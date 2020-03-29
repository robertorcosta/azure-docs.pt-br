---
title: Mapeamento do fluxo de dados Substituição chave
description: Como usar o fluxo de dados de mapeamento do Azure Data Factory Para gerar valores-chave seqüenciais
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930213"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeamento do fluxo de dados Substituição chave



Use a Transformação de Chave Alternativa para adicionar um valor de chave crescente, arbitrário e não comercial ao conjunto de linhas de seu fluxo de dados. Isso é útil durante a criação de tabelas de dimensões em um modelo de dados analítico de esquema em estrela em que cada membro das tabelas precise ter uma chave exclusiva que seja não comercial, parte importante da metodologia Kimball de DWs.

![Transformação da chave substituta](media/data-flow/surrogate.png "Transformação da chave substituta")

"Coluna de Chave" é o nome que você dará à sua nova coluna de chave alternativa.

"Valor Inicial" é o ponto de partida do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Teclas de incremento de fontes existentes

Se você quiser iniciar sua seqüência a partir de um valor que existe em uma Fonte, você pode usar uma transformação de Coluna Derivada imediatamente após a transformação da chave de substituto e adicionar os dois valores juntos:

![SK adicionar Max](media/data-flow/sk006.png "Transformação de chave de substituto adicionar max")

Para semear o valor-chave com o max anterior, existem duas técnicas que você pode usar:

### <a name="database-sources"></a>Fontes de banco de dados

Use a opção "Consulta" para selecionar MAX() da sua fonte usando a transformação Origem:

![Consulta de chave de substituto](media/data-flow/sk002.png "Consulta de transformação de chave de substituto")

### <a name="file-sources"></a>Fontes de arquivos

Se o valor máximo anterior estiver em um arquivo, você poderá usar sua transformação de Origem juntamente com uma transformação Agregada e usar a função de expressão MAX() para obter o valor máximo anterior:

![Arquivo-chave substituto](media/data-flow/sk008.png "Arquivo-chave substituto")

Em ambos os casos, você deve juntar seus novos dados de entrada juntamente com sua fonte que contém o valor máximo anterior:

![Adesão da chave substituta](media/data-flow/sk004.png "Adesão da chave substituta")

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam as transformações [de Coluna Sinuosa](data-flow-join.md) e [Derivada.](data-flow-derived-column.md)
