---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521272"
---
Para visualizar e copiar as chaves de acesso da conta de armazenamento ou a seqüência de conexões do portal Azure:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Em **Configurações,** selecione **Teclas de acesso**. Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Chave** em **key1** e clique no botão **Copiar** para copiar a chave de conta.
5. Como alternativa, você pode copiar a cadeia de conexão inteira. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão.

    ![Captura de tela mostrando como visualizar chaves de acesso no portal Azure](media/storage-view-keys-include/portal-connection-string.png)

Você pode usar qualquer chave para acessar o Azure Storage, mas em geral é uma boa prática usar a primeira chave e reservar o uso da segunda chave para quando você estiver girando as chaves.

Para visualizar ou ler as chaves de acesso de uma conta, o usuário deve ser um administrador de serviçoou deve ser atribuído a uma função RBAC que inclua o **Microsoft.Storage/storageAccounts/listkeys/action**. Algumas funções de RBAC incorporadas que incluem essa ação são as funções de função de serviço do operador de usuário **proprietário,** **contribuinte**e **de conta de armazenamento.** Para obter mais informações sobre a função administrador de serviços, consulte [funções clássicas de administrador de assinatura, funções Do Azure RBAC e Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre funções incorporadas para armazenamento azure, consulte a seção **Armazenamento** em [funções incorporadas do Azure para o Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage).
