---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b49d757067d8ee888bee52f3931c8d48d786c044
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024871"
---
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso a dados de BLOB e de fila usando o Azure AD e o OAuth:

- [Proprietário de dados do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso POSIX para Azure data Lake Storage Gen2. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão aos recursos de armazenamento de BLOBs.
- [Leitor de dados de armazenamento de blob](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de BLOBs.
- [Delegante de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Obtenha uma chave de delegação de usuário para usar para criar uma assinatura de acesso compartilhado que é assinada com as credenciais do Azure ad para um contêiner ou BLOB.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura às filas do Azure.
- [Processador de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use para conceder permissões de inspeção, recuperação e exclusão a mensagens em filas do armazenamento do Azure.
- [Remetente da mensagem de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Use para conceder permissões de adição a mensagens em filas do armazenamento do Azure.

Somente as funções explicitamente definidas para acesso a dados permitem que uma entidade de segurança acesse dados de BLOB ou fila. Funções internas, como **proprietário**, **colaborador**e colaborador de **conta de armazenamento** , permitem que uma entidade de segurança gerencie uma conta de armazenamento, mas não forneça acesso aos dados de BLOB ou de fila dentro dessa conta por meio do Azure AD. No entanto, se uma função incluir a **ação Microsoft. Storage/storageAccounts/listKeys/**, um usuário ao qual essa função é atribuída poderá acessar os dados na conta de armazenamento por meio da autorização de chave compartilhada com as chaves de acesso da conta. Para obter mais informações, consulte [usar o portal do Azure para acessar dados de BLOB ou fila](../articles/storage/common/storage-access-blobs-queues-portal.md).

Para obter informações detalhadas sobre funções RBAC internas para o armazenamento do Azure para os serviços de dados e o serviço de gerenciamento, consulte a seção **armazenamento** em [funções internas do Azure para o RBAC do Azure](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte [funções de administrador de assinatura clássica, funções de RBAC do Azure e funções do Azure ad](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> As atribuições de função RBAC podem levar até cinco minutos para serem propagadas.
