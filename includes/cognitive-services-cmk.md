---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372189"
---
### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. O recurso de serviços cognitivas e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Um novo recurso de serviços cognitivas é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada ao recurso de serviços cognitivas. A identidade gerenciada está disponível somente depois que o recurso é criado usando o tipo de preço para CMK.

Para saber como usar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de serviços cognitivas, consulte:

- [Configurar chaves gerenciadas pelo cliente com Key Vault para criptografia de serviços cognitivas do portal do Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Habilitar chaves gerenciadas pelo cliente também habilitará uma identidade gerenciada atribuída pelo sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída pelo sistema estiver habilitada, esse recurso será registrado com Azure Active Directory. Depois de ser registrado, a identidade gerenciada receberá acesso ao Key Vault selecionado durante a configuração de chave gerenciada pelo cliente. Você pode saber mais sobre [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas pelo sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente, você deve usar um Azure Key Vault para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves.

Somente chaves RSA de tamanho 2048 têm suporte com a criptografia de serviços cognitivas. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o recurso de serviços cognitivas para usar o novo URI de chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal do Azure, consulte a seção intitulada **atualizar a versão da chave** em [Configurar chaves gerenciadas pelo cliente para serviços cognitivas usando o portal do Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

A rotação da chave não aciona a nova criptografia dos dados no recurso. Não há nenhuma ação adicional necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados no recurso de serviços cognitivas, pois a chave de criptografia é inacessível por serviços cognitivas.


