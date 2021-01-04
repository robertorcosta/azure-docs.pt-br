---
title: 'Tutorial: Treinar um modelo usando o ML automatizado'
description: Tutorial sobre como treinar um modelo de machine learning sem código no Azure Synapse usando o Apache Spark e o ML automatizado.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093393"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>Tutorial: Treinar um modelo de machine learning sem código no Azure Synapse com o Apache Spark e o ML automatizado

Saiba como enriquecer facilmente seus dados em tabelas do Spark com novos modelos de machine learning que você treinar usando o [ML automatizado no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Um usuário no Azure Synapse pode simplesmente selecionar uma tabela do Spark no workspace do Azure Synapse para ser usada como um conjunto de dados de treinamento para criar modelos de machine learning em uma experiência sem código.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> - Treinar modelos de machine learning usando uma experiência sem código no Azure Synapse Studio que usa ML automatizado no Azure Machine Learning. O tipo de modelo que você treina depende do problema que está tentando resolver.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Serviço vinculado do Azure Machine Learning em seu workspace do Azure Synapse Analytics. Para obter mais detalhes, confira [Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Criar uma tabela do Spark para o conjunto de dados de treinamento

Você precisará de uma tabela do Spark para este tutorial. O notebook a seguir criará uma tabela do Spark.

1. Baixe o notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importe o notebook para o Azure Synapse Studio.
![Importar Notebook](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecione o Pool do Spark que você deseja usar e clique em `Run all`. A execução deste notebook obterá os dados de táxi de Nova York de um conjunto de dados aberto e os salvará no seu banco de dados do Spark padrão.
![Executar tudo](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Depois que a execução do notebook for concluída, uma tabela do Spark será criada no banco de dados do Spark padrão. Acesse o Hub de Dados e localize a tabela com o nome `nyc_taxi`.
![Tabela do Spark](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>Iniciar o assistente de ML automatizado para treinar um modelo

Clique com o botão direito do mouse na tabela do Spark criada na etapa anterior. Selecione "Machine Learning -> Enriquecer com o novo modelo" para abrir o assistente.
![Iniciar o assistente de ML automatizado](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Um painel de configuração será exibido e você deverá fornecer detalhes de configuração para a criação de um experimento de ML automatizado executado no Azure Machine Learning. Essa execução treinará vários modelos e o melhor modelo de uma execução bem-sucedida será colocado no Registro de modelo do Azure Machine Learning:

![Etapa 1 de Configurar a execução](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Workspace do Azure Machine Learning**: é necessário um Workspace do Azure Machine Learning para a criação de uma execução de experimento de ML automatizado. Você também precisa vincular seu workspace do Azure Synapse ao Workspace do Azure Machine Learning usando um [serviço vinculado](quickstart-integrate-azure-machine-learning.md). Depois de ter todos os pré-requisitos, você poderá especificar o Workspace do Azure Machine Learning que deseja usar para essa execução de ML automatizado.

- **Nome do experimento**: especifique o nome do experimento. Quando você envia uma execução de ML automatizado, você pode fornecer um nome de experimento. As informações da execução são armazenadas nesse experimento no Workspace do Azure Machine Learning. Essa experiência criará um experimento por padrão e gerará um nome proposto, mas você também poderá fornecer um nome de um experimento existente.

- **Melhor modelo**: especifique o nome do melhor modelo com base na execução de ML automatizado. O melhor modelo receberá esse nome e será salvo no Registro de modelo do Azure Machine Learning automaticamente após essa execução. Uma execução de ML automatizado criará muitos modelos de machine learning. Com base na métrica primária que você selecionará em uma etapa posterior, esses modelos podem ser comparados e o melhor modelo pode ser selecionado.

- **Coluna de destino**: isso é o que o modelo é treinado para prever. Escolha a coluna que você deseja prever.

- **Pool do Spark**: o Pool do Spark que você deseja usar para a execução de experimento de ML automatizado. Os cálculos serão executados no pool que você especificar.

- **Detalhes de configuração do Spark**: além do Pool do Spark, você também tem a opção de fornecer detalhes de configuração de sessão.

Neste tutorial, selecionamos a coluna numérica `fareAmount` como a coluna de destino.

Clique em "Continuar".

## <a name="choose-task-type"></a>Escolher o tipo de tarefa

Selecione o tipo de modelo de machine learning para o experimento com base na pergunta que você está tentando responder. Como selecionamos `fareAmount` como a coluna de destino e ele é um valor numérico, selecionaremos *Regressão*.

Clique em "Continuar" para configurar as definições adicionais.

![Seleção de tipo de tarefa](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurações adicionais

Se você selecionar o tipo *Classificação* ou *Regressão*, as configurações adicionais serão:

- **Métrica principal**: a métrica usada para medir o desempenho do modelo. Essa é a métrica que será usada para comparar modelos diferentes criados na execução de ML automatizado e determinar qual modelo teve melhor desempenho.

- **Tempo do trabalho de treinamento (horas)** : o período máximo de tempo, em horas, para que um experimento execute e treine modelos. Observe que você também pode fornecer valores menores que 1. Por exemplo, `0.5`.

- **Máximo de iterações simultâneas**: representa o número máximo de iterações que seriam executadas em paralelo.

- **Compatibilidade de modelo de ONNX**: se habilitado, os modelos treinados pelo ML automatizado serão convertidos no formato ONNX. Isso será particularmente relevante se você quiser usar o modelo para pontuação em pools de SQL do Azure Synapse.

Todas essas configurações têm um valor padrão que você pode personalizar.
![configurações adicionais](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Observe que, se você selecionar "Previsão de série temporal", haverá mais configurações necessárias. A previsão também não dá suporte à compatibilidade de modelo ONNX.

Depois que todas as configurações necessárias forem concluídas, você poderá iniciar a execução de ML automatizado.

Há duas maneiras de iniciar uma execução de ML automatizado no Azure Synapse. Para obter uma experiência sem código, você pode optar por **Criar a execução** diretamente. Se você preferir o código, poderá selecionar **Abrir no notebook**, que permite ver o código que cria a execução e executar o notebook.

### <a name="create-run-directly"></a>Criar Execução diretamente

Clique em "Iniciar Execução" para iniciar a execução de ML automatizado diretamente. Haverá uma notificação que indica que a execução de ML automatizado está sendo iniciada.

Depois que a execução de ML automatizado for iniciada com êxito, você verá outra notificação com êxito. Você também pode clicar no botão de notificação para verificar o estado do envio de execução.
O Azure Machine Learning clicando no link na notificação de êxito.
![Notificações de êxito](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Criar execução com notebook

Selecione *Abrir no Notebook* para gerar um notebook. Clique em *Executar tudo* para executar o notebook.
Isso também dá a oportunidade de adicionar configurações adicionais à sua execução de ML automatizado.

![Abrir Notebook](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Depois que a execução do notebook tiver sido enviada com êxito, haverá um link para a execução do experimento no Workspace do Azure Machine Learning na saída do notebook. Você pode clicar no link para monitorar sua execução de ML automatizado no Azure Machine Learning.
![Executar tudo do notebook](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Pontuação do modelo de machine learning em Pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)
