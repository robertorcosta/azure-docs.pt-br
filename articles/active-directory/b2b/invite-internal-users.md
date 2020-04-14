---
title: Convide usuários internos para a colaboração B2B - Azure AD
description: Se você tiver contas internas de usuários para parceiros, distribuidores, fornecedores, fornecedores e outros convidados, você pode mudar para a colaboração Azure AD B2B convidando-os a fazer login com suas próprias credenciais externas ou login. Use o PowerShell ou a API de convite do Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097de14a3451e8d352dceb17436ae8423aa06533
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265935"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Convide usuários internos para a colaboração B2B

|     |
| --- |
| Convidar usuários internos a usar a colaboração B2B é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Antes da disponibilidade da colaboração Azure AD B2B, as organizações podiam colaborar com distribuidores, fornecedores, fornecedores e outros usuários convidados, configurando credenciais internas para eles. Se você tem usuários convidados internos como este, você pode convidá-los a usar a colaboração B2B para que você possa tirar proveito dos benefícios do Azure AD B2B. Seus usuários convidados B2B poderão usar suas próprias identidades e credenciais para fazer login, e você não precisará manter senhas ou gerenciar ciclos de vida da conta.

O envio de um convite para uma conta interna existente permite que você retenha o ID de objeto, UPN, membros de grupo e atribuições de aplicativos desse usuário. Você não precisa excluir e reconvidar manualmente o usuário ou reatribuir recursos. Para convidar o usuário, você usará a API de convite para passar tanto o objeto do usuário interno quanto o endereço de e-mail do usuário convidado, juntamente com o convite. Quando o usuário aceita o convite, o serviço B2B altera o objeto de usuário interno existente para um usuário B2B. Daqui para frente, o usuário deve fazer login em serviços de recursos em nuvem usando suas credenciais B2B. Eles ainda podem usar suas credenciais internas para acessar os recursos do local, mas você pode impedir isso redefinindo ou alterando a senha na conta interna.

> [!NOTE]
> O convite é só de ida. Você pode convidar usuários internos a usar a colaboração B2B, mas não pode remover as credenciais B2B assim que elas forem adicionadas. Para alterar o usuário de volta para um usuário somente interno, você precisará excluir o objeto do usuário e criar um novo.

Enquanto na visualização pública, o método descrito neste artigo para convidar usuários internos à colaboração B2B não pode ser usado nessas instâncias:

- O usuário interno já recebeu uma licença do Exchange.
- O usuário é de um domínio configurado para federação direta em seu diretório.
- O usuário interno é uma conta somente na nuvem, e sua conta principal não está no Azure AD.

Nesses casos, se o usuário interno deve ser alterado para um usuário B2B, você deve excluir a conta interna e enviar ao usuário um convite para colaboração B2B.

**Usuários sincronizados no local**: Para contas de usuário sincronizadas entre o local e a nuvem, o diretório local continua sendo a fonte de autoridade depois que eles são convidados a usar a colaboração B2B. Quaisquer alterações que você fizer na conta local serão sincronizadas com a conta na nuvem, incluindo desativar ou excluir a conta. Portanto, você não pode impedir que o usuário faça login em sua conta no local, mantendo sua conta na nuvem simplesmente excluindo a conta local. Em vez disso, você pode definir a senha da conta no local para um GUID aleatório ou outro valor desconhecido.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Como convidar usuários internos para a colaboração B2B

Você pode usar o PowerShell ou a API de convite para enviar um convite B2B para o usuário interno. Certifique-se de que o endereço de e-mail que você deseja usar para o convite está definido como o endereço de e-mail externo no objeto interno do usuário.

- Para um usuário somente na nuvem, use o endereço de e-mail na propriedade User.OtherMails para obter o convite.
- Para um usuário sincronizado no local, você deve usar o valor na propriedade User.Mail para o convite.
- O domínio na propriedade Mail do usuário deve corresponder à conta que ele está usando para fazer login. Caso contrário, alguns serviços, como o Teams, não serão capazes de autenticar o usuário.

Por padrão, o convite enviará ao usuário um e-mail informando que eles foram convidados, mas você pode suprimir este e-mail e enviar o seu próprio.

> [!NOTE]
> Para enviar seu próprio e-mail ou outra comunicação, você pode usar o New-AzureADMSInvitation com -SendInvitationMessage:$false para convidar os usuários silenciosamente e, em seguida, enviar sua própria mensagem de e-mail para o usuário convertido. Consulte [a API de colaboração azure AD B2B e a personalização](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Use o PowerShell para enviar um convite B2B

Use o seguinte comando para convidar o usuário para a colaboração B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Use a API de convite para enviar um convite B2B

A amostra abaixo ilustra como chamar a API de convite para convidar um usuário interno como usuário B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>"",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>""
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {"id": "<<ID for the user you want to convert>>"}
}
```

A resposta à API é a mesma resposta que você recebe quando convida um novo usuário convidado para o diretório.

## <a name="next-steps"></a>Próximas etapas

- [Resgate de convite de colaboração B2B](redemption-experience.md)