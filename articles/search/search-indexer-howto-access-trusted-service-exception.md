---
title: Permitir o acesso ao armazenamento por meio de exceção de serviço confiável
titleSuffix: Azure Cognitive Search
description: Guia de instruções que descreve como configurar a exceção de serviço confiável para acessar dados de contas de armazenamento com segurança.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: f901833caf0623de643e0372c53658fa7da8c8be
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463400"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Acessando dados em contas de armazenamento com segurança por meio de exceção de serviço confiável

Os indexadores que acessam dados em contas de armazenamento podem usar a funcionalidade de [exceção de serviço confiável](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para acessar dados com segurança. Esse mecanismo oferece aos clientes que não conseguem conceder [acesso ao indexador por meio de regras de firewall IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para acessar dados em contas de armazenamento.

> [!NOTE]
> O acesso à conta de armazenamento por meio da identidade atribuída do sistema de um serviço de pesquisa ainda está em versão prévia. Esse recurso de visualização é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.

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
