---
title: Usar chaves gerenciadas pelo cliente com Azure Key Vault para gerenciar a criptografia de conta
titleSuffix: Azure Storage
description: Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d53818c91d32bc7435d1328c2ae73a8eb3172cd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029783"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Usar chaves gerenciadas pelo cliente com Azure Key Vault para gerenciar a criptografia de armazenamento do Azure

Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa os dados. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Sobre as chaves gerenciadas pelo cliente

O diagrama a seguir mostra como o armazenamento do Azure usa Azure Active Directory e Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Diagrama mostrando como as chaves gerenciadas pelo cliente funcionam no armazenamento do Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

A seguinte lista explica as etapas enumeradas no diagrama:

1. Um administrador de Azure Key Vault concede permissões a chaves de criptografia para a identidade gerenciada associada à conta de armazenamento.
2. Um administrador de armazenamento do Azure configura a criptografia com uma chave gerenciada pelo cliente para a conta de armazenamento.
3. O armazenamento do Azure usa a identidade gerenciada associada à conta de armazenamento para autenticar o acesso a Azure Key Vault por meio de Azure Active Directory.
4. O armazenamento do Azure encapsula a chave de criptografia da conta com a chave do cliente em Azure Key Vault.
5. Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault para desencapsular a chave de criptografia da conta para executar operações de criptografia e descriptografia.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Criar uma conta que dê suporte a chaves gerenciadas pelo cliente para filas e tabelas

Os dados armazenados nos serviços de fila e tabela não são automaticamente protegidos por uma chave gerenciada pelo cliente quando as chaves gerenciadas pelo cliente são habilitadas para a conta de armazenamento. Opcionalmente, você pode configurar esses serviços no momento em que cria a conta de armazenamento a ser incluída nessa proteção.

Para obter mais informações sobre como criar uma conta de armazenamento que dá suporte a chaves gerenciadas pelo cliente para filas e tabelas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas](account-encryption-key-create.md).

Os dados nos serviços BLOB e arquivo são sempre protegidos por chaves gerenciadas pelo cliente quando as chaves gerenciadas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Habilitar chaves gerenciadas pelo cliente para uma conta de armazenamento

Quando você configura uma chave gerenciada pelo cliente, o armazenamento do Azure encapsula a chave de criptografia de dados raiz para a conta com a chave gerenciada pelo cliente no cofre de chaves associado. A habilitação de chaves gerenciadas pelo cliente não afeta o desempenho e entra em vigor imediatamente.

Quando você habilita ou desabilita as chaves gerenciadas pelo cliente, ou quando você modifica a chave ou a versão da chave, a proteção da chave de criptografia raiz é alterada, mas os dados em sua conta de armazenamento do Azure não precisam ser criptografados novamente.

As chaves gerenciadas pelo cliente podem ser habilitadas somente em contas de armazenamento existentes. O cofre de chaves deve ser configurado com políticas de acesso que concedem permissões para a identidade gerenciada associada à conta de armazenamento. A identidade gerenciada estará disponível somente depois que a conta de armazenamento for criada.

Você pode alternar entre chaves gerenciadas pelo cliente e chaves gerenciadas pela Microsoft a qualquer momento. Para obter mais informações sobre chaves gerenciadas pela Microsoft, consulte [sobre gerenciamento de chaves de criptografia](storage-service-encryption.md#about-encryption-key-management).

Para saber como usar as chaves gerenciadas pelo cliente com o Azure Key Vault para a criptografia de armazenamento do Azure, consulte um destes artigos:

- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para criptografia de armazenamento do Azure do CLI do Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure AD. Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída à sua conta de armazenamento nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou a conta de armazenamento de um diretório do Azure AD para outro, a identidade gerenciada associada à conta de armazenamento não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente em uma conta de armazenamento, você deve usar um cofre de chaves do Azure para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves.

A criptografia de armazenamento do Azure dá suporte às chaves RSA e RSA-HSM de tamanhos 2048, 3072 e 4096. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

O uso de Azure Key Vault tem custos associados. Para obter mais informações, consulte [preços de Key Vault](/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Você tem duas opções para girar uma chave gerenciada pelo cliente:

- **Rotação automática:** Para configurar a rotação automática de chaves gerenciadas pelo cliente, omita a versão da chave ao habilitar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Se a versão da chave for omitida, o Armazenamento do Microsoft Azure verifica o Azure Key Vault diariamente para obter uma nova versão de uma chave gerenciada pelo cliente. Se uma nova versão da chave estiver disponível, o Armazenamento do Microsoft Azure usa automaticamente a versão mais recente da chave.
- **Rotação manual:** Para usar uma versão de chave específica para a criptografia de armazenamento do Azure, especifique essa versão de chave ao habilitar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Se você especificar a versão de chave, o armazenamento do Azure usará essa versão para criptografia até que você atualize manualmente a versão de chave.

    Quando a chave é girada manualmente, você deve atualizar a conta de armazenamento para usar o novo URI de versão de chave. Para saber como atualizar a conta de armazenamento para usar uma nova versão da chave no portal do Azure, consulte [atualizar manualmente a versão da chave](storage-encryption-keys-portal.md#manually-update-the-key-version).

A rotação de uma chave gerenciada pelo cliente não dispara a recriptografia de dados na conta de armazenamento. Não há nenhuma ação adicional necessária do usuário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Você pode revogar o acesso da conta de armazenamento para a chave gerenciada pelo cliente a qualquer momento. Depois que o acesso às chaves gerenciadas pelo cliente for revogado ou depois que a chave tiver sido desabilitada ou excluída, os clientes não poderão chamar operações que lidam ou gravam em um BLOB ou em seus metadados. As tentativas de chamar qualquer uma das seguintes operações falharão com o código de erro 403 (proibido) para todos os usuários:

- [Listar BLOBs](/rest/api/storageservices/list-blobs)quando chamado com o `include=metadata` parâmetro no URI de solicitação
- [Get Blob](/rest/api/storageservices/get-blob)
- [Obter propriedades do blob](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Blob de instantâneo](/rest/api/storageservices/snapshot-blob), quando chamado com o `x-ms-meta-name` cabeçalho de solicitação
- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Copiar BLOB da URL](/rest/api/storageservices/copy-blob-from-url)
- [Definir camada do blob](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Colocar bloco da URL](/rest/api/storageservices/put-block-from-url)
- [Acrescentar Bloco](/rest/api/storageservices/append-block)
- [Anexar bloco da URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Colocar Página](/rest/api/storageservices/put-page)
- [Colocar página da URL](/rest/api/storageservices/put-page-from-url)
- [Blob de Cópia Incremental](/rest/api/storageservices/incremental-copy-blob)

Para chamar essas operações novamente, restaure o acesso à chave gerenciada pelo cliente.

Todas as operações de dados que não estão listadas nesta seção podem continuar depois que as chaves gerenciadas pelo cliente são revogadas ou uma chave é desabilitada ou excluída.

Para revogar o acesso às chaves gerenciadas pelo cliente, use o [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) ou [CLI do Azure](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves gerenciadas pelo cliente para Azure Managed disks

As chaves gerenciadas pelo cliente também estão disponíveis para gerenciar a criptografia de Azure Managed disks. Chaves gerenciadas pelo cliente se comportam de maneira diferente para discos gerenciados do que para recursos de armazenamento do Azure Para obter mais informações, consulte [criptografia no servidor de Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) para Windows ou [criptografia do lado do servidor de Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) para Linux.

## <a name="next-steps"></a>Próximas etapas

- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para criptografia de armazenamento do Azure do CLI do Azure](storage-encryption-keys-cli.md)
- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
