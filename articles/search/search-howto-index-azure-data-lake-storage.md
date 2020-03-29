---
title: Pesquisar sobre o Azure Data Lake Storage Gen2 (visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como indexar conteúdo e metadados no Azure Data Lake Storage Gen2. Este recurso está atualmente em visualização pública
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905651"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexação de documentos no Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> O suporte ao Azure Data Lake Storage Gen2 está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Você pode solicitar acesso às visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Atualmente, não há suporte a portal ou .NET SDK.


Ao configurar uma conta de armazenamento Azure, você tem a opção de ativar [namespace hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Isso permite que a coleta de conteúdo em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados. Ao ativar o namespace hierárquico, você habilita [o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Este artigo descreve como começar com documentos de indexação que estão no Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurar o indexador Azure Data Lake Storage Gen2

Existem alguns passos que você precisará concluir para indexar o conteúdo do Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Passo 1: Inscreva-se para a pré-visualização

Inscreva-se na visualização do indexador Data Lake Storage Gen2 preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um e-mail de confirmação assim que tiver sido aceito na pré-visualização.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Passo 2: Siga as etapas de configuração de indexação de armazenamento DoZure Blob

Depois de receber a confirmação de que sua inscrição de pré-visualização foi bem sucedida, você está pronto para criar o pipeline de indexação.

Você pode indexar conteúdo e metadados do Data Lake Storage Gen2 usando a [versão 2019-05-06-Preview](search-api-preview.md)da API REST ( Não há suporte a portal ou .NET SDK no momento.

A indexação do conteúdo no Data Lake Storage Gen2 é idêntica ao conteúdo de indexação no armazenamento Azure Blob. Então, para entender como configurar a fonte de dados, índice e indexador data lake storage Gen2, consulte [Como indexar documentos no Azure Blob Storage com a Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). O artigo de armazenamento Blob também fornece informações sobre quais formatos de documento são suportados, quais propriedades de metadados blob são extraídas, indexação incremental e muito mais. Essas informações serão as mesmas para data lake storage Gen2.

## <a name="access-control"></a>Controle de acesso

O Azure Data Lake Storage Gen2 implementa um modelo de controle de [acesso](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) que suporta tanto o RBAC (Control de acesso baseado em função) do Azure quanto as listas de controle de acesso semelhantes ao POSIX (ACLs). Ao indexar o conteúdo do Data Lake Storage Gen2, o Azure Cognitive Search não extrairá as informações de RBAC e ACL do conteúdo. Como resultado, essas informações não serão incluídas no índice de Pesquisa Cognitiva do Azure.

Se a manutenção do controle de acesso em cada documento no índice for importante, cabe ao desenvolvedor do aplicativo implementar o [corte de segurança](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Detecção de alterações

O indexador Data Lake Storage Gen2 suporta detecção de alterações. Isso significa que, quando o indexador é executado, ele apenas reindexa as bolhas alteradas, conforme determinado pelo carimbo de tempo da `LastModified` bolha.

> [!NOTE] 
> Data Lake Storage Gen2 permite que os diretórios sejam renomeados. Quando um diretório é renomeado os carimbos de tempo para os blobs nesse diretório não são atualizados. Como resultado, o indexador não reindexará essas bolhas. Se você precisar que as bolhas em um diretório sejam reindexadas após um renomeação de diretório, `LastModified` porque eles agora têm novos URLs, você precisará atualizar o carimbo de tempo para todas as bolhas no diretório para que o indexador saiba reindexá-los durante uma execução futura.
