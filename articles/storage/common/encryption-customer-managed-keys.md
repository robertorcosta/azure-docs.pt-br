---
title: Use chaves gerenciadas pelo cliente com o Azure Key Vault para gerenciar a criptografia da conta
titleSuffix: Azure Storage
description: Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa seus dados. As chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456814"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Use chaves gerenciadas pelo cliente com o Azure Key Vault para gerenciar a criptografia de armazenamento do Azure

Você pode usar sua própria chave de criptografia para proteger os dados em sua conta de armazenamento. Quando você especifica uma chave gerenciada pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que criptografa seus dados. As chaves gerenciadas pelo cliente oferecem maior flexibilidade para gerenciar controles de acesso.

Você deve usar o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Sobre chaves gerenciadas pelo cliente

O diagrama a seguir mostra como o Azure Storage usa o Azure Active Directory e o Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Diagrama mostrando como as chaves gerenciadas pelo cliente funcionam no Armazenamento Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

A lista a seguir explica as etapas numeradas no diagrama:

1. Um administrador do Azure Key Vault concede permissões às chaves de criptografia da identidade gerenciada associada à conta de armazenamento.
2. Um administrador do Azure Storage configura a criptografia com uma chave gerenciada pelo cliente para a conta de armazenamento.
3. O Azure Storage usa a identidade gerenciada associada à conta de armazenamento para autenticar o acesso ao Azure Key Vault via Azure Active Directory.
4. O Azure Storage envolve a chave de criptografia da conta com a chave do cliente no Azure Key Vault.
5. Para operações de leitura/gravação, o Azure Storage envia solicitações ao Azure Key Vault para desembrulhar a chave de criptografia da conta para executar operações de criptografia e descriptografia.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Crie uma conta que suporte chaves gerenciadas pelo cliente para filas e tabelas

Os dados armazenados nos serviços De fila e mesa não são protegidos automaticamente por uma chave gerenciada pelo cliente quando as chaves gerenciadas pelo cliente são habilitadas para a conta de armazenamento. Você pode configurar opcionalmente esses serviços no momento em que você criar a conta de armazenamento a ser incluída nesta proteção.

Para obter mais informações sobre como criar uma conta de armazenamento que suporte as chaves gerenciadas pelo cliente para filas e tabelas, consulte [Criar uma conta que suporte chaves gerenciadas pelo cliente para tabelas e filas.](account-encryption-key-create.md)

Os dados nos serviços Blob e File são sempre protegidos por chaves gerenciadas pelo cliente quando as chaves gerenciadas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Habilite as chaves gerenciadas pelo cliente para uma conta de armazenamento

As chaves gerenciadas pelo cliente podem ser habilitadas apenas em contas de armazenamento existentes. O cofre-chave deve ser provisionado com políticas de acesso que concedam permissões-chave à identidade gerenciada associada à conta de armazenamento. A identidade gerenciada só está disponível depois que a conta de armazenamento for criada.

Quando você configura uma chave gerenciada pelo cliente, o Azure Storage envolve a chave de criptografia de dados raiz da conta com a chave gerenciada pelo cliente no cofre de chaves associado. Habilitar chaves gerenciadas pelo cliente não afeta o desempenho e faz efeito imediatamente.

Quando você modifica a chave que está sendo usada para criptografia do Azure Storage, ativando ou desativando chaves gerenciadas pelo cliente, atualizando a versão-chave ou especificando uma chave diferente, a criptografia da chave raiz muda, mas os dados em sua conta do Azure Storage não precisam ser recriptografados.

Quando você habilita ou desativa chaves gerenciadas pelo cliente, ou quando você modifica a chave ou a versão-chave, a proteção da chave de criptografia raiz muda, mas os dados em sua conta do Azure Storage não precisam ser recriptografados.

Para saber como usar as chaves gerenciadas pelo cliente com o Azure Key Vault para criptografia de armazenamento azure, consulte um desses artigos:

- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure a partir do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para os recursos do Azure, um recurso do Azure AD. Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal Azure, uma identidade gerenciada é automaticamente atribuída à sua conta de armazenamento sob as capas. Se você posteriormente mover a conta de assinatura, grupo de recursos ou armazenamento de um diretório AD do Azure para outro, a identidade gerenciada associada à conta de armazenamento não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte **Transferir uma assinatura entre diretórios Azure AD** em PERGUNTAS [FREQUENTES e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazene chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar as chaves gerenciadas pelo cliente em uma conta de armazenamento, você deve usar um Cofre de Chaves do Azure para armazenar suas chaves. Você deve habilitar as propriedades **Soft Delete** e **Não Expurguir** no cofre de chaves.

Apenas as chaves RSA e RSA-HSM de 2048 bits são suportadas com criptografia de armazenamento Azure. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="rotate-customer-managed-keys"></a>Gire as chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar a conta de armazenamento para usar a nova versão-chave URI. Para saber como atualizar a conta de armazenamento para usar uma nova versão da chave no portal Azure, consulte a seção intitulada **Atualizar a versão-chave** em [Configurar chaves gerenciadas pelo cliente para armazenamento Azure usando o portal Azure](storage-encryption-keys-portal.md).

Girar a chave não desencadeia a recriptografia de dados na conta de armazenamento. Não há mais nenhuma ação necessária do usuário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Você pode revogar o acesso da conta de armazenamento à chave gerenciada pelo cliente a qualquer momento. Depois que o acesso às chaves gerenciadas pelo cliente for revogado ou depois que a chave for desativada ou excluída, os clientes não podem ligar para operações que lêem ou escrevem para uma bolha ou seus metadados. As tentativas de ligar para qualquer uma das seguintes operações falharão com o código de erro 403 (Proibido) para todos os usuários:

- [List blobs](/rest/api/storageservices/list-blobs), quando `include=metadata` chamado com o parâmetro na solicitação URI
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Definir Metadados de Blob](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), quando `x-ms-meta-name` chamado com o cabeçalho de solicitação
- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Copiar blob da URL](/rest/api/storageservices/copy-blob-from-url)
- [Definir camada do Blob](/rest/api/storageservices/set-blob-tier)
- [Colocar bloco](/rest/api/storageservices/put-block)
- [Colocar bloquear a URL](/rest/api/storageservices/put-block-from-url)
- [Acrescentar Bloco](/rest/api/storageservices/append-block)
- [Anexar bloco de URL](/rest/api/storageservices/append-block-from-url)
- [Colocar Blob](/rest/api/storageservices/put-blob)
- [Colocar Página](/rest/api/storageservices/put-page)
- [Colocar página da URL](/rest/api/storageservices/put-page-from-url)
- [Blob de cópia incremental](/rest/api/storageservices/incremental-copy-blob)

Para ligar para essas operações novamente, restaure o acesso à chave gerenciada pelo cliente.

Todas as operações de dados que não estão listadas nesta seção podem prosseguir depois que as chaves gerenciadas pelo cliente forem revogadas ou uma chave for desativada ou excluída.

Para revogar o acesso às chaves gerenciadas pelo cliente, use [o PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) ou [o Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves gerenciadas pelo cliente para discos gerenciados pelo Azure

As chaves gerenciadas pelo cliente também estão disponíveis para o gerenciamento da criptografia de discos gerenciados pelo Azure. As chaves gerenciadas pelo cliente se comportam de forma diferente para discos gerenciados do que para os recursos de armazenamento do Azure. Para obter mais informações, consulte [a criptografia do lado do servidor dos discos gerenciados pelo Azure](../../virtual-machines/windows/disk-encryption.md) para criptografia do lado do Windows ou do Servidor dos discos [gerenciados pelo Azure](../../virtual-machines/linux/disk-encryption.md) para Linux.

## <a name="next-steps"></a>Próximas etapas

- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure a partir do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves gerenciadas pelo cliente com key vault para criptografia de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)
- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
