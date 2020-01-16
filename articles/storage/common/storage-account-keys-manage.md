---
title: Gerenciar as chaves de acesso da conta
titleSuffix: Azure Storage
description: Saiba como exibir, gerenciar e girar suas chaves de acesso da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975778"
---
# <a name="manage-storage-account-access-keys"></a>Gerenciar chaves de acesso da conta de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de conta de armazenamento de 512 bits. Essas chaves podem ser usadas para autorizar o acesso a dados em sua conta de armazenamento por meio da autorização de chave compartilhada.

A Microsoft recomenda que você use Azure Key Vault para gerenciar suas chaves de acesso e que você regularmente gira e regenera suas chaves. O uso de Azure Key Vault facilita a rotação de suas chaves sem interrupções em seus aplicativos. Você também pode girar manualmente suas chaves.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Exibir chaves de acesso e cadeia de conexão

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Usar Azure Key Vault para gerenciar suas chaves de acesso

A Microsoft recomenda o uso de Azure Key Vault para gerenciar e girar suas chaves de acesso. Seu aplicativo pode acessar com segurança suas chaves no Key Vault, para que você possa evitar armazená-las com o código do aplicativo. Para obter mais informações sobre como usar Key Vault para o gerenciamento de chaves, consulte os seguintes artigos:

- [Gerenciar chaves de conta de armazenamento com o Azure Key Vault e o PowerShell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Gerenciar chaves de conta de armazenamento com Azure Key Vault e o CLI do Azure](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Girar manualmente as chaves de acesso

A Microsoft recomenda que você gire suas chaves de acesso periodicamente para ajudar a manter sua conta de armazenamento segura. Se possível, use Azure Key Vault para gerenciar suas chaves de acesso. Se você não estiver usando Key Vault, será necessário girar suas chaves manualmente.

Duas chaves de acesso são atribuídas para que você possa girar as chaves. Ter duas chaves garante que seu aplicativo Mantenha o acesso ao armazenamento do Azure durante todo o processo.

> [!WARNING]
> A regeneração das chaves de acesso pode afetar todos os aplicativos ou serviços do Azure que dependem da chave de conta de armazenamento. Todos os clientes que usam a chave de conta para acessar a conta de armazenamento devem ser atualizados para usar a nova chave, incluindo os serviços de mídia, nuvem, aplicativos para área de trabalho e móveis e aplicativos de interface gráfica do usuário para o Armazenamento do Azure, tais como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

Siga este processo para girar as chaves da conta de armazenamento:

1. Atualize as cadeias de conexão no código do aplicativo para usar a chave secundária.
2. Regenere a chave de acesso primária de sua conta de armazenamento. Na folha **Chaves de Acesso** no portal do Azure, clique em **Regenerar Chave1** e clique em **Sim** para confirmar que você deseja gerar uma nova chave.
3. Atualize as cadeias de conexão em seu código para fazer referência à nova chave de acesso primária.
4. Regenere a chave de acesso secundária da mesma maneira.

> [!NOTE]
> A Microsoft recomenda usar apenas uma das chaves em todos os aplicativos ao mesmo tempo. Se você usar a Chave 1 em alguns lugares e a Chave 2 em outros, nãos será possível alternar as chaves sem que algum aplicativo perca o acesso.

## <a name="next-steps"></a>Próximos passos

- [Visão geral da conta de armazenamento do Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
