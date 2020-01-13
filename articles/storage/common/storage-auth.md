---
title: Como autorizar operações de dados
titleSuffix: Azure Storage
description: Saiba mais sobre as diferentes maneiras de autorizar o acesso ao armazenamento do Azure, incluindo Azure Active Directory, autorização de chave compartilhada ou SAS (assinaturas de acesso compartilhado).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 783e8666e2602f9251d81e976a27fbce099defa2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460523"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizando o acesso aos dados no armazenamento do Azure

Sempre que você acessar os dados na conta de armazenamento, seu cliente fará uma solicitação sobre HTTP/HTTPS para Armazenamento do Microsoft Azure. Toda solicitação para um recurso seguro deve ser autorizada para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados.

A tabela a seguir descreve as opções que o armazenamento do Azure oferece para autorizar o acesso a recursos:

|  |Chave compartilhada (chave da conta de armazenamento)  |Assinatura de acesso compartilhado (SAS)  |Active Directory do Azure (Azure AD)  |Acesso de leitura público anônimo  |
|---------|---------|---------|---------|---------|
|Blobs do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |[Com suporte](storage-auth-aad.md)         |[Com suporte](../blobs/storage-manage-access-to-resources.md)         |
|Arquivos do Azure (SMB)     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |Sem suporte         |[Com suporte, somente com os serviços de domínio do AAD](../files/storage-files-active-directory-overview.md)         |Sem suporte         |
|Arquivos do Azure (REST)     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |Sem suporte         |Sem suporte         |
|Filas do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |[Com suporte](storage-auth-aad.md)         |Sem suporte         |
|Tabelas do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |Sem suporte         |Sem suporte         |

Cada opção de autorização é descrita brevemente abaixo:

- **Integração do Azure Active Directory (Azure AD)** para BLOBs e filas. O Azure AD fornece RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente a recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do Azure AD para BLOBs e filas, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

- **Integração de Azure AD Domain Services (DS) (versão prévia)** para arquivos. Os arquivos do Azure oferecem suporte à autorização baseada em identidade no protocolo SMB por meio do Azure AD DS. Você pode usar o RBAC para um controle refinado sobre o acesso de um cliente aos recursos de arquivos do Azure em uma conta de armazenamento. Para obter mais informações sobre a integração do AD do Azure para arquivos usando os serviços de domínio, consulte [visão geral do suporte à autenticação do AAD DS (serviço de domínio Azure Active Directory de arquivos do Azure) para acesso SMB (versão prévia)](../files/storage-files-active-directory-overview.md).

- **Autorização de chave compartilhada** para blobs, arquivos, filas e tabelas. Um cliente usando a Chave Compartilhada passa um cabeçalho com cada solicitação assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key/).
- **Assinaturas de acesso compartilhado** para blobs, arquivos, filas e tabelas. SAS (assinaturas de acesso compartilhado) fornecem acesso delegado limitado a recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões concedidas fornecerá flexibilidade no gerenciamento de acesso. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-sas-overview.md) para saber mais.
- **Acesso de leitura pública anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).  

Por padrão, todos os recursos no Armazenamento do Microsoft Azure são protegidos e estão disponíveis apenas para o proprietário da conta. Embora seja possível usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes o acesso a recursos na conta de armazenamento, a Microsoft recomenda o uso do Azure AD quando possível para obter máxima segurança e facilidade de uso.

## <a name="next-steps"></a>Próximos passos

- [Autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md)
- [Autorizar com chave compartilhada](/rest/api/storageservices/authorize-with-shared-key/)
- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
