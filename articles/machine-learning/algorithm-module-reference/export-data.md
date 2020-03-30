---
title: 'Dados de exportação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Dados de Exportação no Azure Machine Learning para salvar resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456294"
---
# <a name="export-data-module"></a>Módulo de dados de exportação

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para salvar resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem. 

Este módulo suporta exportar seus dados para os seguintes serviços de dados em nuvem:

- Recipiente Azure Blob
- Compartilhamento de arquivos do Azure
- Azure Data Lake
- Azure Data Lake Gen2

Antes de exportar seus dados, você precisa primeiro registrar um datastore no seu espaço de trabalho azure Machine Learning. Para obter mais informações, consulte [os dados de acesso nos serviços de armazenamento do Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **Dados de Exportação** ao seu pipeline no designer. Você pode encontrar este módulo na categoria **Entrada e Saída.**

1. Conecte **os dados de exportação** ao módulo que contém os dados que deseja exportar.

1. Selecione **Dados de exportação** para abrir o painel **Propriedades.**

1. Para **Datastore,** selecione um armazenamento de dados existente na lista de saque. Você também pode criar um novo datastore. Confira como visitando [dados do Access nos serviços de armazenamento do Azure](../how-to-access-data.md).

1. A caixa de seleção, **reger a saída,** decide se executa o módulo para regenerar a saída no tempo de execução. 

    É por padrão não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de execução. 

    Se for selecionado, o sistema executará o módulo novamente para regenerar a saída.

1. Defina o caminho no datastore onde os dados estão. O caminho é um caminho relativo. Os caminhos vazios ou os caminhos de URL não são permitidos.


1. Para **o formato Arquivo,** selecione o formato no qual os dados devem ser armazenados.
 
1. Envie o oleoduto.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
