---
title: Visão geral do conector do Azure Data Factory
description: Conheça os conectores com suporte no Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: d7a872121ca6560b8ede86abc35294ab8c9b0c1b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142013"
---
# <a name="azure-data-factory-connector-overview"></a>Visão geral do conector do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory oferece suporte aos seguintes armazenamentos de dados e formatos por meio de cópia, fluxo de dados, pesquisa, obtenção de metadados e atividades de exclusão. Clique em cada armazenamento de dados para aprender os recursos com suporte e as configurações correspondentes em detalhes.

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Formatos de arquivo com suporte

O Azure Data Factory dá suporte aos seguintes formatos de arquivo. Consulte cada artigo para configurações baseadas em formato.

- [Formato Avro](format-avro.md)
- [Formato binário](format-binary.md)
- [Formato do Common Data Model](format-common-data-model.md)
- [Formato de texto delimitado](format-delimited-text.md)
- [Formato do Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)

## <a name="next-steps"></a>Próximas etapas

- [Atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)
