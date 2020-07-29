---
title: Importar dados para o designer (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como importar dados para o designer de Azure Machine Learning (versão prévia) de várias fontes de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 92568ceb9c7f0a428189647f259a3f84278eb612
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326997"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Importar dados para o designer de Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá a importar seus próprios dados para o designer para criar soluções personalizadas. Há duas maneiras de importar dados para o designer: 

* **Conjuntos de dados do Azure Machine Learning** - Registre [conjuntos de dados](concept-data.md#datasets) no Azure Machine Learning para habilitar recursos avançados que ajudam a gerenciar os dados.
* **Módulo Importar Dados** – Use o módulo [Importar Dados](algorithm-module-reference/import-data.md) para acessar diretamente os dados de fontes de dados online.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Usar conjuntos de dados do Azure Machine Learning

É recomendável que você use [conjuntos de dados](concept-data.md#datasets) para importar para o designer. Quando você registra um conjunto de dados, pode aproveitar ao máximo os recursos avançados, como [controle de versão e acompanhamento](how-to-version-track-datasets.md) e [monitoramento de dados](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrar um conjunto de dados

Você pode registrar conjuntos de dados existentes [programaticamente com o SDK](how-to-create-register-datasets.md#use-the-sdk) ou [visualmente no Azure Machine Learning Studio](how-to-create-register-datasets.md#use-the-ui).

Além disso, é possível registrar o resultado de qualquer módulo do designer como um conjunto de dados.

1. Selecione o módulo que gera os dados que você deseja registrar.

1. No painel Propriedades, selecione **Saídas** > **Registrar conjunto de dados**.

    ![Captura de tela mostrando como navegar até a opção Registrar conjunto de dados](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Usar um conjunto de dados

Os conjuntos de dados registrados podem ser encontrados na paleta de módulos, em **Conjuntos de dados** > **Meus conjuntos de dados**. Para usar um conjunto de dados, arraste-o e solte-o na tela do pipeline. Em seguida, conecte a porta de saída do conjunto de dados a outros módulos na paleta.

![Captura de tela mostrando o local dos conjuntos de dados salvos na paleta do designer](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> No momento, o designer oferece suporte apenas ao processamento de [conjuntos de dados tabulares](how-to-create-register-datasets.md#dataset-types). Se você quiser usar [conjuntos de dados de arquivo](how-to-create-register-datasets.md#dataset-types), use o SDK do Azure Machine Learning disponível para Python e R.

## <a name="import-data-using-the-import-data-module"></a>Importar dados usando o módulo Importar Dados

Embora seja recomendável usar os conjuntos de dados para importar dados, também é possível usar o módulo [Importar Dados](algorithm-module-reference/import-data.md). O módulo Importar Dados ignora o registro do conjunto de dados no Azure Machine Learning e os importa os diretamente de um [armazenamento de dados](concept-data.md#datastores) ou URL de HTTP.

Para obter informações detalhadas sobre como usar o módulo Importar Dados, consulte a [página de referência Importar Dados](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se o conjunto de dados tiver muitas colunas, você poderá encontrar o seguinte erro: "Falha na validação devido à limitação de tamanho". Para evitar isso, [registre o conjunto de dados na interface Conjuntos de dados](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Fontes compatíveis

Esta seção lista as fontes de dados compatíveis com o designer. Os dados são inseridos no designer por meio de um armazenamento de dados ou de um [conjunto de dados tabular](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Fontes de armazenamento de dados
Para obter uma lista das fontes de armazenamento de dados compatíveis, consulte [Acessar dados nos serviços de armazenamento do Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Fontes de conjunto de dados tabular

O designer oferece suporte a conjuntos de dados tabulares criados a partir das seguintes fontes:
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

O designer usa um tipo de dados interno chamado para transmitir dados entre módulos. Você pode converter explicitamente os dados no formato de tabela de dados usando o módulo [Converter em conjunto de dados](algorithm-module-reference/convert-to-dataset.md). Qualquer módulo que aceite formatos diferentes do formato interno converterá os dados silenciosamente antes de transmiti-los para o próximo módulo.

## <a name="data-constraints"></a>Restrições de dados

Os módulos no designer são limitados pelo tamanho do destino de computação. Em conjuntos de dados maiores, use um recurso de computação do Azure Machine Learning maior. Para obter mais informações sobre a computação do Azure Machine Learning, consulte [O que são os destinos de computação no Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Acessar dados em uma rede virtual

Se o seu espaço de trabalho estiver em uma rede virtual, você deverá executar etapas de configuração adicionais para visualizar dados no designer. Para obter mais informações sobre como usar armazenamentos de dados e conjuntos de dados em uma rede virtual, consulte [isolamento de rede durante o treinamento & inferência com redes virtuais privadas](how-to-enable-virtual-network.md#machine-learning-studio).

## <a name="next-steps"></a>Próximas etapas

Aprenda as noções básicas do designer com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md).
