---
title: Como autorizar operações de dados
titleSuffix: Azure Storage
description: Conheça as diferentes maneiras de autorizar o acesso ao Azure Storage, incluindo o Azure Active Directory, a autorização de chave compartilhada ou assinaturas de acesso compartilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8db6140789a9e4f46e368b913cacacd21609ccd9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529715"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizando o acesso a dados no Azure Storage

Sempre que você acessar os dados na conta de armazenamento, seu cliente fará uma solicitação sobre HTTP/HTTPS para Armazenamento do Microsoft Azure. Toda solicitação para um recurso seguro deve ser autorizada para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados.

A tabela a seguir descreve as opções que o Azure Storage oferece para autorizar o acesso aos recursos:

|  |Chave compartilhada (chave da conta de armazenamento)  |Assinatura de acesso compartilhado (SAS)  |Active Directory do Azure (Azure AD)  |Serviços de domínio de diretório ativo no local (visualização) |Acesso de leitura pública anônima  |
|---------|---------|---------|---------|---------|---------|
|Blobs do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |[Com suporte](storage-auth-aad.md)         |Sem suporte|[Com suporte](../blobs/storage-manage-access-to-resources.md)         |
|Arquivos Azure (SMB)     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |Sem suporte         |[Suportado, apenas com serviços de domínio AAD](../files/storage-files-active-directory-overview.md)         |[Suportadas, as credenciais devem ser sincronizadas com o Azure AD](../files/storage-files-active-directory-overview.md)|Sem suporte         |
|Arquivos Azure (REST)     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |Sem suporte         |Sem suporte |Sem suporte         |
|Filas do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |[Com suporte](storage-auth-aad.md)         |Sem suporte | Sem suporte         |
|Tabelas do Azure     |[Com suporte](/rest/api/storageservices/authorize-with-shared-key/)         |[Com suporte](storage-sas-overview.md)         |Sem suporte         |Sem suporte| Sem suporte         |

Cada opção de autorização é descrita brevemente abaixo:

- **Integração do Azure Active Directory (Azure AD)** para blobs e filas. O Azure AD fornece rbac (Role-based Access Control, controle sobre o acesso de um cliente a recursos em uma conta de armazenamento). Para obter mais informações sobre a integração do Azure AD para blobs e filas, consulte [Autorizar o acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).

- **Autenticação do Azure Active Directory Domain Services (Azure AD DS)** para arquivos Azure. O Azure Files suporta autorização baseada em identidade sobre o SMB (Server Message Block, bloco de mensagens do servidor) através do Azure AD DS. Você pode usar o RBAC para um controle fino sobre o acesso de um cliente aos recursos do Azure Files em uma conta de armazenamento. Para obter mais informações sobre a autenticação de arquivos do Azure usando serviços de domínio, consulte a [visão geral](../files/storage-files-active-directory-overview.md).

- **Autenticação (visualização) de Serviços de Domínio de Diretório Ativo (AD DS) no local ou autenticação aD DS no local** para arquivos Azure. O Azure Files suporta autorização baseada em identidade sobre SMB através do AD DS. Seu ambiente AD DS pode ser hospedado em máquinas locais ou em VMs azure. O acesso smb a arquivos é suportado usando credenciais AD DS de máquinas de domínio, no local ou no Azure. Você pode usar uma combinação de RBAC para controle de acesso de nível de compartilhamento e DACLs NTFS para aplicação de permissão de nível de diretório/arquivo. Para obter mais informações sobre a autenticação de arquivos do Azure usando serviços de domínio, consulte a [visão geral](../files/storage-files-active-directory-overview.md).

- **Autorização de chave compartilhada** para blobs, arquivos, filas e tabelas. Um cliente usando a Chave Compartilhada passa um cabeçalho com cada solicitação assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key/).
- **Assinaturas de acesso compartilhado** para blobs, arquivos, filas e tabelas. SAS (assinaturas de acesso compartilhado) fornecem acesso delegado limitado a recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões concedidas fornecerá flexibilidade no gerenciamento de acesso. Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](storage-sas-overview.md) para saber mais.
- **Acesso de leitura pública anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../blobs/storage-manage-access-to-resources.md).  

Por padrão, todos os recursos no Armazenamento do Microsoft Azure são protegidos e estão disponíveis apenas para o proprietário da conta. Embora seja possível usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes o acesso a recursos na conta de armazenamento, a Microsoft recomenda o uso do Azure AD quando possível para obter máxima segurança e facilidade de uso.

## <a name="next-steps"></a>Próximas etapas

- [Autorize o acesso a blobs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md)
- [Autorizar com Chave Compartilhada](/rest/api/storageservices/authorize-with-shared-key/)
- [Conceda acesso limitado aos recursos do Azure Storage usando assinaturas de acesso compartilhado (SAS)](storage-sas-overview.md)
