---
title: Ingerir em Azure Data Lake Storage Gen2 no Azure Synapse Analytics
description: Saiba como ingerir dados em Azure Data Lake Storage Gen2 no Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430572"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingestão de dados em Azure Data Lake Storage Gen2 

Neste artigo, você aprenderá a ingerir dados de um local para outro em uma conta de armazenamento Azure Data Lake Gen 2 (Azure Data Lake Gen 2) usando o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**: Use Azure data Lake Gen 2 como um armazenamento de dados de *origem* . Se você não tiver uma conta de armazenamento, consulte [criar uma conta de armazenamento do Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter as etapas para criar uma.

## <a name="create-linked-services"></a>Criar serviços vinculados

No Azure Synapse Analytics, um serviço vinculado é onde você define as informações de conexão com outros serviços. Nesta seção, você adicionará o Azure Synapse Analytics e o Azure Data Lake Gen 2 como serviços vinculados.

1. Abra o UX do Azure Synapse Analytics e vá para a guia **gerenciar** .
1. Em **conexões externas**, selecione **Serviços vinculados**.
1. Para adicionar um serviço vinculado, clique em **novo**.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e clique em **continuar**.
1. Insira suas credenciais de autenticação. A chave de conta, a entidade de serviço e a identidade gerenciada são tipos de autenticação com suporte no momento. Clique em testar conexão para verificar se suas credenciais estão corretas. 
1. Clique em **Criar** quando terminar.

## <a name="create-pipeline"></a>Criar um pipeline

Um pipeline contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta seção, você criará um pipeline que contém uma atividade de cópia que ingere dados do Azure Data Lake Gen 2 para um pool do SQL.

1. Vá para a guia **orquestrar** . Clique no ícone de adição ao lado do cabeçalho pipelines e selecione **pipeline**.
1. Em **mover e transformar** no painel atividades, arraste **copiar dados** para a tela do pipeline.
1. Clique na atividade de cópia e vá para a guia **origem** . clique em **novo** para criar um novo conjunto de fonte de origem.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e clique em continuar.
1. Selecione DelimitedText como seu formato e clique em continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho do arquivo de dados de origem e especifique se a primeira linha tem um cabeçalho. Você pode importar o esquema do repositório de arquivos ou de um arquivo de exemplo. Clique em OK quando terminar.
1. Vá para a guia **coletor** . clique em **novo** para criar um novo conjunto de coletas.
1. Selecione Azure Data Lake Storage Gen2 como seu armazenamento de dados e clique em continuar.
1. Selecione DelimitedText como seu formato e clique em continuar.
1. No painel definir propriedades, selecione o serviço vinculado ADLS que você criou. Especifique o caminho da pasta onde você deseja gravar os dados. Clique em OK quando terminar.

## <a name="debug-and-publish-pipeline"></a>Depurar e publicar o pipeline

Depois de concluir a configuração do pipeline, você poderá executar uma execução de depuração antes de publicar seus artefatos para verificar se tudo está correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela. 
1. Depois que o pipeline puder ser executado com êxito, na barra de ferramentas superior, selecione **publicar tudo**. Essa ação publica entidades (conjuntos de valores e pipelines) que você criou para o serviço de análise de Synapse.
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique no botão de sino no canto superior direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Disparar e monitorar o pipeline

Nesta etapa, você dispara manualmente o pipeline publicado na etapa anterior. 

1. Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  
1. Vá para a guia **Monitor** localizada na barra lateral esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar links na coluna **ações** para exibir detalhes da atividade e executar novamente o pipeline.
1. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação de cópia, selecione o link **detalhes** (ícone de óculos) na coluna **ações** . Para voltar ao modo de exibição Execuções de Pipeline, selecione **Execuções de Pipeline** na parte superior. Para atualizar a exibição, selecione **Atualizar**.
1. Verifique se os dados estão gravados corretamente no pool do SQL.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados para o Synapse Analytics, consulte o artigo [ingerir dados em um pool de SQL](data-integration-sql-pool.md) .
