---
title: Identidades gerenciadas para o Azure
description: Saiba mais sobre o uso de identidades gerenciadas para o Azure com o Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: a26f188ed2f5e18bdf775cd1fb21001495ffdc89
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461439"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Usando identidades gerenciadas para Azure com malha de serviço

Um desafio comum ao criar aplicativos em nuvem é como gerenciar com segurança as credenciais em seu código para autenticar em vários serviços sem salvá-los localmente em uma estação de trabalho do desenvolvedor ou no controle de origem. *As identidades gerenciadas para o Azure* resolvem esse problema para todos os seus recursos no Azure Active Directory (Azure AD), fornecendo-lhes identidades gerenciadas automaticamente dentro do Azure AD. Você pode usar a identidade de um serviço para autenticar qualquer serviço que suporte a autenticação Azure AD, incluindo o Key Vault, sem quaisquer credenciais armazenadas em seu código.

*As identidades gerenciadas para os recursos do Azure* são gratuitas com o Azure AD para assinaturas do Azure. Não há nenhum custo adicional.

> [!NOTE]
> *Identidades gerenciadas para o Azure* é o novo nome para o serviço anteriormente conhecido como MSI (Managed Service Identity).

## <a name="concepts"></a>Conceitos

As identidades gerenciadas para o Azure são baseadas em vários conceitos-chave:

- **ID do cliente** - um identificador exclusivo gerado pelo Azure AD que está vinculado a um principal de aplicativo e serviço durante seu provisionamento inicial (veja também [o ID do aplicativo](/azure/active-directory/develop/developer-glossary#application-id-client-id).)

- **ID principal** - o ID do objeto principal do serviço para sua Identidade Gerenciada que é usado para conceder acesso baseado em função a um recurso do Azure.

- **Service Principal** - um objeto do Azure Active Directory, que representa a projeção de um aplicativo AAD em um determinado inquilino (veja também [o principal do serviço](../active-directory/develop/developer-glossary.md#service-principal-object).)

Há dois tipos de identidades gerenciadas:

- Uma **identidade gerenciada atribuída ao sistema** é ativada diretamente em uma instância de serviço do Azure.  O ciclo de vida de uma identidade atribuída ao sistema é exclusivo da instância de serviço do Azure em que está habilitada.
- Uma **identidade gerenciada atribuída pelo usuário** é criada como um recurso autônomo do Azure. A identidade pode ser atribuída a uma ou mais instâncias de serviço do Azure e é gerenciada separadamente dos ciclos de vida dessas instâncias.

Para entender melhor a diferença entre os tipos de identidade gerenciados, [consulte Como funcionam as identidades gerenciadas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>Cenários suportados para aplicativos de malha de serviço

As identidades gerenciadas para malha de serviço são suportadas apenas em clusters de malha de serviço implantados pelo Azure e somente para aplicativos implantados como recursos do Azure; um aplicativo que não é implantado como um recurso Do Zure não pode ser atribuído uma identidade. Conceitualmente falando, o suporte para identidades gerenciadas em um cluster Azure Service Fabric consiste em duas fases:

1. Atribuir uma ou mais identidades gerenciadas ao recurso do aplicativo; um aplicativo pode ser atribuído a uma única identidade atribuída ao sistema e/ou até 32 identidades atribuídas pelo usuário, respectivamente.

2. Dentro da definição do aplicativo, mapeie uma das identidades atribuídas ao aplicativo a qualquer serviço individual que compõe o aplicativo.

A identidade atribuída ao sistema de um aplicativo é exclusiva desse aplicativo; uma identidade atribuída pelo usuário é um recurso autônomo, que pode ser atribuído a vários aplicativos. Dentro de um aplicativo, uma única identidade (atribuída ao sistema ou atribuída ao usuário) pode ser atribuída a vários serviços do aplicativo, mas cada serviço individual só pode ser atribuído a uma identidade. Por último, um serviço deve ser atribuído uma identidade explicitamente para ter acesso a esse recurso. Com efeito, o mapeamento das identidades de um aplicativo para seus serviços constituintes permite o isolamento no aplicativo — um serviço só pode usar a identidade mapeada para ele.  

Atualmente, os seguintes cenários são suportados para este recurso:

- Implantar um novo aplicativo com um ou mais serviços e uma ou mais identidades atribuídas

- Atribua uma ou mais identidades gerenciadas a um aplicativo existente (implantado pelo Azure) para acessar os recursos do Azure

Os seguintes cenários não são suportados ou não recomendados; observe que essas ações podem não ser bloqueadas, mas podem levar a paralisações em seus aplicativos:

- Remover ou alterar as identidades atribuídas a um aplicativo; se você deve fazer alterações, envie implantações separadas para primeiro adicionar uma nova atribuição de identidade e, em seguida, para remover uma anteriormente atribuída. A remoção de uma identidade de um aplicativo existente pode ter efeitos indesejáveis, incluindo deixar seu aplicativo em um estado que não é atualizável. É seguro excluir o aplicativo completamente se a remoção de uma identidade for necessária; note que isso excluirá a identidade atribuída ao sistema (se assim definida) associada ao aplicativo e removerá quaisquer associações com as identidades atribuídas pelo usuário atribuídas ao aplicativo.

- O suporte ao Service Fabric para identidades gerenciadas não está integrado no [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md).

## <a name="next-steps"></a>Próximas etapas

- [Implantar um novo cluster de malha de serviço Azure com suporte de identidade gerenciado](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Habilite o suporte de identidade gerenciado em um cluster azure Service Fabric existente](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Aproveite a identidade gerenciada de um aplicativo service fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
- [Conceda acesso a um aplicativo de malha de serviço do Azure a outros recursos do Azure](./how-to-grant-access-other-resources.md)
- [Declarando e usando segredos de aplicativos como KeyVaultReferences](./service-fabric-keyvault-references.md)
