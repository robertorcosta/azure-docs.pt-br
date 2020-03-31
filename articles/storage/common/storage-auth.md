---
title: Como autorizar operações de dados
titleSuffix: Azure Storage
description: Conheça as diferentes maneiras de autorizar o acesso ao Azure Storage, incluindo o Azure Active Directory, a autorização de chave compartilhada ou assinaturas de acesso compartilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b0f2ad7566d0204871a9c6441315d6201662d92b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616300"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizando o acesso a dados no Azure Storage

Sempre que você acessar os dados na conta de armazenamento, seu cliente fará uma solicitação sobre HTTP/HTTPS para Armazenamento do Microsoft Azure. Toda solicitação para um recurso seguro deve ser autorizada para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados.

A tabela a seguir descreve as opções que o Azure Storage oferece para autorizar o acesso aos recursos:

|  |Chave compartilhada (chave da conta de armazenamento)  |Assinatura de acesso compartilhado (SAS)  |Active Directory do Azure (Azure AD)  |Diretório Ativo (visualização) |Acesso de leitura pública anônima  |
|---------|---------|---------|---------|---------|---------|
|Blobs do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |[Suportado](storage-auth-aad.md)         |Sem suporte|[Suportado](../blobs/storage-manage-access-to-resources.md)         |
|Arquivos Azure (SMB)     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |Sem suporte         |[Suportado, apenas com serviços de domínio AAD](../files/storage-files-active-directory-overview.md)         |[Suportadas, as credenciais devem ser sincronizadas com o Azure AD](../files/storage-files-active-directory-overview.md)|Sem suporte         |
|Arquivos Azure (REST)     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |Sem suporte         |Sem suporte |Sem suporte         |
|Filas do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |[Suportado](storage-auth-aad.md)         |Sem suporte | Sem suporte         |
|Tabelas do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |Sem suporte         |Sem suporte| Sem suporte         |

Cada opção de autorização é descrita brevemente abaixo:

- **Integração do Azure Active Directory (Azure AD)** para blobs e filas. O Azure AD fornece RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente a recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do Azure AD para blobs e filas, consulte [Autorizar o acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

- **Autenticação do Azure Active Directory Domain Services (Azure AD DS)** para arquivos Azure. O Azure Files suporta autorização baseada em identidade sobre o SMB (Server Message Block, bloco de mensagens do servidor) através do Azure AD DS. Você pode usar o RBAC para um controle fino sobre o acesso de um cliente aos recursos do Azure Files em uma conta de armazenamento. Para obter mais informações sobre a autenticação de arquivos do Azure usando serviços de domínio, consulte nossa [visão geral](../files/storage-files-active-directory-overview.md).

- **Autenticação (AD) active directory (visualização)** para arquivos Azure. O Azure Files suporta autorização baseada em identidade sobre SMB através de AD. Seu serviço de domínio AD pode ser hospedado em máquinas locais ou em VMs do Azure. O acesso smb a arquivos é suportado usando credenciais AD de máquinas de domínio, seja no local ou no Azure. Você pode usar o RBAC para controle de acesso ao nível de compartilhamento e DACLs NTFS para a aplicação da permissão de nível de diretório/arquivo. Para obter mais informações sobre a autenticação de arquivos do Azure usando serviços de domínio, consulte nossa [visão geral](../files/storage-files-active-directory-overview.md).

- **Autorização de chave compartilhada** para blobs, arquivos, filas e tabelas. Um cliente usando a Chave Compartilhada passa um cabeçalho com cada solicitação assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key/).
- **Assinaturas de acesso compartilhado** para blobs, arquivos, filas e tabelas. SAS (assinaturas de acesso compartilhado) fornecem acesso delegado limitado a recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões concedidas fornecerá flexibilidade no gerenciamento de acesso. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-sas-overview.md) para saber mais.
- **Acesso de leitura pública anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).  

Por padrão, todos os recursos no Armazenamento do Microsoft Azure são protegidos e estão disponíveis apenas para o proprietário da conta. Embora seja possível usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes o acesso a recursos na conta de armazenamento, a Microsoft recomenda o uso do Azure AD quando possível para obter máxima segurança e facilidade de uso.

## <a name="next-steps"></a>Próximas etapas

- [Autorize o acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md)
- [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key/)
- [Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](storage-sas-overview.md)
