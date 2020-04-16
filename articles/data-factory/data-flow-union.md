---
title: Mapeamento da transformação da união do fluxo de dados
description: Azure Data Factory mapeando fluxo de dados Novo Branch Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 3bdf443a052ba8884a361774154f4d68cc180dfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417820"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory mapeando a transformação da união de fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A União combinará vários fluxos de dados em um, com o SQL Union desses fluxos como a nova saída da transformação de União. Todo o esquema de cada fluxo de entrada será combinado dentro do seu fluxo de dados, sem a necessidade de ter uma chave de adesão.

Você pode combinar n-número de fluxos na tabela de configurações selecionando o ícone "+" ao lado de cada linha configurada, incluindo tanto os dados de origem quanto os fluxos de transformações existentes no fluxo de dados.

![Transformação União](media/data-flow/union.png "Union")

Neste caso, você pode combinar metadados diferentes de várias fontes (neste exemplo, três arquivos de origem diferentes) e combiná-los em um único fluxo:

![Visão geral da transformação sindical](media/data-flow/union111.png "União 1")

Para isso, adicione linhas adicionais nas Configurações da União, incluindo todas as fontes que deseja adicionar. Não há necessidade de uma busca comum ou de uma chave de adesão:

![Configurações de transformação sindical](media/data-flow/unionsettings.png "Configurações sindicais")

Se você definir uma transformação Select após a sua União, você poderá renomear campos ou campos sobrepostos que não foram nomeados a partir de fontes sem cabeça. Clique em "Inspecionar" para ver os metadados combinados com 132 colunas totais neste exemplo de três fontes diferentes:

![Final da transformação sindical](media/data-flow/union333.png "União 3")

## <a name="name-and-position"></a>Nome e posição

Quando você escolhe "união por nome", cada valor de coluna cairá na coluna correspondente de cada fonte, com um novo esquema de metadados concatenado.

Se você escolher "união por posição", cada valor de coluna cairá na posição original de cada fonte correspondente, resultando em um novo fluxo combinado de dados onde os dados de cada fonte são adicionados ao mesmo fluxo:

![Saída sindical](media/data-flow/unionoutput.png "Saída da União")

## <a name="next-steps"></a>Próximas etapas

Explore transformações semelhantes, incluindo [O Join](data-flow-join.md) and [Exists](data-flow-exists.md).
