---
title: Mapeando a transformação de fluxo de dados não dinâmico
description: Transformação não dinâmica do fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: e7c0a4cd6e44994c4b002fcc2e5fde441cf22283
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541644"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory transformação não dinâmica

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use o fluxo de dados de mapeamento do AAD no ADF como uma maneira de transformar um conjunto de dado não normalizado em uma versão mais normalizada expandindo valores de várias colunas em um único registro em vários registros com os mesmos valores em uma única coluna.

![Transformação não dinâmica](media/data-flow/unpivot1.png "Opções não dinâmicas 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Desagrupar por

![Transformação não dinâmica](media/data-flow/unpivot5.png "Opções não dinâmicas 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de tabela dinâmica. Defina uma ou mais colunas para desagrupamento com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não dinâmica

![Transformação não dinâmica](media/data-flow/unpivot6.png "Opções não dinâmicas 3")

A Chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar para valores de coluna.

## <a name="unpivoted-columns"></a>Colunas não dinâmicas

![Transformação não dinâmica](media/data-flow//unpivot7.png "Opções de UNPIVOT 4")

Por fim, escolha a agregação que você deseja usar para os valores dinâmicos e como você gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, dinamizar “Vendas” por “Região” simplesmente forneceria novos valores de coluna de cada valor de vendas. Por exemplo: "25", "50", "1000",... No entanto, se você definir um valor de prefixo de "vendas", então "vendas" será prefixado para os valores.

![Imagem mostrando as colunas PO, Vendor e fruta antes e depois de uma transformação unipivot usando a coluna fruta como a chave unipivot.](media/data-flow/unpivot3.png)

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinamizadas com seus valores agregados. Definir a disposição de colunas como “Lateral” alternará entre coluna e valor.

![Transformação não dinâmica](media/data-flow//unpivot7.png "Opções de UNPIVOT 5")

O conjunto final de resultados de dados não dinâmicos mostra os totais de colunas agora transformadas em valores de linha separados.

## <a name="next-steps"></a>Próximas etapas

Use a [transformação dinâmica](data-flow-pivot.md) para dinamizar linhas para colunas.
