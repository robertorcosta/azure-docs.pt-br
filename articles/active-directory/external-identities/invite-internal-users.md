---
title: Convidar usuários internos para colaboração B2B-Azure AD
description: Se você tiver contas de usuário internas para parceiros, distribuidores, fornecedores, fornecedores e outros convidados, poderá mudar para a colaboração B2B do Azure AD convidando-as para entrar com suas próprias credenciais externas ou logon. Use o PowerShell ou a API de convite Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87907958"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Convidar usuários internos para colaboração B2B

> [!NOTE]
> Convidar usuários internos para usar a colaboração B2B é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Antes da disponibilidade da colaboração B2B do Azure AD, as organizações podem colaborar com distribuidores, fornecedores, fornecedores e outros usuários convidados Configurando credenciais internas para eles. Se você tiver usuários convidados internos como esse, poderá convidá-los para usar a colaboração B2B para poder aproveitar os benefícios do Azure AD B2B. Seus usuários convidados B2B poderão usar suas próprias identidades e credenciais para entrar, e você não precisará manter senhas ou gerenciar ciclos de vida da conta.

O envio de um convite para uma conta interna existente permite reter a ID de objeto do usuário, o UPN, as associações de grupo e as atribuições de aplicativo. Você não precisa excluir e convidar o usuário novamente manualmente ou reatribuir recursos. Para convidar o usuário, você usará a API de convite para passar o objeto de usuário interno e o endereço de email do usuário convidado junto com o convite. Quando o usuário aceita o convite, o serviço B2B altera o objeto de usuário interno existente para um usuário B2B. No futuro, o usuário deve entrar nos serviços de recursos de nuvem usando suas credenciais B2B. Eles ainda podem usar suas credenciais internas para acessar recursos locais, mas você pode evitar isso redefinindo ou alterando a senha na conta interna.

> [!NOTE]
> O convite é unidirecional. Você pode convidar usuários internos para usar a colaboração B2B, mas não pode remover as credenciais B2B depois que elas são adicionadas. Para alterar o usuário de volta para um usuário somente interno, você precisará excluir o objeto de usuário e criar um novo.

Enquanto estiver em visualização pública, o método descrito neste artigo para convidar usuários internos para colaboração B2B não pode ser usado nessas instâncias:

- O usuário interno já foi atribuído a uma licença do Exchange.
- O usuário é de um domínio que está configurado para Federação direta em seu diretório.
- O usuário interno é uma conta somente em nuvem e sua conta principal não está no Azure AD.

Nesses casos, se o usuário interno precisar ser alterado para um usuário B2B, você deverá excluir a conta interna e enviar ao usuário um convite para colaboração B2B.

**Usuários sincronizados no local**: para contas de usuário que são sincronizadas entre o local e a nuvem, o diretório local permanece a fonte de autoridade depois que eles são convidados a usar a colaboração B2B. As alterações feitas na conta local serão sincronizadas com a conta de nuvem, incluindo a desabilitação ou a exclusão da conta. Portanto, você não pode impedir que o usuário entre em sua conta local enquanto mantém sua conta de nuvem simplesmente excluindo a conta local. Em vez disso, você pode definir a senha da conta local para um GUID aleatório ou outro valor desconhecido.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Como convidar usuários internos para colaboração B2B

Você pode usar o PowerShell ou a API de convite para enviar um convite B2B para o usuário interno. Verifique se o endereço de email que você deseja usar para o convite está definido como o endereço de email externo no objeto de usuário interno.

- Para um usuário somente em nuvem, use o endereço de email na propriedade User. OtherMails para o convite.
- Para um usuário sincronizado no local, você deve usar o valor na propriedade User. mail para o convite.
- O domínio na propriedade mail do usuário deve corresponder à conta que está usando para entrar. Caso contrário, alguns serviços, como as equipes, não poderão autenticar o usuário.

Por padrão, o convite enviará ao usuário um email informando que eles foram convidados, mas você poderá suprimir esse email e enviar seu próprio.

> [!NOTE]
> Para enviar seu próprio email ou outra comunicação, você pode usar New-AzureADMSInvitation com-SendInvitationMessage: $false para convidar usuários silenciosamente e enviar sua própria mensagem de email para o usuário convertido. Consulte [personalização e API de colaboração B2B do Azure ad](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Usar o PowerShell para enviar um convite B2B

Use o seguinte comando para convidar o usuário para a colaboração B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Usar a API de convite para enviar um convite B2B

O exemplo a seguir ilustra como chamar a API de convite para convidar um usuário interno como um usuário B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

A resposta à API é a mesma resposta que você obtém ao convidar um novo usuário convidado para o diretório.

## <a name="next-steps"></a>Próximas etapas

- [Resgate de convite de colaboração B2B](redemption-experience.md)
