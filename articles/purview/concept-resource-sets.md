---
title: Compreendendo os conjuntos de recursos
description: Este artigo explica quais são os conjuntos de recursos e como o Azure alcance os cria.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551452"
---
# <a name="understanding-resource-sets"></a>Compreendendo os conjuntos de recursos

Este artigo ajuda você a entender como o Azure alcance usa conjuntos de recursos para mapear ativos de dados para recursos lógicos.

## <a name="background-info"></a>Informações em segundo plano

Normalmente, os sistemas de processamento de dados em escala armazenam uma única tabela em um disco como vários arquivos. Esse conceito é representado no Azure alcance usando conjuntos de recursos. Um conjunto de recursos é um único objeto no catálogo que representa um grande número de ativos no armazenamento.

Por exemplo, suponha que o cluster Spark persistiu um dataframe em uma fonte de dados ADLS Gen2. Embora no Spark a tabela se pareça com um único recurso lógico, o disco provavelmente tem milhares de arquivos parquet, cada um representando uma partição do conteúdo total do dataframe. Dados de IoT e dados de log da Web têm o mesmo desafio. Imagine que você tenha um sensor que produza arquivos de log várias vezes por segundo. Não levará muito tempo até que você tenha centenas de milhares de arquivos de log desse único sensor.

Para resolver o desafio de mapear um grande número de ativos de dados para um único recurso lógico, o Azure alcance usa conjuntos de recursos.

## <a name="how-azure-purview-detects-resource-sets"></a>Como o Azure alcance detecta conjuntos de recursos

O Azure alcance dá suporte à detecção de conjuntos de recursos somente em BLOBs do Azure, ADLS Gen1 e ADLS Gen2.

O Azure alcance detecta automaticamente conjuntos de recursos usando um recurso chamado descoberta de conjunto de recursos automatizado. Esse recurso analisa todos os dados que são ingeridos por meio da verificação e os compara com um conjunto de padrões definidos.

Por exemplo, suponha que você examine uma fonte de dados cuja URL é `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . O Azure alcance examina os segmentos de caminho e determina se eles correspondem a quaisquer padrões internos. Ele tem padrões internos para GUIDs, números, formatos de data, códigos de localização (por exemplo, en-US) e assim por diante. Nesse caso, o padrão numérico corresponde a *23*. O alcance do Azure pressupõe que esse arquivo é parte de um conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Ou, para uma URL como `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , o Azure alcance corresponde ao padrão de localização e ao padrão de número, produzindo um conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Usando essa estratégia, o Azure alcance mapearia os seguintes recursos para o mesmo conjunto de recursos `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> O Azure Data Lake Storage Gen2 já se encontra disponível ao público em geral. É recomendável que você comece a usá-lo hoje mesmo. Para saber mais, consulte a [página do produto](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipos de arquivo que o Azure alcance não detectará como conjuntos de recursos

Alcance intencionalmente não tenta classificar a maioria dos tipos de arquivo de documento como Word, Excel ou PDF como conjuntos de recursos. A exceção é CSVs, pois isso é um formato de arquivo particionado comum.

## <a name="how-azure-purview-scans-resource-sets"></a>Como o Azure alcance examina conjuntos de recursos

Quando o Azure alcance detecta os recursos que ele considera que fazem parte de um conjunto de recursos, ele alterna de uma verificação completa para uma verificação de exemplo. Em um exame de exemplo, ele abre apenas um subconjunto dos arquivos que ele acha que estão no conjunto de recursos. Para cada arquivo que ele abre, ele usa seu esquema e executa seus classificadores. Em seguida, o Azure alcance localiza o recurso mais recente entre os recursos abertos e usa o esquema e as classificações do recurso na entrada para todo o conjunto de recursos no catálogo.

## <a name="what-azure-purview-stores-about-resource-sets"></a>O que o Azure alcance armazena sobre conjuntos de recursos

Além de um único esquema e classificações, o Azure alcance armazena as seguintes informações sobre conjuntos de recursos:

- Dados do último recurso de partição que ele examinou em profundidade.
- Agregar informações sobre os recursos de partição que compõem o conjunto de recursos.
- Uma contagem de partições que mostra quantos recursos de partição foram encontrados.
- Uma contagem de esquema que mostra quantos esquemas exclusivos ele encontrou no exemplo de conjunto em que faziam verificações profundas. Esse valor é um número entre 1 e 5, ou para valores maiores que 5, 5 +.
- Uma lista de tipos de partição quando mais de um tipo de partição única é incluído no conjunto de recursos. Por exemplo, um sensor IoT pode produzir arquivos XML e JSON, embora ambos sejam logicamente parte do mesmo conjunto de recursos.

## <a name="built-in-resource-set-patterns"></a>Padrões de conjunto de recursos internos

O Azure alcance dá suporte aos seguintes padrões de conjunto de recursos. Esses padrões podem aparecer como um nome em um diretório ou como parte de um nome de arquivo.

| Nome do padrão | Nome de exibição | Descrição |
|--------------|--------------|-------------|
| GUID         | VOLUME       | Um identificador global exclusivo, conforme definido no [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Número       | P          | Um ou mais dígitos. |
| Formatos de data/hora | P     | O Azure alcance dá suporte a diferentes tipos de formatos de data/hora, mas todos são reduzidos para uma série de {N} s. |
| 4ByteHex     | PRESSÃO        | Um número hexadecimal de quatro dígitos. |
| Localização | LOCALIZAÇÃO        | Uma marca de idioma, conforme definido no [BCP 47](https://tools.ietf.org/html/bcp47). O Azure alcance dá suporte a marcas que contêm um hífen (-) ou um sublinhado (_). Por exemplo, en_ca e en-CA. |

## <a name="issues-with-resource-sets"></a>Problemas com conjuntos de recursos

Embora os conjuntos de recursos funcionem bem na maioria dos casos, você pode encontrar os seguintes problemas, nos quais o Azure alcance:

- Marca incorretamente um ativo como um conjunto de recursos
- Coloca um ativo no conjunto de recursos errado
- Marca incorretamente um ativo como não sendo um conjunto de recursos

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o catálogo de dados, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
