---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522814"
---
## <a name="select-an-ingestion-type"></a>Selecionar um tipo de ingestão

Em **Tipo de ingestão**, selecione uma das seguintes opções:
   * **do armazenamento**: no campo **Link para armazenamento**, adicione a URL da conta de armazenamento. Use [URL de SAS do blob](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para as contas de armazenamento particulares.
   
      ![Ingestão com um clique do armazenamento](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **de arquivo**: selecione **Procurar** para localizar o arquivo ou arraste o arquivo para o campo.
  
      ![Ingestão com um clique do arquivo](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **de contêiner**: no campo **Link para armazenamento**, adicione a URL de [SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) do contêiner e, opcionalmente, insira o tamanho da amostra.

      ![Ingestão com um clique do contêiner](media/data-explorer-one-click-ingestion-types/from-container.png)

  Uma amostra dos dados é exibida. Se desejar, você poderá filtrá-lo para mostrar apenas os arquivos que começam e terminam com caracteres específicos. Quando você ajusta os filtros, a visualização é atualizada automaticamente.
  
  Por exemplo, você pode filtrar todos os arquivos que começam com a palavra *dados* e terminam com uma extensão *.csv.gz*.

  ![Filtro da ingestão com um clique](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
