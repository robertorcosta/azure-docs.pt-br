---
title: Criptografia de serviço de compreensão de idiomas de dados em repouso
titleSuffix: Azure Cognitive Services
description: Criptografia de serviço de compreensão de linguagem de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372332"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Criptografia de serviço de compreensão de idiomas de dados em repouso

O serviço de compreensão de idiomas criptografa automaticamente seus dados quando eles são perscisos na nuvem. A criptografia do serviço Language Understanding protege seus dados e ajuda você a cumprir seus compromissos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a criptografia de Serviços Cognitivos

Os dados são criptografados e descriptografados usando criptografia [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [FIPS 140-2.](https://en.wikipedia.org/wiki/FIPS_140-2) Criptografia e descriptografia são transparentes, o que significa que criptografia e acesso são gerenciados para você. Seus dados são seguros por padrão e você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Há também uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com o Azure Key Vault

Há também uma opção para gerenciar sua assinatura com suas próprias chaves. As chaves gerenciadas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK), oferecem maior flexibilidade para criar, girar, desativar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Você deve usar o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Chaves gerenciadas pelo cliente para compreensão de idiomas

Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [Formulário de Solicitação de Chave Gerenciado pelo Cliente do Luis Service](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para ouvir de volta sobre o status de sua solicitação. Dependendo da demanda, você pode ser colocado em uma fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para usar CMK com LUIS, você precisará criar um novo recurso de compreensão de idiomas a partir do portal Azure e selecionar E0 como o Nível de Precificação. O novo SKU funcionará da mesma forma que o F0 SKU que já está disponível, exceto para CMK. Os usuários não poderão atualizar do F0 para o novo E0 SKU.

Os recursos E0 estão disponíveis apenas para o serviço de Autoria e que o nível E0 será inicialmente suportado apenas na região oeste dos EUA.

![Imagem de assinatura luis](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Disponibilidade regional

As chaves gerenciadas pelo cliente estão atualmente disponíveis na região **oeste dos EUA.**

### <a name="limitations"></a>Limitações

Existem algumas limitações ao usar o nível E0 com aplicativos existentes/criados anteriormente:

* A migração para um recurso E0 será bloqueada. Os usuários só poderão migrar seus aplicativos para recursos F0. Depois de migrar um recurso existente para f0, você pode criar um novo recurso no nível E0. Saiba mais sobre [a migração aqui](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* A movimentação de aplicativos para ou a partir de um recurso E0 será bloqueada. Um trabalho em torno dessa limitação é exportar seu aplicativo existente e importá-lo como um recurso E0.
* O recurso de verificação do Bing Spell não é suportado.
* O tráfego do usuário final é desativado se o aplicativo for E0.
* O recurso de preparação de fala do serviço Azure Bot não é suportado para aplicativos no nível E0. Esse recurso está disponível através do Azure Bot Service, que não suporta CMK.
* A capacidade de preparação de fala do portal requer o Armazenamento Azure Blob. Para obter mais informações, consulte [trazer seu próprio armazenamento](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Um novo recurso de Serviços Cognitivos é sempre criptografado usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que o recurso é criado. As chaves gerenciadas pelo cliente são armazenadas no Azure Key Vault e o cofre principal deve ser provisionado com políticas de acesso que concedem permissões-chave à identidade gerenciada associada ao recurso Serviços Cognitivos. A identidade gerenciada só está disponível depois que o recurso for criado usando o Nível de Preços para CMK.

Para aprender como usar as chaves gerenciadas pelo cliente com a criptografia do Azure Key Vault for Cognitive Services, consulte:

- [Configure chaves gerenciadas pelo cliente com a criptografia Key Vault for Cognitive Services do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

A habilitação de chaves gerenciadas pelo cliente também permitirá uma identidade gerenciada atribuída ao sistema, um recurso do Azure AD. Uma vez que a identidade gerenciada atribuída ao sistema esteja habilitada, esse recurso será registrado no Azure Active Directory. Após ser registrada, a identidade gerenciada terá acesso ao Key Vault selecionado durante a configuração da chave gerenciada pelo cliente. Você pode aprender mais sobre [Identidades Gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Se você desativar as identidades gerenciadas do sistema, o acesso ao cofre de chaves será removido e todos os dados criptografados com as chaves do cliente não estarão mais acessíveis. Qualquer característica que dependa desse dado vai parar de funcionar.

> [!IMPORTANT]
> Identidades gerenciadas não têm suporte a cenários entre diretórios. Quando você configura as chaves gerenciadas pelo cliente no portal Azure, uma identidade gerenciada é automaticamente atribuída as capas. Se você posteriormente mover a assinatura, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerenciada associada ao recurso não será transferida para o novo inquilino, de modo que as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte **Transferir uma assinatura entre diretórios Azure AD** em PERGUNTAS [FREQUENTES e problemas conhecidos com identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazene chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar as chaves gerenciadas pelo cliente, você deve usar um Cofre de Chaves do Azure para armazenar suas chaves. Você deve habilitar as propriedades **Soft Delete** e **Não Expurguir** no cofre de chaves.

Apenas as chaves RSA do tamanho 2048 são suportadas com criptografia de Serviços Cognitivos. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Gire as chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar o recurso Serviços Cognitivos para usar o novo URI de chave. Para saber como atualizar o recurso para usar uma nova versão da chave no portal Azure, consulte a seção intitulada **Atualizar a versão-chave** em [Configurar chaves gerenciadas pelo cliente para Serviços Cognitivos usando o portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

Girar a chave não desencadeia a recriptografia de dados no recurso. Não há mais nenhuma ação necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia efetivamente o acesso a todos os dados no recurso dos Serviços Cognitivos, uma vez que a chave de criptografia é inacessível pelos Serviços Cognitivos.

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave gerenciado pelo cliente do serviço LUIS](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
