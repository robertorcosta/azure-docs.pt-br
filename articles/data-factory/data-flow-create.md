---
title: Criar um fluxo de dados de mapeamento
description: Como criar um fluxo de dados de mapeamento da Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416521"
---
# <a name="create-azure-data-factory-data-flow"></a>Criar Fluxo de Dados do Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os Fluxos de Dados de Mapeamento no ADF fornecem uma maneira de transformar dados em grande escala sem a necessidade de nenhuma codificação. Você pode criar um trabalho de transformação de dados no designer de fluxo de dados, criando uma série de transformações. Comece com qualquer número de transformações de código-fonte, seguidas pelas etapas de transformação de dados. Em seguida, conclua seu fluxo de dados com o coletor para descarregar seus resultados em um destino.

Comece criando uma nova fábrica de dados V2 a partir do portal Azure. Depois de criar sua nova fábrica, clique no bloco “Criar e Monitorar” para iniciar a IU do Data Factory.

![Opções de Fluxo de Dados](media/data-flow/v2portal.png "fluxo de dados criar")

Quando estiver na IU do Data Factory, você pode usar Fluxos de Dados de exemplo. Os exemplos estão disponíveis na Galeria de Modelos do ADF. Em ADF, crie “Pipeline de modelo” e selecione a categoria de Fluxo de Dados da galeria de modelos.

![Opções de Fluxo de Dados](media/data-flow/template.png "fluxo de dados criar")

Será solicitado que você insira suas informações de conta do Armazenamento de Blobs do Azure.

![Opções de Fluxo de Dados](media/data-flow/template2.png "fluxo de dados criar 2")

[Os dados usados para esses exemplos podem ser encontrados aqui](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Baixe os dados de exemplo e armazene os arquivos em suas contas de armazenamento de Blobs do Azure para que você possa executar os exemplos.

## <a name="create-new-data-flow"></a>Criar novo fluxo de dados

Use o botão Criar recurso "mais sinal" na UI aDF para criar fluxos de dados.

![Opções de Fluxo de Dados](media/data-flow/newresource.png "Novo recurso")

## <a name="next-steps"></a>Próximas etapas

Comece a construir sua transformação de dados com uma [transformação de origem.](data-flow-source.md)
