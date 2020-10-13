---
title: Cópia em massa de dados de arquivos para banco de dados
description: Saiba como usar um modelo de solução para copiar dados em massa do Azure Data Lake Storage Gen2 para o Azure Synapse Analytics/Banco de Dados SQL do Azure.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: c7f4cba10117efef4099b3524b49cae313593a9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442702"
---
# <a name="bulk-copy-from-files-to-database"></a>Cópia em massa de dados de arquivos para banco de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que pode ser usado para copiar dados em massa do Azure Data Lake Storage Gen2 para o Azure Synapse Analytics/Banco de Dados SQL do Azure.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo recupera arquivos originários do Azure Data Lake Storage Gen2. Em seguida, ele itera em cada arquivo na origem e copia o arquivo para o armazenamento de dados de destino. 

Atualmente, esse modelo dá suporte apenas à cópia de dados no formato **DelimitedText**. Arquivos em outros formatos de dados também podem ser recuperados do armazenamento de dados de origem, mas não podem ser copiados para o armazenamento de dados de destino.  

O modelo contém três atividades:
- A atividade **Obter Metadados** recupera arquivos do Azure Data Lake Storage Gen2 e os transmite para a atividade *ForEach* seguinte.
- A atividade **ForEach** obtém arquivos da atividade *Obter Metadados* e itera cada arquivo para a atividade *Copiar*.
- A atividade **Copiar** reside na atividade *ForEach* para copiar cada arquivo do armazenamento de dados de origem para o de destino.

O modelo define os dois seguintes parâmetros:
- *SourceContainer* é o caminho do contêiner raiz do qual os dados são copiados em seu Azure Data Lake Storage Gen2. 
- *SourceDirectory* é o caminho do diretório no contêiner raiz do qual os dados são copiados em seu Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá até o modelo **Cópia em massa dos arquivos para o banco de dados**. Crie uma **Conexão** com o armazenamento Gen2 de origem. Lembre-se de que "GetMetadataDataset" e "SourceDataset" são referências à mesma conexão do seu armazenamento de arquivos de origem.

    ![Criar uma conexão com o armazenamento de dados de origem](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Crie uma **Conexão** com o armazenamento de dados do coletor para o qual você está copiando dados.

    ![Criar uma conexão com o armazenamento de dados do coletor](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Você verá que um pipeline foi criado, conforme mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Se você escolheu o **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** como o destino de dados na **etapa 2** mencionado acima, você deve inserir uma conexão com o armazenamento de BLOBs do Azure para preparo, conforme exigido pelo polybase do Azure Synapse Analytics (anteriormente SQL data warehouse). Como mostra a captura de tela a seguir, o modelo gera automaticamente um *Caminho de armazenamento* para o Armazenamento de Blobs. Verifique se o contêiner foi criado após a execução do pipeline.
        
    ![Configuração do Polybase](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecione **Depurar**, insira os **Parâmetros** e, em seguida, selecione **Concluir**.

    ![Clique em **Depurar**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Quando a execução do pipeline for concluída com êxito, você verá resultados semelhantes ao exemplo a seguir:

    ![Revisar o resultado](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)