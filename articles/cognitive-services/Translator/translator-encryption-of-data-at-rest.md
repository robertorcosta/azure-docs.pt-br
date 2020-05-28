---
title: Criptografia do tradutor de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia do tradutor de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: egeaney
ms.openlocfilehash: 0a4a1f49735a30d7ad764e7f031610eabd09763e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995761"
---
# <a name="translator-encryption-of-data-at-rest"></a>Criptografia do tradutor de dados em repouso

O Translator criptografa automaticamente os dados, que você carrega para criar modelos de tradução personalizados, quando ele é mantido para a nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de serviços cognitivas

Os dados são criptografados e descriptografados usando [a criptografia AES de 256 bits em](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conformidade com [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para você. Os dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Se você estiver usando um tipo de preço que ofereça suporte a chaves gerenciadas pelo cliente, poderá ver as configurações de criptografia para seu recurso na seção **criptografia** do [portal do Azure](https://portal.azure.com), conforme mostrado na imagem a seguir.

![Exibir configurações de criptografia](../media/cognitive-services-encryption/encryptionblade.png)

Para assinaturas que dão suporte apenas a chaves de criptografia gerenciadas pela Microsoft, você não terá uma seção de **criptografia** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Também há uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), também conhecidas como traga sua própria chave (BYOK), oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis para todos os tipos de preço do serviço do tradutor. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [formulário de solicitação de chave gerenciada pelo cliente do tradutor](https://aka.ms/cogsvc-cmk) , será necessário cerca de 3-5 dias úteis para o retorno do status de sua solicitação. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço de tradutor, você precisará criar um novo recurso de tradutor. Depois que o recurso do tradutor for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

Siga estas etapas para habilitar chaves gerenciadas pelo cliente para o Tradutor:

1. Crie seu novo tradutor regional ou recurso de serviços de cognitiva regional. Isso não funcionará com um recurso global.
2. Habilitou a identidade gerenciada no portal do Azure e adicione as informações de chave gerenciada pelo cliente.
3. Crie um novo espaço de trabalho no Tradutor personalizado e associe essas informações de assinatura.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. O recurso de serviços cognitivas e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Um novo recurso de serviços cognitivas é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada ao recurso de serviços cognitivas. A identidade gerenciada está disponível assim que o recurso é criado.

Para saber como usar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de serviços cognitivas, consulte:

- [Configurar chaves gerenciadas pelo cliente com Key Vault para criptografia de serviços cognitivas do portal do Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Habilitar chaves gerenciadas pelo cliente também habilitará uma identidade gerenciada atribuída pelo sistema, um recurso do Azure AD. Depois que a identidade gerenciada atribuída pelo sistema estiver habilitada, esse recurso será registrado com Azure Active Directory. Depois de ser registrado, a identidade gerenciada receberá acesso ao Key Vault selecionado durante a configuração de chave gerenciada pelo cliente. Você pode saber mais sobre [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desabilitar as identidades gerenciadas atribuídas pelo sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Todos os recursos dependentes desses dados deixarão de funcionar. Todos os modelos implantados também serão desimplantados. Todos os dados carregados serão excluídos do tradutor personalizado. Se as identidades gerenciadas forem habilitadas novamente, não iremos reimplantar o modelo automaticamente para você.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou o recurso de um diretório do Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente, você deve usar um Azure Key Vault para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves.

Somente chaves RSA de tamanho 2048 têm suporte com a criptografia de serviços cognitivas. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Se o cofre de chaves inteiro for excluído, seus dados não serão mais exibidos e todos os seus modelos serão desimplantados. Todos os dados carregados serão excluídos do tradutor personalizado. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados no recurso de serviços cognitivas e seus modelos serão desimplantados, pois a chave de criptografia fica inacessível por serviços cognitivas. Todos os dados carregados também serão excluídos do tradutor personalizado.


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
