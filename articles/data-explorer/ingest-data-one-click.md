---
title: Use a ingestão de um clique para ingerir dados no Azure Data Explorer
description: Saiba como ingerir dados (carregar) no Azure Data Explorer simplesmente usando a ingestão de um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444547"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Use a ingestão de um clique para ingerir dados no Azure Data Explorer

Este artigo mostra como usar a ingestão de um clique para a ingestão rápida de uma nova tabela nos formatos JSON ou CSV. Os dados podem ser ingeridos do armazenamento ou de um arquivo local em uma tabela existente ou em uma nova tabela. Use o assistente intuitivo de um clique e seus dados ingerem em poucos minutos. Em seguida, você pode editar a tabela e executar consultas usando a UI do Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Faça login [no aplicativo](https://dataexplorer.azure.com/).
* Crie [um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md).
* Faça login na [UI da Web e](https://dataexplorer.azure.com/) [adicione uma conexão ao seu cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Ingerir novos dados

1. Clique com o botão direito do mouse no *banco de dados* ou na linha de *tabela* no menu esquerdo da UI da Web e selecione **Ingest novos dados (Preview)**.

    ![Selecione a ingestão de um clique na UI da Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Na janela **'Inser',** selecione a guia '''''Visualizar', selecione a guia **''''Concluir's'** **Detalhes**do projeto :

    * Em **'Tabela ''' Para',** selecione um nome de tabela existente no menu suspenso ou selecione **Criar novo** para fazer uma nova tabela.
    * Para **o tipo Ingestion,** selecione **no armazenamento** ou **no arquivo**.
      * Se você for selecionado **a partir do armazenamento,** selecione **Link to storage** para adicionar a URL. Use [a URL Blob SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para contas de armazenamento privadas. 
      * Se você selecionou **no arquivo,** **selecione Procurar** e arraste o arquivo para dentro da caixa.
    * Selecione **Editar esquema** para exibir e editar a configuração da coluna de tabela.
 
    ![Detalhes da fonte de ingestão com um clique](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se você selecionar **'Visualizar novos dados ' ''** em uma linha *de tabela,.o* nome da tabela selecionado aparecerá nos **Detalhes**do projeto .

1. Se você selecionou uma tabela existente, a janela **Colunas mapa** será aberta para mapear colunas de dados de origem para colunas de tabela de destino. 
    * Use **a coluna Omite** para remover uma coluna de destino da tabela.
    * Use **a nova coluna** para adicionar uma nova coluna à tabela.

    ![Janela de colunas de mapa](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Na guia **Esquema:**

    * Selecione **O tipo de compactação** no menu suspenso e selecione **Descompactado** ou **GZip**.
    * Selecione o **formato De seleção** do menu suspenso e selecione **JSON,** **CSV,** **TSV,** **SCSV,** **SOHSV,** **TSVE**ou **PSV.** 
        * Ao selecionar o formato **JSON,** você também deve selecionar **os níveis JSON**, de 1 a 10. Os níveis afetam a representação de dados da coluna da tabela. 
        * Se você selecionar um formato diferente do JSON, você deve selecionar a caixa de seleção **Incluir nomes** de coluna para ignorar a linha de título do arquivo.
    * **O nome de mapeamento** é definido automaticamente, mas pode ser editado.
    * Se você selecionou uma tabela existente, você pode selecionar **colunas mapa** para abrir a janela **colunas Mapa.**

    ![Esquema de formato CSV de ingestão de um clique](media/ingest-data-one-click/one-click-csv-format.png)

1. Acima do painel **Editor,** selecione o botão **v** para abrir o editor. No editor, você pode visualizar e copiar as consultas automáticas geradas a partir de suas entradas. 

1. Na tabela: 
    * Clique com o botão direito do mouse em novos cabeçalhos de coluna para **alterar o tipo de dados,** **Renomear coluna,** **Excluir coluna,** **Classificar ascendente**ou **Classificar descendente**. Nas colunas existentes, apenas a classificação de dados está disponível. 
    * Clique duas vezes no novo nome da coluna para editar.

1. Selecione **Iniciar ingestão** para criar uma tabela e mapeamento e iniciar a ingestão de dados.

    ![Esquema de formato JSON de ingestão de um clique](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Consultar dados

1. Na janela **completa de ingestão de dados,** todas as três etapas serão marcadas com marcas de verificação verdes se a ingestão de dados for concluída com sucesso.
 
    ![Ingestão de dados com um clique concluída](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selecione o botão **v** para abrir a consulta. Copie para a ui da Web para editar a consulta.

1. O menu à direita contém **consultas rápidas** e opções **de ferramentas.** 

    * **Consultas rápidas** incluem links para a UI da Web com consultas de exemplo.
    * **As ferramentas** incluem um link para **comandos Drop** na UI da Web, `.drop` que permitem solucionar problemas executando os comandos relevantes.

    > [!TIP]
    > Você pode perder dados `.drop` quando usar comandos. Use-os com cuidado.

## <a name="next-steps"></a>Próximas etapas

* [Dados de consulta na UI do Azure Data Explorer](web-query-data.md)
* [Escrever consultas para o Azure Data Explorer usando o Kusto Query Language](write-queries.md)
