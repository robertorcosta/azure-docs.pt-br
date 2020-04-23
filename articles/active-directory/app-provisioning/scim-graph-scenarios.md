---
title: Usando o SCIM, o Microsoft Graph e o serviço de provisionamento Azure AD para prover usuários e enriquecer seu aplicativo com os dados necessários | Microsoft Docs
description: Usando o SCIM e o Microsoft Graph juntos para provisionar os usuários e enriquecer seu aplicativo com os dados necessários .
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087615"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Usando o SCIM e o Microsoft Graph juntos para provisionar os usuários e enriquecer seu aplicativo com os dados necessários

**Público-alvo:** Este documento é direcionado para aplicativos de construção de desenvolvedores integrados com o Azure AD. Para outros que procuram integrar um aplicativo existente, como Zoom, ServiceNow e DropBox, você pode pular isso e rever os [tutoriais](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)específicos do aplicativo . 

**Cenários comuns**

> [!div class="checklist"]
> * Crie automaticamente usuários no meu aplicativo
> * Remova automaticamente os usuários do meu aplicativo quando eles não devem ter mais acesso
> * Integre meu aplicativo com vários provedores de identidade para provisionamento
> * Enriqueça meu aplicativo com dados de serviços da Microsoft, como Sharepoint, Outlook e Office.
> * Crie, atualize e exclua automaticamente usuários e grupos no Azure AD e Active Directory

![Árvore de decisão do Gráfico SCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Cenário 1: Crie automaticamente usuários no meu aplicativo
Hoje, os admins de TI criam manualmente contas de usuário no meu aplicativo cada vez que alguém precisa acessar ou carregar periodicamente arquivos CSV. O processo é demorado para os clientes e retarda a adoção do meu aplicativo. Tudo o que preciso é de informações básicas [do usuário,](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) como nome, e-mail e userPrincipalName para criar um usuário. Além disso, meus clientes usam vários IdPs e eu não tenho recursos para manter um mecanismo de sincronização e integrações personalizadas com cada IdP. 

**Recomendação**: Suporte a um ponto final compatível com SCIM [/Usuários.](https://aka.ms/scimreferencecode) Seus clientes poderão usar facilmente esse ponto final para se integrar ao serviço de provisionamento Ad do Azure e criar automaticamente contas de usuário quando precisarem de acesso. Você pode construir o ponto final uma vez e ele será compatível com todos os IdPs, sem ter que manter um mecanismo de sincronização. Confira abaixo o exemplo de solicitação de como um usuário seria criado.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Cenário 2: Remova automaticamente os usuários do meu aplicativo
Os clientes que usam meu aplicativo são focados em segurança e têm requisitos de governança para remover contas quando os funcionários não precisam mais delas. Como posso automatizar o deprovisionamento da minha aplicação?

**Recomendação:** Suporte a um ponto final compatível com SCIM /Usuários. O serviço de provisionamento Azure AD enviará solicitações para desativar e excluir quando o usuário não tiver mais acesso. Recomendamos o suporte tanto para desativar quanto excluir usuários. Veja os exemplos abaixo para saber como é uma solicitação de desabilitação e exclusão. 

Desabilitar usuário
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Excluir usuário
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Cenário 3: Automatize a gestão de membros de grupos no meu aplicativo
Meu aplicativo conta com grupos para acesso a vários recursos, e os clientes querem reutilizar os grupos que eles têm no Azure AD. Como posso importar grupos do Azure AD e mantê-los atualizados à medida que as adesões mudam?  

**Recomendação:** Suporte a um [ponto final](https://aka.ms/scimreferencecode)compatível com SCIM /Grupos . O serviço de provisionamento Azure AD cuidará da criação de grupos e do gerenciamento de atualizações de membros em seu aplicativo. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Cenário 4: Enriqueça meu aplicativo com dados de serviços da Microsoft, como Teams, Outlook e OneDrive.
Meu aplicativo é incorporado ao Microsoft Teams e conta com dados de mensagens. Além disso, armazenamos arquivos para usuários no OneDrive. Como posso enriquecer meu aplicativo com os dados desses serviços e em toda a Microsoft?

**Recomendação:** O [Microsoft Graph](https://docs.microsoft.com/graph/) é o seu ponto de entrada para acessar os dados da Microsoft. Cada carga de trabalho expõe ASTs com os dados que você precisa. O gráfico da Microsoft pode ser usado juntamente com o [provisionamento SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) para os cenários acima. Você pode usar o SCIM para provisionar atributos básicos do usuário em seu aplicativo enquanto liga para o gráfico para obter quaisquer outros dados que você precisa. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Cenário 5: Acompanhe as mudanças nos serviços da Microsoft, como Teams, Outlook e Azure AD.
Preciso ser capaz de acompanhar as mudanças nas mensagens do Teams e outlook e reagir a elas em tempo real. Como posso fazer com que essas mudanças sejam empurradas para a minha aplicação?

**Recomendação:** O Microsoft Graph fornece [notificações de alterações](https://docs.microsoft.com/graph/webhooks) e rastreamento de alterações para vários recursos. Observe as seguintes limitações das notificações de alteração:
- Se um receptor de evento reconhecer um evento, mas não agir sobre ele por qualquer motivo, o evento pode ser perdido
- Se um receptor de evento reconhecer um evento, mas não agir sobre ele por qualquer motivo, o evento pode ser perdido
- Notificações de alteração nem sempre contêm os [dados de recursos](https://docs.microsoft.com/graph/webhooks-with-resource-data) Pelas razões acima, os desenvolvedores geralmente usam notificações de alteração, juntamente com o rastreamento de alterações para cenários de sincronização. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Cenário 6: Provisão de usuários e grupos no Azure AD.
Meu aplicativo cria informações sobre um usuário que os clientes precisam no Azure AD. Isso pode ser um aplicativo de RH do que gerencia a contratação, um aplicativo de comunicação que cria números de telefone para os usuários, ou algum outro aplicativo que gera dados que seriam valiosos no Azure AD. Como preencher o registro de usuário no Azure AD com esses dados? 

**Recomendação** O gráfico da Microsoft expõe /Usuários e /Grupos pontos finais com os que você pode integrar hoje para provisionar os usuários no Azure AD. Observe que o Azure Active Directory não suporta escrever esses usuários de volta ao Active Directory. 

> [!NOTE]
> A Microsoft tem um serviço de provisionamento que coleta dados de aplicativos de RH, como Workday e SuccessFactors. Essas integrações são construídas e gerenciadas pela Microsoft. Para embarcar em um novo aplicativo de RH para o nosso serviço, você pode solicitá-lo no [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Artigos relacionados

- [Revise a documentação do Microsoft Graph de sincronização](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrando um aplicativo SCIM personalizado com o Azure AD](use-scim-to-provision-users-and-groups.md)
