---
title: Visão geral do armazenamento-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados no Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: a0f1e7789c0cebdd1cb5b22f21151020a0be09c9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855109"
---
# <a name="data-storage"></a>Armazenamento de Dados

Ao criar um ambiente de Azure Time Series Insights Gen2, você cria dois recursos do Azure:

* Um ambiente de Azure Time Series Insights Gen2 que pode ser configurado para armazenamento de dados quente.
* Uma conta de armazenamento do Azure para armazenamento de dados frio.

Os dados em sua loja a quente estão disponíveis apenas por meio de [APIs de consulta de série temporal](./time-series-insights-update-tsq.md) e pelo [Azure Time Series insights Explorer TSI](./time-series-insights-update-explorer.md). Sua loja a quente conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente de Azure Time Series insights Gen2.

Azure Time Series Insights Gen2 salva seus dados de armazenamento frio no armazenamento de BLOBs do Azure no [formato de arquivo parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 gerencia esses dados de armazenamento frio exclusivamente, mas está disponível para você ler diretamente como arquivos parquet padrão.

> [!WARNING]
> Como o proprietário da conta de armazenamento de Blobs do Azure, na qual os dados de armazenamento cold residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que Azure Time Series Insights gravações Gen2 porque isso pode causar perda de dados.

## <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Gen2 partições e índices de dados para obter o desempenho de consulta ideal. Os dados tornam-se disponíveis para consulta de uma loja passiva (se habilitada) e fria após sua indexação. A quantidade de dados que está sendo ingerida pode afetar essa disponibilidade.

> [!IMPORTANT]
> Você pode experimentar um período de até 60 segundos antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa que dure mais de 60 segundos, envie um tíquete de suporte pelo portal do Azure.

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights Gen2.

Para obter uma descrição completa do armazenamento de Blobs do Azure, leia a [Introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria um ambiente de Azure Time Series Insights Gen2, uma conta de armazenamento do Azure é criada como sua loja fria de longo prazo.  

Azure Time Series Insights Gen2 retém até duas cópias de cada evento em sua conta de armazenamento do Azure. Uma cópia armazena eventos ordenados pelo tempo de ingestão, sempre permitindo o acesso a eventos em uma sequência ordenada por tempo. Ao longo do tempo, Azure Time Series Insights Gen2 também cria uma cópia reparticionada dos dados para otimizar para consultas de alto desempenho.

Todos os seus dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

#### <a name="writing-and-editing-blobs"></a>Gravando e editando BLOBs

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite nem exclua nenhum blob que Azure Time Series Insights Gen2 cria.

#### <a name="accessing-cold-store-data"></a>Acessando dados de armazenamento frio

Além de acessar seus dados Azure Time Series Insights das APIs de consulta do Gerenciador de parquet [TSI](./time-series-insights-update-explorer.md) e de [série de tempo](./time-series-insights-update-tsq.md), você também pode querer acessar seus dados diretamente dos arquivos armazenados no armazenamento frio. Por exemplo, você pode ler, transformar e limpar dados em um notebook Jupyter e, em seguida, usá-lo para treinar seu modelo do Azure Machine Learning no mesmo fluxo de trabalho do Spark.

Para acessar dados diretamente da sua conta de armazenamento do Azure, você precisa de acesso de leitura à conta usada para armazenar seus dados do Azure Time Series Insights Gen2. Você pode ler os dados selecionados com base na hora de criação do arquivo Parquet localizado na pasta `PT=Time` descrita abaixo na seção [Formato de arquivo Parquet](#parquet-file-format-and-folder-structure).  Para obter mais informações sobre como habilitar o acesso de leitura para sua conta de armazenamento, consulte [Gerenciar o acesso aos recursos de sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos do Azure Time Series Insights Gen2. Gerenciar dados relacionados somente no Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo Parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna open-source projetado para armazenamento e desempenho eficientes. Azure Time Series Insights Gen2 usa parquet para habilitar o desempenho de consulta baseado em ID de série temporal em escala.  

Para obter mais informações sobre o tipo de arquivo Parquet, leia a [documentação do Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 armazena cópias de seus dados da seguinte maneira:

* Na primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Esses dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Na segunda, a cópia reparticionada é agrupada por IDs da série temporal e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

O carimbo de data/hora nos nomes de blob na `PT=Time` pasta corresponde à hora de chegada dos dados para Azure Time Series insights Gen2 e não ao carimbo de data/hora dos eventos.

Os dados na pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não são estáticos. Durante o reparticionamento, alguns eventos podem estar presentes em vários BLOBs. Não há garantia de que a nomenclatura dos BLOBs nesta pasta permaneça a mesma.

Em geral, se você precisar acessar dados diretamente por meio de arquivos parquet, use a `PT=Time` pasta.  A funcionalidade futura permitirá o acesso eficiente à `PT=TsId` pasta.

> [!NOTE]
>
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundos de três dígitos (`fff`).

Azure Time Series Insights eventos Gen2 são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna de **carimbo de data/hora** com um carimbo de data/hora do evento. A propriedade de carimbo de data/hora nunca é nula. O padrão será o **tempo de enfileiramento do evento** se a propriedade de carimbo de data/hora não for especificada na origem do evento. O carimbo de data/hora armazenado sempre está no fuso horário UTC.
* Cada linha inclui as colunas de ID da série temporal (TSID) conforme definido quando o ambiente de Azure Time Series Insights Gen2 é criado. O nome da propriedade TSID inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de coluna que terminam com `_bool` (booliano), `_datetime` (carimbo de data/hora), `_long` (longo), `_double` (duplo), `_string` (cadeia de caracteres) ou `dynamic` (dinâmico), dependendo do tipo de propriedade.  Para obter mais informações, leia sobre [tipos de dados com suporte](./concepts-supported-data-types.md).
* Esse esquema de mapeamento se aplica à primeira versão do formato de arquivo, referenciado como **V=1** e armazenado na pasta base de mesmo nome. Conforme esse recurso evolui, esse esquema de mapeamento pode ser alterado e o nome de referência é incrementado.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre [modelagem de dados](./time-series-insights-update-tsm.md).

* Planeje seu [ambiente de Azure Time Series insights Gen2](./time-series-insights-update-plan.md).
