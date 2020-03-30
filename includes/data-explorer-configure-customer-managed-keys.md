---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297882"
---
O Azure Data Explorer criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados. 

As chaves gerenciadas pelo cliente devem ser armazenadas em [um Cofre de Chaves Azure](/azure/key-vault/key-vault-overview). Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar uma API do Azure Key Vault para gerar chaves. O cluster Azure Data Explorer e o cofre-chave devem estar na mesma região, mas eles podem estar em assinaturas diferentes. Para obter uma explicação detalhada sobre as chaves gerenciadas pelo cliente, consulte [as chaves gerenciadas pelo cliente com o Azure Key Vault](/azure/storage/common/storage-service-encryption). 

Este artigo mostra como configurar chaves gerenciadas pelo cliente.

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

Para configurar as chaves gerenciadas pelo cliente com [o](/azure/key-vault/key-vault-ovw-soft-delete)Azure Data Explorer, você deve definir duas propriedades no cofre principal : Soft **Delete** e Não **Purga .** Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, execute **a habilitação de exclusão suave** e **a ativação da proteção de purga** no [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) ou [no Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) em um cofre de chaves novo ou existente. Apenas as chaves RSA do tamanho 2048 são suportadas. Para obter mais informações sobre as chaves, consulte [chaves do Cofre de Chaves](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> A criptografia de dados usando chaves gerenciadas pelo cliente não é suportada em [clusters de líderes e seguidores](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Atribuir uma identidade ao cluster

Para habilitar as chaves gerenciadas pelo cliente para o cluster, primeiro atribua uma identidade gerenciada atribuída ao sistema ao cluster. Você usará essa identidade gerenciada para conceder permissões ao cluster para acessar o cofre de chaves. Para configurar identidades gerenciadas atribuídas ao sistema, consulte [identidades gerenciadas](/azure/data-explorer/managed-identities).