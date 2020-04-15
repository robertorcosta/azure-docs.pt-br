---
title: Usar a ingestão com um clique para ingerir dados no Azure Data Explorer
description: Visão geral da ingestão (carregamento) de dados em no Azure Data Explorer apenas usando a ingestão com um clique.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521566"
---
# <a name="what-is-one-click-ingestion"></a>O que é a ingestão com um clique? 

A ingestão com um clique permite ingerir dados rapidamente e sugerir automaticamente tabelas e estruturas de mapeamento com base em uma fonte de dados no Azure Data Explorer. 

Usando a interface do usuário da Web do Azure Data Explorer, você pode ingerir dados do armazenamento (arquivo de blob), de um arquivo local ou de um contêiner (até 10 mil blobs). Você também pode definir uma grade de eventos em um contêiner para ingestão contínua. Os dados podem ser ingeridos em uma tabela nova ou existente em JSON, CSV e [outros formatos](#file-formats). A ingestão com um clique pode sugerir uma estrutura para uma nova tabela e um mapeamento de tabela, com base na fonte de dados, além de fornecer uma plataforma intuitiva para ajustar a estrutura de uma tabela e um mapeamento de tabela existentes. A ingestão com um clique vai ingerir os dados na tabela em apenas alguns minutos.

A ingestão com um clique é particularmente útil ao ingerir dados pela primeira vez ou quando o esquema dos dados não é conhecido para você.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Crie [um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md).
* Entre na [interface do usuário da Web do Azure Data Explorer](https://dataexplorer.azure.com/) e [adicione uma conexão ao cluster](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Formatos de arquivo

A ingestão com um clique dá suporte à ingestão de uma nova tabela com base nos dados de origem em um dos seguintes formatos:
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>Assistente da ingestão com um clique

O assistente da ingestão com um clique orientará você pelo processo da ingestão com um clique. 

> [!Note]
> Esta seção descreve o assistente em geral. As opções selecionadas dependem do fato de a ingestão estar sendo feita em uma tabela nova ou existente. Para obter mais informações, consulte:
    > * Ingestão em [uma nova tabela](one-click-ingestion-new-table.md)
    > * Ingestão em uma [tabela existente](one-click-ingestion-existing-table.md) 
    
1. Para acessar o assistente, clique com o botão direito do mouse na linha de *banco de dados* ou de *tabela* no menu à esquerda da interface do usuário da Web do Azure Data Explorer e selecione **Ingerir novos dados (versão prévia)** .

    ![Selecionar a ingestão com um clique na interface do usuário da Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. O assistente fornece instruções para as seguintes opções:
       * Ingestão em uma [tabela existente](one-click-ingestion-existing-table.md)
       * Ingestão em [uma nova tabela](one-click-ingestion-new-table.md)
       * Ingerir dados de: * Armazenamento de Blobs * Um arquivo local * Um contêiner
       * Insira o tamanho da amostra de 1 a 10 mil linhas (somente do contêiner)
       
1. Depois que você selecionar a fonte de dados com êxito, uma visualização dos dados será exibida. 
    Se você estiver ingerindo dados de um contêiner, filtre os dados para que somente os arquivos com extensões de arquivo ou prefixos específicos sejam ingeridos. Por exemplo, talvez você queira apenas ingerir arquivos com nomes de arquivo que comecem com a palavra *Europa* ou apenas arquivos com a extensão *.json*. 

1. Clique em **Editar esquema**. Se você estiver ingerindo dados em uma tabela específica, mapeie as colunas de origem para as colunas de destino e decida se deseja ou não incluir os nomes das colunas.

1. Inicie o processo de ingestão de dados.

> [!Note]
> Se a fonte de dados for um contêiner, observe que a política de agregação de ingestão de dados (envio em lote) do Azure Data Explorer foi projetada para otimizar o processo de ingestão. Por padrão, a política é configurada para 5 minutos ou 500 MB de dados e, portanto, poderá haver latência. Confira [Política de envio em lote](/azure/kusto/concepts/batchingpolicy) para obter as opções de agregação. Ao ingerir dados de outras fontes, a ingestão terá efeito imediato.

## <a name="next-steps"></a>Próximas etapas

* Decida se você usará a ingestão com um clique para ingerir dados em [uma tabela existente](one-click-ingestion-existing-table.md) ou em [uma nova tabela](one-click-ingestion-new-table.md)
* [Consultar dados na interface do usuário da Web do Azure Data Explorer](/azure/data-explorer/web-query-data)
* [Escrever consultas para o Azure Data Explorer usando a linguagem de consulta Kusto](/azure/data-explorer/write-queries)