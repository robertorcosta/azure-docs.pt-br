---
title: Criptografia do serviço de Reconhecimento vocal de dados em repouso
titleSuffix: Azure Cognitive Services
description: Reconhecimento vocal criptografia do serviço de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 4fc816c3894120a5d1b356d91ebebbc56f21b530
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052691"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Criptografia do serviço de Reconhecimento vocal de dados em repouso

O serviço de Reconhecimento vocal criptografa automaticamente os dados quando eles são persistidos na nuvem. A criptografia do serviço de Reconhecimento vocal protege seus dados e ajuda você a atender aos compromissos de conformidade e segurança da organização.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de serviços cognitivas

Os dados são criptografados e descriptografados usando [a criptografia AES de 256 bits em](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conformidade com [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Os dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Também há uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK) oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Também há uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), também conhecidas como traga sua própria chave (BYOK), oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de serviços cognitivas e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Chaves gerenciadas pelo cliente para Reconhecimento vocal

Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [formulário de solicitação de chave gerenciada pelo cliente do serviço Luis](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com LUIS, você precisará criar um novo recurso de Reconhecimento vocal da portal do Azure e selecionar E0 como o tipo de preço. O novo SKU funcionará da mesma forma que a SKU F0 que já está disponível, exceto para CMK. Os usuários não poderão atualizar do F0 para o novo SKU E0.

![Imagem de assinatura do LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão disponíveis em todas as [regiões de criação](luis-reference-regions.md). 

### <a name="limitations"></a>Limitações

Há algumas limitações ao usar a camada E0 com aplicativos existentes/criados anteriormente:

* A migração para um recurso E0 será bloqueada. Os usuários só poderão migrar seus aplicativos para os recursos de F0. Depois de migrar um recurso existente para F0, você pode criar um novo recurso na camada E0. Saiba mais sobre a [migração aqui](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* A movimentação de aplicativos para ou de um recurso E0 será bloqueada. Uma solução alternativa para essa limitação é exportar seu aplicativo existente e importá-lo como um recurso E0.
* Não há suporte para o recurso de verificação ortográfica do Bing.
* O registro em log do tráfego do usuário final será desabilitado se seu aplicativo for E0.
* O recurso de desobstrução de fala do serviço de bot do Azure não tem suporte para aplicativos na camada E0. Esse recurso está disponível por meio do serviço de bot do Azure, que não dá suporte a CMK.
* O recurso de desobstrução de fala do portal requer o armazenamento de BLOBs do Azure. Para obter mais informações, consulte [traga seu próprio armazenamento](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Um novo recurso de serviços cognitivas é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada ao recurso de serviços cognitivas. A identidade gerenciada está disponível somente depois que o recurso é criado usando o tipo de preço para CMK.

Para saber como usar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de serviços cognitivas, consulte:

- [Configurar chaves gerenciadas pelo cliente com Key Vault para criptografia de serviços cognitivas do portal do Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Habilitar chaves gerenciadas pelo cliente também habilitará uma identidade gerenciada atribuída pelo sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída pelo sistema estiver habilitada, esse recurso será registrado com Azure Active Directory. Depois de ser registrado, a identidade gerenciada receberá acesso ao Key Vault selecionado durante a configuração de chave gerenciada pelo cliente. Você pode saber mais sobre [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas pelo sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente, você deve usar um Azure Key Vault para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves.

Somente chaves RSA de tamanho 2048 têm suporte com a criptografia de serviços cognitivas. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode fazer a rotação de uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o recurso de serviços cognitivas para usar o novo URI de chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal do Azure, consulte a seção intitulada **atualizar a versão da chave** em [Configurar chaves gerenciadas pelo cliente para serviços cognitivas usando o portal do Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

A rotação da chave não aciona a nova criptografia dos dados no recurso. Não há nenhuma ação adicional necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, confira [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados no recurso de serviços cognitivas, pois a chave de criptografia é inacessível por serviços cognitivas.

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciada pelo cliente do serviço LUIS](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
