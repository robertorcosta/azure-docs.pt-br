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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372189"
---
### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Você deve usar o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Um novo recurso de Serviços Cognitivos é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre principal deve ser provisionado com políticas de acesso que concedem permissões-chave à identidade gerenciada associada ao recurso Serviços Cognitivos. A identidade gerenciada só está disponível depois que o recurso for criado usando o Nível de Preços para CMK.

Para aprender como usar as chaves gerenciadas pelo cliente com a criptografia do Azure Key Vault for Cognitive Services, consulte:

- [Configure chaves gerenciadas pelo cliente com a criptografia Key Vault for Cognitive Services do portal Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

A habilitação de chaves gerenciadas pelo cliente também permitirá uma identidade gerenciada atribuída ao sistema, um recurso do Azure AD. Uma vez que a identidade gerenciada atribuída ao sistema esteja habilitada, esse recurso será registrado no Azure Active Directory. Após ser registrada, a identidade gerenciada terá acesso ao Key Vault selecionado durante a configuração da chave gerenciada pelo cliente. Você pode aprender mais sobre [Identidades Gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desativar as identidades gerenciadas do sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Qualquer característica que dependa desse dado vai parar de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura as chaves gerenciadas pelo cliente no portal Azure, uma identidade gerenciada é automaticamente atribuída as capas. Se você posteriormente mover a assinatura, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte **Transferir uma assinatura entre diretórios Azure AD** em PERGUNTAS [FREQUENTES e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazene chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar as chaves gerenciadas pelo cliente, você deve usar um Cofre de Chaves do Azure para armazenar suas chaves. Você deve habilitar as propriedades **Soft Delete** e **Não Expurguir** no cofre de chaves.

Apenas as chaves RSA do tamanho 2048 são suportadas com criptografia de Serviços Cognitivos. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Gire as chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o recurso Serviços Cognitivos para usar o novo URI de chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal Azure, consulte a seção intitulada **Atualizar a versão-chave** em [Configurar chaves gerenciadas pelo cliente para Serviços Cognitivos usando o portal Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Girar a chave não desencadeia a recriptografia de dados no recurso. Não há mais nenhuma ação necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os dados no recurso dos Serviços Cognitivos, uma vez que a chave de criptografia é inacessível pelos Serviços Cognitivos.


