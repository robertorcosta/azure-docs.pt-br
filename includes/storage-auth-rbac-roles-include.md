---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519559"
---
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso a dados de BLOB e de fila usando o Azure AD e o OAuth:

- [Proprietário de dados do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso POSIX para Azure data Lake Storage Gen2. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão aos recursos de armazenamento de BLOBs.
- [Leitor de dados de armazenamento de blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de BLOBs.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura às filas do Azure.
- [Processador de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use para conceder permissões de inspeção, recuperação e exclusão a mensagens em filas do armazenamento do Azure.
- [Remetente da mensagem de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Use para conceder permissões de adição a mensagens em filas do armazenamento do Azure.

Para obter informações detalhadas sobre funções RBAC internas para o armazenamento do Azure para os serviços de dados e o serviço de gerenciamento, consulte a seção **armazenamento** em [funções internas do Azure para o RBAC do Azure](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte [funções de administrador de assinatura clássica, funções de RBAC do Azure e funções do Azure ad](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> As atribuições de função RBAC podem levar até cinco minutos para serem propagadas.
>
> Somente as funções explicitamente definidas para acesso a dados permitem que uma entidade de segurança acesse dados de BLOB ou fila. Funções como **proprietário**, **colaborador**e colaborador de **conta de armazenamento** permitem que uma entidade de segurança gerencie uma conta de armazenamento, mas não forneça acesso aos dados de BLOB ou de fila nessa conta.
>
> O acesso a dados de BLOB ou de fila no portal do Azure pode ser autorizado usando sua conta do Azure AD ou a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [usar o portal do Azure para acessar dados de BLOB ou fila](../articles/storage/common/storage-access-blobs-queues-portal.md).
