---
title: Autorizar o acesso aos Hubs de Eventos do Azure
description: Este artigo fornece informações sobre diferentes opções para autorizar o acesso aos recursos do Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521303"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizar o acesso aos Hubs de Eventos do Azure
Toda vez que você publica ou consome eventos/dados de um hub de eventos, seu cliente está tentando acessar os recursos do Event Hubs. Toda solicitação a um recurso seguro deve ser autorizada para que o serviço possa garantir que o cliente tenha as permissões necessárias para publicar/consumir os dados. 

O Azure Event Hubs oferece as seguintes opções para autorizar o acesso a recursos seguros:

- Azure Active Directory
- Assinatura de acesso compartilhado

> [!NOTE]
> Este artigo se aplica tanto aos cenários Event Hubs quanto [Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Active Directory (Azure AD) para os recursos do Event Hubs fornece o Controle de Acesso Baseado em Papéis (RBAC) para controle fino sobre o acesso de um cliente aos recursos. Você pode usar o RBAC (Role-based Access Control, controle de acesso baseado em função) para conceder permissões ao principal de segurança, que pode ser um usuário, um grupo ou um diretor de serviço de aplicativo. O principal de segurança é autenticado pelo Azure AD para retornar um token OAuth 2.0. O token pode ser usado para autorizar uma solicitação para acessar um recurso do Event Hubs.

Para obter mais informações sobre autenticação com o Azure AD, consulte os seguintes artigos:

- [Autenticar solicitações ao Azure Event Hubs usando o Azure Active Directory](authenticate-application.md)
- [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado 
As assinaturas de acesso compartilhado (SAS) para os recursos do Event Hubs fornecem acesso limitado delegado aos recursos do Event Hubs. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões que concede fornece flexibilidade no gerenciamento de recursos. Para obter mais informações, consulte [Authenticate usando assinaturas de acesso compartilhado (SAS)](authenticate-shared-access-signature.md). 

Autorizar usuários ou aplicativos usando um token OAuth 2.0 devolvido pelo Azure AD proporciona maior segurança e facilidade de uso sobre assinaturas de acesso compartilhado (SAS). Com o Azure AD, não há necessidade de armazenar os tokens de acesso com seu código e riscos potenciais vulnerabilidades de segurança. Embora você possa continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso fino aos recursos do Event Hubs, o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar em revogar um SAS comprometido. 

Por padrão, todos os recursos do Event Hubs são protegidos e estão disponíveis apenas para o proprietário da conta. Embora você possa usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes acesso aos recursos do Event Hub. A Microsoft recomenda o uso do Azure AD quando possível para máxima segurança e facilidade de uso.

Para obter mais informações sobre a autorização usando o SAS, consulte [Autorizando o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Próximas etapas
- Revise [as amostras de RBAC publicadas](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) em nosso repositório gitHub. 
- Veja os artigos a seguir:
    - [Autenticar solicitações ao Azure Event Hubs a partir de um aplicativo usando o Azure Active Directory](authenticate-application.md)
    - [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs](authenticate-managed-identity.md)
    - [Autenticar solicitações ao Azure Event Hubs usando assinaturas de acesso compartilhadas](authenticate-shared-access-signature.md)
    - [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorize o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

