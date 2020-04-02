---
title: Importar dados
titleSuffix: Azure Machine Learning
description: Saiba como importar seus dados para o designer de Machine Learning do Azure a partir de várias fontes de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546747"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importe seus dados para o azure Machine Learning designer (visualização)

Neste artigo, você aprende a importar seus próprios dados no designer para criar soluções personalizadas. Há duas maneiras de importar dados para o designer: 

* **Conjuntos de dados Azure Machine Learning** - Registre [conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para habilitar recursos avançados que ajudam a gerenciar seus dados.
* **Importar módulo de dados** - Use o módulo [Importar dados](algorithm-module-reference/import-data.md) para acessar diretamente dados de fontes de dados on-line.

## <a name="use-azure-machine-learning-datasets"></a>Use conjuntos de dados de aprendizado de máquina do Azure

Recomendamos que você use [conjuntos de dados](concept-data.md#datasets) para importar dados para o designer. Quando você registra um conjunto de dados, você pode aproveitar ao máximo os recursos avançados de dados, como [versão e rastreamento e](how-to-version-track-datasets.md) monitoramento de [dados.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Registre um conjunto de dados

Você pode registrar conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no estúdio Azure Machine Learning](how-to-create-register-datasets.md#use-the-ui).

Você também pode registrar a saída para qualquer módulo de designer como um conjunto de dados.

1. Selecione o módulo que produz os dados que deseja registrar.

1. No painel propriedades, selecione **'Saídas** > **Registrar conjunto de dados 'Registro'.**

    ![Captura de tela mostrando como navegar até a opção Registrar conjunto de dados](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Use um conjunto de dados

Seus conjuntos de dados registrados podem ser encontrados na paleta de **módulos,** > em Conjuntos de**dados Meus Conjuntos de Dados**. Para usar um conjunto de dados, arraste e solte-o na tela do pipeline. Em seguida, conecte a porta de saída do conjunto de dados a outros módulos na paleta.

![Captura de tela mostrando a localização dos conjuntos de dados salvos na paleta de designer](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Atualmente, o designer suporta apenas conjuntos [de dados tabulares de processamento](how-to-create-register-datasets.md#dataset-types). Se você quiser usar [conjuntos de dados de arquivos,](how-to-create-register-datasets.md#dataset-types)use o Azure Machine Learning SDK disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Importar dados usando o módulo Dados de Importação

Embora recomendamos que você use conjuntos de dados para importar dados, você também pode usar o módulo [Importar dados.](algorithm-module-reference/import-data.md) O módulo Importar dados ignora o registro do seu conjunto de dados no Azure Machine Learning e importa dados diretamente de um [datastore](concept-data.md#datastores) ou URL HTTP.

Para obter informações detalhadas sobre como usar o módulo Dados de Importação, consulte a [página de referência Dados de importação](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se o conjunto de dados tiver muitas colunas, você pode encontrar o seguinte erro: "Falha de validação devido à limitação de tamanho". Para evitar isso, [registre o conjunto de dados na interface Datasets](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Fontes suportadas

Esta seção lista as fontes de dados suportadas pelo designer. Os dados entram no designer de um datastore ou de um conjunto de [dados tabular](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Fontes de datastore
Para obter uma lista de fontes de armazenamento de dados suportadas, consulte [dados de acesso nos serviços de armazenamento do Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Fontes de conjunto de dados tabular

O designer suporta conjuntos de dados tabulares criados a partir das seguintes fontes:
 * Arquivos delimitados
 * Arquivos JSON
 * Arquivos Parquet
 * Consultas SQL

## <a name="data-types"></a>Tipos de dados

O designer reconhece internamente os seguintes tipos de dados:

* String
* Integer
* Decimal
* Boolean
* Data

O designer usa um tipo de dados interno para passar dados entre módulos. Você pode converter explicitamente os dados no formato de tabela de dados usando o módulo [Converter em Conjunto de Dados](algorithm-module-reference/convert-to-dataset.md). Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de passá-los para o próximo módulo.

## <a name="data-constraints"></a>Restrições de dados

Os módulos do designer são limitados pelo tamanho do alvo da computação. Para conjuntos de dados maiores, você deve usar um recurso maior de computação de aprendizado de máquina do Azure. Para obter mais informações sobre a computação do Azure Machine Learning, consulte [Quais são os alvos de computação no Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Próximas etapas

Aprenda o básico do designer com [tutorial: preveja o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md).
