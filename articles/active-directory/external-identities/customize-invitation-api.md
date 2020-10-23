---
title: API de colaboração B2B e personalização-Azure Active Directory
description: A colaboração B2B do Azure Active Directory dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7cbcdb4b947e4b45a5473dc0f9f0252b5ad1d5c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442041"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API e personalização da colaboração B2B do Azure Active Directory

Muitos clientes nos disseram que desejam personalizar o processo de convite de forma que funcione melhor para suas organizações. Com nossa API, você pode fazer exatamente isso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Recursos da API de convite

A API oferece os seguintes recursos:

1. Convidar um usuário com *qualquer* endereço de email.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalizar onde você deseja que os usuários cheguem depois de aceitar o convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Optar por enviar o email de convite padrão por nosso intermédio

    ```
    "sendInvitationMessage": true
    ```

   com uma mensagem para o destinatário que você pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E escolher copiar (cc:) as pessoas que você deseja manter no loop sobre o seu convite a esse colaborador.

5. Ou personalizar completamente seu convite e o fluxo de integração ao optar por não enviar notificações por meio do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   Nesse caso, você obtém novamente uma URL de resgate da API, que pode ser inserida em um modelo de email, mensagem instantânea ou outro método de distribuição de sua escolha.

6. Por fim, se você for administrador, poderá convidar o usuário como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização

A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>Modo Aplicativo + Usuário

Nesse modo, a pessoa que está usando a API precisa ter as permissões para criar convites de B2B.

### <a name="app-only-mode"></a>Modo Somente aplicativo

No contexto somente aplicativo, o aplicativo precisa do escopo User.Invite.All para que o convite tenha êxito.

Para obter mais informações, consulte: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

You can use PowerShell to add and invite external users to an organization easily. Crie um convite usando o cmdlet:

```powershell
New-AzureADMSInvitation
```

É possível usar as seguintes opções:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Status do convite

Depois que você envia um convite por um usuário externo, você pode usar o **Get-AzureADUser** cmdlet para ver se eles aceitou-lo. As seguintes propriedades de Get-AzureADUser são preenchidas quando um usuário externo é enviado um convite:

* **UserState** indica se o convite é **PendingAcceptance** ou **Aceito**.
* **UserStateChangedOn** mostra o registro de data e hora da última alteração na propriedade **UserState**.

Você pode usar o **filtro** opção para filtrar os resultados por **UserState**. O exemplo a seguir mostra como filtrar resultados para mostrar apenas os usuários que têm um convite pendente. O exemplo também mostra a **Format-List** opção, que permite que você especifique as propriedades a serem exibidas. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Verifique se você possui a versão mais recente do módulo do PowerShell do AzureAD ou do módulo PowerShell do AzureADPreview. 

## <a name="see-also"></a>Confira também

Confira a referência da API de convite em [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) .

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Os elementos do email de convite para colaboração B2B](invitation-email-elements.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](add-user-without-invite.md)