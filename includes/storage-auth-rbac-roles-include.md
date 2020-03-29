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
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279512"
---
O Azure fornece as seguintes funções de RBAC incorporadas para autorizar o acesso a dados de blob e fila usando Azure AD e OAuth:

- [Armazenamento Blob Data Owner](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso POSIX para o Azure Data Lake Storage Gen2. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Armazenamento Blob Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão aos recursos de armazenamento Blob.
- [Armazenamento Blob Data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente de leitura aos recursos de armazenamento Blob.
- [Contribuinte de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão para filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente de leitura para filas do Azure.
- [Processador de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use para conceder permissões de peek, recuperar e excluir mensagens em filas de armazenamento do Azure.
- [Remetente de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Use para conceder permissões adicionais às mensagens nas filas de armazenamento do Azure.

> [!NOTE]
> As atribuições de função RBAC podem levar até cinco minutos para se propagar.
>
> Apenas funções explicitamente definidas para acesso a dados permitem que um princípio de segurança acesse dados de blob ou fila. Funções como **Proprietário,** **Contribuinte**e **Contribuinte de Conta de Armazenamento** permitem que um princípio de segurança gerencie uma conta de armazenamento, mas não forneça acesso aos dados de blob ou fila dentro dessa conta.
>
> O acesso a dados de blob ou fila no portal DoZure pode ser autorizado usando sua conta Azure AD ou a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [Use o portal Azure para acessar dados de blob ou fila](../articles/storage/common/storage-access-blobs-queues-portal.md).
