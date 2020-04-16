---
title: Ingerido no pool SQL no Azure Synapse Analytics
description: Saiba como ingerir dados em um pool SQL no Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430559"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Ingerindo dados em um pool SQL

Neste artigo, você aprenderá como ingerir dados de uma conta de armazenamento Azure Data Lake Gen 2 em um pool SQL usando o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento Azure**: Você usa o Azure Data Lake Storage Gen 2 como um armazenamento de dados *de origem.* Se você não tiver uma conta de armazenamento, consulte [Criar uma conta do Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter etapas para criar uma.
* **Azure Synapse Analytics**: Você usa um pool SQL como um armazenamento de dados *de pia.* Se você não tiver uma instância do Azure Synapse Analytics, consulte [Criar um pool SQL](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter etapas para criar uma.

## <a name="create-linked-services"></a>Criar serviços vinculados

No Azure Synapse Analytics, um serviço vinculado é onde você define suas informações de conexão para outros serviços. Nesta seção, você adicionará um serviço vinculado ao Azure Synapse Analytics e ao Azure Data Lake Storage Gen2.

1. Abra o Ux do Azure Synapse Analytics e vá para a guia **Gerenciar.**
1. Em **conexões externas,** selecione **serviços vinculados**.
1. Para adicionar um serviço vinculado, clique em **Novo**.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e clique em **Continuar**.
1. Digite suas credenciais de autenticação. A chave da conta, o principal de serviço e a identidade gerenciada são atualmente tipos de autenticação suportados. Clique em conexão de teste para verificar se suas credenciais estão corretas. Clique em **Criar** quando terminar.
1. Repita as etapas 3-5, mas em vez de Azure Data Lake Storage Gen2, selecione o bloco Azure Synapse Analytics e digite as credenciais de conexão correspondentes. Para o Azure Synapse Analytics, a autenticação SQL, a identidade gerenciada e o principal de serviço são suportados no momento.

## <a name="create-pipeline"></a>Criar um pipeline

Um gasoduto contém o fluxo lógico para uma execução de um conjunto de atividades. Nesta seção, você criará um pipeline contendo uma atividade de cópia que ingere dados do ADLS gen 2 em um pool SQL.

1. Vá para a guia **Orchestrate.** Clique no ícone de adição ao lado do cabeçalho de pipelines e selecione **Pipeline**.
1. Em **Mover e Transformar** no painel de atividades, arraste os dados de **Copiar** para a tela do pipeline.
1. Clique na atividade de cópia e vá para a guia **Origem.** Clique **em Novo** para criar um novo conjunto de dados de origem.
1. Selecione Azure Data Lake Storage gen2 como seu armazenamento de dados e clique em continuar.
1. Selecione DelimitedText como seu formato e clique em continuar.
1. No painel propriedades definidas, selecione o serviço vinculado ao ADLS criado. Especifique o caminho do arquivo dos dados de origem e especifique se a primeira linha tem um cabeçalho. Você pode importar o esquema do armazenamento de arquivos ou de um arquivo de amostra. Clique em OK quando terminar.
1. Vá para a guia **'Sink'.** Clique em **Novo** para criar um novo conjunto de dados do sink.
1. Selecione Azure Synapse Analytics como seu armazenamento de dados e clique em continuar.
1. No painel propriedades definidas, selecione o serviço vinculado ao Azure Synapse Analytics criado. Se você estiver escrevendo para uma tabela existente, selecione-a a partir da estada. Caso contrário, verifique **Editar** e digite seu novo nome de tabela. Clique em OK após a conclusão
1. Se você estiver criando uma tabela, habilite **a tabela Criar automaticamente** no campo de opção de tabela.

## <a name="debug-and-publish-pipeline"></a>Depurar e publicar pipeline

Depois de terminar de configurar seu pipeline, você pode executar uma execução de depuração antes de publicar seus artefatos para verificar se tudo está correto.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Você verá o status da execução do pipeline na guia **Saída** na parte inferior da janela. 
1. Depois que o pipeline for executado corretamente, na barra de ferramentas superior, selecione **Publicar Tudo**. Esta ação publica entidades (conjuntos de dados e pipelines) que você criou para o serviço Synapse Analytics.
1. Aguarde até que você veja a mensagem **Publicado com êxito**. Para ver as mensagens de notificação, clique no botão do sino no canto superior direito. 


## <a name="trigger-and-monitor-the-pipeline"></a>Acione e monitore o gasoduto

Nesta etapa, você aciona manualmente o pipeline publicado na etapa anterior. 

1. Selecione **Adicionar gatilho** na barra de ferramentas e selecione **Disparar Agora**. Na página **Execução de Pipeline**, selecione **Concluir**.  
1. Vá para a guia **Monitor** localizada na barra lateral esquerda. Você verá uma execução do pipeline que é disparada por um gatilho manual. Você pode usar links na coluna **Ações** para visualizar detalhes da atividade e reexecutar o pipeline.
1. Selecione o link **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada à execução do pipeline. Neste exemplo, há apenas uma atividade, então você vê apenas uma entrada na lista. Para obter detalhes sobre a operação da cópia, selecione o link **Detalhes** (ícone dos óculos) na coluna **Ações.** Para voltar ao modo de exibição Execuções de Pipeline, selecione **Execuções de Pipeline** na parte superior. Para atualizar a exibição, selecione **Atualizar**.
1. Verifique se seus dados estão corretamente escritos no pool SQL.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a integração de dados para o Synapse Analytics, consulte os dados de ingestão no artigo [Azure Data Lake Storage Gen2.](data-integration-data-lake.md)
