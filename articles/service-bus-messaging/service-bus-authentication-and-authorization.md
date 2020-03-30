---
title: Autenticação e Autorização do Barramento de Serviço do Azure | Microsoft Docs
description: Autentique aplicativos no Barramento de Serviço com a autenticação SAS (Assinatura de Acesso Compartilhado).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013240"
---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Barramento de Serviço

Os aplicativos obtêm acesso aos recursos do Barramento de Serviço do Azure usando a autenticação de token SAS (Assinatura de Acesso Compartilhado). Com o SAS, aplicativos apresentam ao Barramento de Serviço um token que foi assinado com uma chave simétrica conhecida tanto pelo emissor do token quanto pelo Barramento de Serviço (portanto, “compartilhada”) e essa chave é diretamente associada a uma regra que concede direitos de acesso específicos, como a permissão para receber/escutar ou enviar mensagens. As regras de SAS são configuradas no namespace ou diretamente em entidades, como uma fila ou um tópico, permitindo um controle de acesso refinado.

Os tokens SAS podem ser gerados por um cliente do Barramento de Serviço diretamente ou por algum ponto de extremidade intermediário emissor de token com o qual o cliente interage. Por exemplo, um sistema pode exigir que o cliente chame um ponto de extremidade de serviço Web protegido por autorização do Active Directory para provar sua identidade e seus direitos de acesso ao sistema, e o serviço Web retorna em seguida o token do Barramento de Serviço apropriado. Esse token SAS pode ser gerado com facilidade usando o provedor de token do Barramento de Serviço incluído no SDK do Azure. 

> [!IMPORTANT]
> Se você está usando o Controle de Acesso do Azure Active Directory (também conhecido como Serviço de Controle de Acesso ou ACS) com o Barramento de Serviço, observe que o suporte para esse método agora é limitado e que você deve migrar seu aplicativo para usar o SAS. Para obter mais informações, consulte [esta postagem no blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) e [este artigo](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
A integração do Azure Active Directory (Azure AD) para recursos de Bus de Serviço fornece controle de acesso baseado em função (RBAC) para controle fino sobre o acesso de um cliente aos recursos. Você pode usar o RBAC (Role-based Access Control, controle de acesso baseado em função) para conceder permissões ao principal de segurança, que pode ser um usuário, um grupo ou um diretor de serviço de aplicativo. O principal de segurança é autenticado pelo Azure AD para retornar um token OAuth 2.0. O token pode ser usado para autorizar uma solicitação para acessar um recurso de Service Bus (fila, tópico, etc.).

Para obter mais informações sobre autenticação com o Azure AD, consulte os seguintes artigos:

- [Autenticar com identidades gerenciadas](service-bus-managed-service-identity.md)
- [Autenticar a partir de um aplicativo](authenticate-application.md)

> [!IMPORTANT]
> Autorizar usuários ou aplicativos usando o token OAuth 2.0 devolvido pelo Azure AD proporciona maior segurança e facilidade de uso sobre assinaturas de acesso compartilhado (SAS). Com o Azure AD, não há necessidade de armazenar os tokens em seu código e arriscar possíveis vulnerabilidades de segurança. Recomendamos que você use o Azure AD com seus aplicativos de Ônibus de Serviço do Azure quando possível. 


## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado
A [autenticação SAS](service-bus-sas.md) permite que você conceda a um usuário o acesso aos recursos do Barramento de Serviço, com direitos específicos. A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace do Barramento de Serviço. A chave se aplica a todas as entidades de mensagens nesse namespace. Também é possível configurar chaves em tópicos e filas do Barramento de Serviço. Também há suporte para SAS na [Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace, uma fila ou um tópico. Essa regra consiste nos seguintes elementos:

* *KeyName*: identifica a regra.
* *PrimaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey*: uma chave de criptografia usada para assinar/validar tokens SAS.
* *Rights*: representa a coleção de direitos de **Escuta**, **Envio** ou **Gerenciamento** concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a tecla correspondente. Configure até 12 regras de autorização como essas em um namespace, uma fila ou um tópico do Barramento de Serviço. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="next-steps"></a>Próximas etapas

- Continue lendo [Autenticação de Barramento de Serviço com Assinaturas de Acesso Compartilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.
- Como [migrar da autorização do ACS (Controle de Acesso do Azure Active Directory) para a Assinatura de Acesso Compartilhado](service-bus-migrate-acs-sas.md).
- [Alterações em namespaces habilitados para ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Para informações correspondentes sobre a autenticação e autorização da Retransmissão do Azure, confira [Autenticação e autorização da Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md). 

