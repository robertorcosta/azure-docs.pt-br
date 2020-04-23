---
title: Autorizar o acesso com o Azure Active Directory
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Event Hubs usando o Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 6216b56c8e8f0de4f9cd60306bbf9c5ed49a11ad
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025196"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory
O Azure Event Hubs suporta o uso do Azure Active Directory (Azure AD) para autorizar solicitações aos recursos do Event Hubs. Com o Azure AD, você pode usar o RBAC (Role-Based Access Control, controle de acesso baseado em função) para conceder permissões a um diretor de segurança, que pode ser um usuário ou um diretor de serviço de aplicativo. Para saber mais sobre papéis e atribuições de papéis, consulte [Entendendo os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Visão geral
Quando um principal de segurança (um usuário ou um aplicativo) tenta acessar um recurso do Event Hubs, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://eventhubs.azure.net/`solicitar um token é . Para clientes Kafka, o recurso `https://<namespace>.servicebus.windows.net`para solicitar um token é .
 1. Em seguida, o token é passado como parte de uma solicitação ao serviço Event Hubs para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2.0 em tempo de execução. Se um aplicativo estiver sendo executado dentro de uma entidade Azure, como uma VM do Azure, um conjunto de escala de máquina virtual ou um aplicativo Azure Function, ele poderá usar uma identidade gerenciada para acessar os recursos. Para saber como autenticar solicitações feitas por uma identidade gerenciada ao serviço Event Hubs, consulte [Authenticate acesso aos recursos do Azure Event Hubs com o Azure Active Directory e identidades gerenciadas para recursos do Azure](authenticate-managed-identity.md). 

A etapa de autorização exige que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Event Hubs fornece funções RBAC que englobam conjuntos de permissões para recursos do Event Hubs. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para obter mais informações sobre as funções RBAC, consulte [funções RBAC incorporadas para hubs de eventos azure](#built-in-rbac-roles-for-azure-event-hubs). 

Aplicativos nativos e aplicativos web que fazem solicitações aos Event Hubs também podem autorizar com o Azure AD. Para saber como solicitar um token de acesso e usá-lo para autorizar solicitações de recursos do Event Hubs, consulte [Authenticate acesso aos Hubs de Eventos do Azure com o Azure AD a partir de um aplicativo](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Atribuir funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). O Azure Event Hubs define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões usadas para acessar dados do hub de eventos e você também pode definir funções personalizadas para acessar os dados.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O acesso pode ser escopo para o nível de assinatura, o grupo de recursos, o namespace do Event Hubs ou qualquer recurso sob ele. Um diretor de segurança Azure AD pode ser um usuário, ou um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Funções RBAC incorporadas para hubs de eventos do Azure
O Azure fornece as seguintes funções de RBAC incorporadas para autorizar o acesso aos dados do Event Hubs usando o Azure AD e o OAuth:

- [Azure Event Hubs Data owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use essa função para dar acesso completo aos recursos do Event Hubs.
- [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Use essa função para dar ao envio acesso aos recursos do Event Hubs.
- [Receptor de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use essa função para dar ao consumidor/recebimento de acesso aos recursos do Event Hubs.

## <a name="resource-scope"></a>Escopo de recursos 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o escopo de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode escopo de acesso aos recursos do Event Hubs, começando pelo escopo mais estreito:

- **Grupo de consumidores**: Neste escopo, a atribuição de papéis aplica-se apenas a esta entidade. Atualmente, o portal Azure não suporta atribuir uma função RBAC a um diretor de segurança neste nível. 
- **Hub de eventos**: A atribuição de função se aplica à entidade Event Hub e ao grupo de consumidores sob ele.
- **Namespace**: A atribuição de funções abrange toda a topologia dos Hubs de Eventos sob o namespace e para o grupo de consumidores associado a ele.
- **Grupo de recursos**: A atribuição de função se aplica a todos os recursos do Event Hubs sob o grupo de recursos.
- **Assinatura**: A atribuição de função se aplica a todos os recursos do Event Hubs em todos os grupos de recursos da assinatura.

> [!NOTE]
> - Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para se propagar. 
> - Este conteúdo se aplica tanto aos Hubs de Eventos quanto aos Hubs de Eventos para Apache Kafka. Para obter mais informações sobre o suporte a Event Hubs for Kafka, consulte [Event Hubs for Kafka - segurança e autenticação](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Para obter mais informações sobre como os papéis incorporados são [definidos,](../role-based-access-control/role-definitions.md#management-and-data-operations)consulte Entender definições de função . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar funções personalizadas para o Controle de Acesso baseado em funções do Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Exemplos
- [Amostras do Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Essas amostras usam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas você pode atualizá-la facilmente para usar a biblioteca mais recente **do Azure.Messaging.EventHubs.** Para mover a amostra do uso da biblioteca antiga para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para o Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Azure.Messaging.EventHubs amostras](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para usar a biblioteca Mais recente **do Azure.Messaging.EventHubs.**
- [Hubs de eventos para Kafka - Amostras de OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Próximas etapas
- Saiba como atribuir uma função built-in-RBAC a um diretor de segurança, consulte [Authenticate acesso aos recursos do Event Hubs usando o Azure Active Directory](authenticate-application.md).
- Aprenda [a criar funções personalizadas com o RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- [Saiba como usar o Azure Active Directory com eH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Veja os seguintes artigos relacionados:

- [Autenticar solicitações ao Azure Event Hubs a partir de um aplicativo usando o Azure Active Directory](authenticate-application.md)
- [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs](authenticate-managed-identity.md)
- [Autenticar solicitações ao Azure Event Hubs usando assinaturas de acesso compartilhadas](authenticate-shared-access-signature.md)
- [Autorize o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)
