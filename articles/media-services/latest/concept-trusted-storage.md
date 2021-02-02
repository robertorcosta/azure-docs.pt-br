---
title: Armazenamento confiável para os serviços de mídia
description: A autenticação de identidades gerenciadas permite que os serviços de mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio de acesso de armazenamento confiável.
keywords: armazenamento confiável, identidades gerenciadas
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408469"
---
# <a name="trusted-storage-for-media-services"></a>Armazenamento confiável para os serviços de mídia

Ao criar uma conta de serviços de mídia, você deve associá-la a uma conta de armazenamento. Os serviços de mídia podem acessar essa conta de armazenamento usando a autenticação do sistema. Os serviços de mídia validam que a conta dos serviços de mídia e a conta de armazenamento estão na mesma assinatura e valida que o usuário que está adicionando a associação tem acesso à conta de armazenamento com o Azure Resource Manager RBAC.

No entanto, se você quiser usar um firewall para proteger sua conta de armazenamento e habilitar o armazenamento confiável, deverá usar a autenticação de [identidades gerenciadas](concept-managed-identities.md) . Ele permite que os serviços de mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio de acesso de armazenamento confiável.

Para entender os métodos de criação de armazenamento confiável com identidades gerenciadas, leia [identidades gerenciadas e serviços de mídia](concept-managed-identities.md).

Para obter mais informações sobre chaves gerenciadas pelo cliente e Key Vault, consulte [traga sua própria chave (chaves gerenciadas pelo cliente) com os serviços de mídia](concept-use-customer-managed-keys-byok.md)

Para obter mais informações sobre serviços confiáveis da Microsoft, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Tutoriais

Esses tutoriais incluem os dois cenários mencionados acima.

- [Usar o portal do Azure para usar as chaves gerenciadas pelo cliente ou o BYOK com os Serviços de Mídia](tutorial-byok-portal.md)
- [Use chaves gerenciadas pelo cliente ou BYOK com a API REST dos serviços de mídia](tutorial-byok-postman.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o que as identidades gerenciadas podem fazer por você e seus aplicativos do Azure, confira [identidades gerenciadas do Azure ad](../../active-directory/managed-identities-azure-resources/overview.md).