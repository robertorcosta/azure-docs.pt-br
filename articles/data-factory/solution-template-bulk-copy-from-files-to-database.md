---
title: Cópia em massa de dados de arquivos para banco de dados
description: Aprenda a usar um modelo de solução para copiar dados em massa do Azure Data Lake Storage Gen2 para o Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 070b708f204006bc1ba90c4c3676696291fde902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414865"
---
# <a name="bulk-copy-from-files-to-database"></a>Cópia em massa de dados de arquivos para banco de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo de solução que você pode usar para copiar dados em massa do Azure Data Lake Storage Gen2 para o Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo recupera arquivos da fonte Azure Data Lake Storage Gen2. Em seguida, ele itera sobre cada arquivo na fonte e copia o arquivo para o armazenamento de dados de destino. 

Atualmente, este modelo só suporta copiar dados no formato **DelimitedText.** Arquivos em outros formatos de dados também podem ser recuperados do armazenamento de dados de origem, mas não podem ser copiados para o armazenamento de dados de destino.  

O modelo contém três atividades:
- **Obtenha a** atividade Metadados recupera arquivos do Azure Data Lake Storage Gen2 e os passa para atividades subsequentes *do ForEach.*
- **ForEach** atividade recebe arquivos da atividade *Obter metadados* e itera cada arquivo para a atividade *Copiar.*
- **A** atividade de cópia reside em *atividade forCada* para copiar cada arquivo do armazenamento de dados de origem para o armazenamento de dados de destino.

O modelo define os dois parâmetros a seguir:
- *SourceContainer* é o caminho do contêiner raiz do qual os dados são copiados no Azure Data Lake Storage Gen2. 
- *SourceDirectory* é o caminho do diretório sob o recipiente raiz onde os dados são copiados no Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o **modelo Cópia em massa de arquivos para banco de dados.** Crie uma **nova** conexão com a loja Gen2 de origem. Esteja ciente de que "GetMetadataDataset" e "SourceDataset" são referências à mesma conexão do seu armazenamento de arquivos de origem.

    ![Crie uma nova conexão com o armazenamento de dados de origem](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Crie uma **nova** conexão com o armazenamento de dados da pia para a a que você está copiando dados.

    ![Crie uma nova conexão com o armazenamento de dados da pia](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Você veria um pipeline criado como mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se você escolheu **o Azure Synapse Analytics (anteriormente SQL DW)** como o destino dos dados na **etapa 2** mencionada acima, você deve inserir uma conexão com o armazenamento Azure Blob para estágio, conforme exigido pelo SQL Data Warehouse Polybase. Como mostra a captura de tela a seguir, o modelo gerará automaticamente um *caminho de armazenamento* para o armazenamento blob. Verifique se o contêiner foi criado após o tráfego do gasoduto.
        
    ![Configuração do Polybase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando a execução do pipeline for concluída com sucesso, você verá resultados semelhantes ao seguinte exemplo:

    ![Revisar o resultado](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)