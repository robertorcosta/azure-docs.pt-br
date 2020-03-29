---
title: Criptografia tradutora de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia tradutora de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372233"
---
# <a name="translator-encryption-of-data-at-rest"></a>Criptografia tradutora de dados em repouso

O Tradutor criptografa automaticamente seus dados, que você carrega para construir modelos de tradução personalizados, quando ele é persiciado para a nuvem, ajudando a cumprir suas metas de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de Serviços Cognitivos

Os dados são criptografados e descriptografados usando criptografia [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Criptografia e descriptografia são transparentes, o que significa que criptografia e acesso são gerenciados para você. Seus dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Se você estiver usando um nível de preços que suporte as chaves gerenciadas pelo Cliente, você poderá ver as configurações de criptografia do seu recurso na seção **Criptografia** do [portal Azure,](https://portal.azure.com)conforme mostrado na imagem a seguir.

![Exibir configurações de criptografia](../media/cognitive-services-encryption/encryptionblade.png)

Para assinaturas que só suportam chaves de criptografia gerenciadas pela Microsoft, você não terá uma seção **Criptografia.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Há também uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK), oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis para todos os níveis de preços para o serviço Tradutor. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [Formulário de Solicitação de Chave Gerenciado pelo Cliente Tradutor](https://aka.ms/cogsvc-cmk) levará aproximadamente 3-5 dias úteis para ouvir de volta o status de sua solicitação. Dependendo da demanda, você pode ser colocado em uma fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para usar CMK com o serviço Tradutor, você precisará criar um novo recurso tradutor. Uma vez que o recurso Tradutor seja criado, você pode usar o Azure Key Vault para configurar sua identidade gerenciada.

Siga estas etapas para habilitar as chaves gerenciadas pelo cliente para o Tradutor:

1. Crie seu novo texto tradutor regional ou recurso de Serviços Cognitivos regionais. Isso não funcionará com um recurso global.
2. Habilitado identidade gerenciada no portal Azure e adicionar suas informações-chave gerenciadas pelo cliente.
3. Crie um novo espaço de trabalho no Personal Translator e associe essas informações de assinatura.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Você deve usar o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Um novo recurso de Serviços Cognitivos é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre principal deve ser provisionado com políticas de acesso que concedem permissões-chave à identidade gerenciada associada ao recurso Serviços Cognitivos. A identidade gerenciada está disponível assim que o recurso é criado.

Para aprender como usar as chaves gerenciadas pelo cliente com a criptografia do Azure Key Vault for Cognitive Services, consulte:

- [Configure chaves gerenciadas pelo cliente com a criptografia Key Vault for Cognitive Services do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

A habilitação de chaves gerenciadas pelo cliente também permitirá uma identidade gerenciada atribuída ao sistema, um recurso do Azure AD. Uma vez que a identidade gerenciada atribuída ao sistema esteja habilitada, esse recurso será registrado no Azure Active Directory. Após ser registrada, a identidade gerenciada terá acesso ao Key Vault selecionado durante a configuração da chave gerenciada pelo cliente. Você pode aprender mais sobre [Identidades Gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desativar as identidades gerenciadas do sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Qualquer característica que dependa desse dado vai parar de funcionar. Todos os modelos que você implantou também serão desimplantados. Todos os dados carregados serão excluídos do Personal Translator. Se as identidades gerenciadas forem reativadas, não reimplantaremos automaticamente o modelo para você.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura as chaves gerenciadas pelo cliente no portal Azure, uma identidade gerenciada é automaticamente atribuída as capas. Se você posteriormente mover a assinatura, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte **Transferir uma assinatura entre diretórios Azure AD** em PERGUNTAS [FREQUENTES e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazene chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar as chaves gerenciadas pelo cliente, você deve usar um Cofre de Chaves do Azure para armazenar suas chaves. Você deve habilitar as propriedades **Soft Delete** e **Não Expurguir** no cofre de chaves.

Apenas as chaves RSA do tamanho 2048 são suportadas com criptografia de Serviços Cognitivos. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Se todo o cofre de chaves for excluído, seus dados não serão mais exibidos e todos os seus modelos serão desimplantados. Todos os dados carregados serão excluídos do Personal Translator. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os dados no recurso de Serviços Cognitivos e seus modelos não serão implantados, pois a chave de criptografia é inacessível pelos Serviços Cognitivos. Todos os dados carregados também serão excluídos do Personal Translator.


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
