---
title: Permitir o acesso ao armazenamento por meio de exceção de serviço confiável
titleSuffix: Azure Cognitive Search
description: Guia de instruções que descreve como configurar a exceção de serviço confiável para acessar dados de contas de armazenamento com segurança.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320465"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Acessando dados em contas de armazenamento com segurança por meio de exceção de serviço confiável

Os indexadores que acessam dados em contas de armazenamento podem usar a funcionalidade de [exceção de serviço confiável](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para acessar dados com segurança. Esse mecanismo oferece aos clientes que não conseguem conceder [acesso ao indexador por meio de regras de firewall IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para acessar dados em contas de armazenamento.

> [!NOTE]
> O suporte para acessar dados em contas de armazenamento por meio de uma exceção de serviço confiável é limitado ao armazenamento de BLOBs do Azure e Azure Data Lake armazenamento de Gen2. Não há suporte para o armazenamento de tabelas do Azure.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Etapa 1: configurar a conexão com a conta de armazenamento por meio de identidade

Siga os detalhes descritos no guia de [acesso de identidade gerenciada](search-howto-managed-identities-storage.md) para configurar indexadores para acessar contas de armazenamento por meio da identidade gerenciada do serviço de pesquisa.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Etapa 2: permitir que os serviços confiáveis da Microsoft acessem a conta de armazenamento

Na portal do Azure, navegue até a guia "firewalls e redes virtuais" da conta de armazenamento. Verifique se a opção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" está marcada. Essa opção só permitirá a instância específica do serviço de pesquisa com acesso baseado em função apropriado para a conta de armazenamento (autenticação forte) para acessar dados na conta de armazenamento, mesmo que elas sejam protegidas por regras de firewall de IP.

![Exceção de serviço confiável](media\search-indexer-howto-secure-access\exception.png "Exceção de serviço confiável")

Os indexadores agora poderão acessar os dados na conta de armazenamento, mesmo se a conta estiver protegida por meio de regras de firewall de IP.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os indexadores de armazenamento do Azure:

- [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
- [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)
