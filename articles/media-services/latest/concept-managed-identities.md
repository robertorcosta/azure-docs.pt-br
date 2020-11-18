---
title: Identidades gerenciadas e armazenamento confiável com os serviços de mídia
description: Os serviços de mídia podem ser usados com identidades gerenciadas para habilitar o armazenamento confiável.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: a0ded2a1ad5cd590ab5715edebde2ab19e399e8d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686598"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Identidades gerenciadas e armazenamento confiável com os serviços de mídia

Os serviços de mídia podem ser usados com [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para habilitar o armazenamento confiável. Ao criar uma conta de serviços de mídia, você deve associá-la a uma conta de armazenamento. Os serviços de mídia podem acessar essa conta de armazenamento usando a autenticação do sistema. Os serviços de mídia validam que a conta dos serviços de mídia e a conta de armazenamento estão na mesma assinatura e valida que o usuário que está adicionando a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Armazenamento confiável

No entanto, se você quiser usar um firewall para proteger sua conta de armazenamento, deverá usar a autenticação de identidade gerenciada. Ele permite que os serviços de mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio de acesso de armazenamento confiável.  Para obter mais informações sobre serviços confiáveis da Microsoft, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Cenários de identidade gerenciada dos serviços de mídia

Atualmente, há dois cenários em que a identidade gerenciada pode ser usada com os serviços de mídia:

- Use a identidade gerenciada da conta dos serviços de mídia para acessar as contas de armazenamento.

- Use a identidade gerenciada da conta dos serviços de mídia para acessar Key Vault acessar as chaves do cliente.

As próximas duas seções descrevem as diferenças nos dois cenários.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Usar a identidade gerenciada da conta dos serviços de mídia para acessar contas de armazenamento

1. Crie uma conta dos serviços de mídia com uma identidade gerenciada.
1. Conceda o acesso de entidade de identidade gerenciada a uma conta de armazenamento que você possui.
1. Os serviços de mídia podem, então, acessar a conta de armazenamento em seu nome usando a identidade gerenciada.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Use a identidade gerenciada da conta dos serviços de mídia para acessar Key Vault acessar as chaves do cliente

1. Crie uma conta dos serviços de mídia com uma identidade gerenciada.
1. Conceda acesso de entidade de identidade gerenciada a um Key Vault que você possui.
1. Configure a conta dos serviços de mídia para usar a criptografia de conta baseada na chave do cliente.
1. Os serviços de mídia acessam Key Vault em seu nome usando a identidade gerenciada.

Para obter mais informações sobre chaves gerenciadas pelo cliente e Key Vault, consulte [traga sua própria chave (chaves gerenciadas pelo cliente) com os serviços de mídia](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Tutoriais

Esses tutoriais incluem os dois cenários mencionados acima.

- [Usar o portal do Azure para usar chaves gerenciadas pelo cliente ou BYOK com os serviços de mídia](tutorial-byok-portal.md)
- [Use chaves gerenciadas pelo cliente ou BYOK com a API REST dos serviços de mídia](tutorial-byok-postman.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o que as identidades gerenciadas podem fazer por você e seus aplicativos do Azure, confira [identidades gerenciadas do Azure ad](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
