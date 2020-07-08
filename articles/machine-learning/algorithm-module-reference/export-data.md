---
title: 'Exportar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo exportar dados no Azure Machine Learning para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456294"
---
# <a name="export-data-module"></a>Exportar módulo de dados

Este artigo descreve um módulo no designer do Azure Machine Learning (versão prévia).

Use este módulo para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem. 

Este módulo dá suporte à exportação de dados para os seguintes serviços de dados de nuvem:

- Contêiner de blob do Azure
- Compartilhamento de arquivos do Azure
- Azure Data Lake
- Azure Data Lake Gen2

Antes de exportar seus dados, você precisa primeiro registrar um repositório de armazenamento em seu espaço de trabalho Azure Machine Learning. Para obter mais informações, consulte [acessar dados nos serviços de armazenamento do Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **exportar dados** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria **entrada e saída** .

1. Conecte **dados de exportação** ao módulo que contém os dados que você deseja exportar.

1. Selecione **exportar dados** para abrir o painel **Propriedades** .

1. Para o **repositório de armazenamento**, selecione um repositório de armazenamento existente na lista suspensa. Você também pode criar um novo repositório de armazenamento. Verifique como [acessar dados do Access nos serviços de armazenamento do Azure](../how-to-access-data.md).

1. A caixa de seleção, **regenerar saída**, decide se o módulo deve ser executado para regenerar a saída em tempo de execução. 

    Ele é, por padrão, desmarcado, o que significa que, se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de execução. 

    Se estiver selecionado, o sistema executará o módulo novamente para regenerar a saída.

1. Defina o caminho no repositório de dados onde estão os dados. O caminho é um caminho relativo. Não são permitidos caminhos vazios ou caminhos de URL.


1. Para **formato de arquivo**, selecione o formato no qual os dados devem ser armazenados.
 
1. Envie o pipeline.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
