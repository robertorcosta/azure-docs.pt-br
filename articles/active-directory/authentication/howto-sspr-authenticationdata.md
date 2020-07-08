---
title: Requisitos de dados do Azure AD SSPR-Azure Active Directory
description: Requisitos de dados para autoatendimento de redefinição de senha do Azure AD e como atendê-los
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652349"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implantar redefinição de senha sem exigir registro do usuário final

Para implantar a redefinição de senha de autoatendimento (SSPR) do Azure Active Directory (Azure AD), os dados de autenticação precisam estar presentes. Algumas organizações fazem com que seus usuários insiram seus próprios dados de autenticação. Outras organizações preferem sincronizar com os dados que já existem no Active Directory. Esses dados sincronizados são disponibilizados para o Azure AD e o SSPR sem a necessidade de interação do usuário se você atender aos seguintes requisitos:

* Formatar corretamente os dados em seu diretório local.
* Configurar o [Azure AD Connect usando as configurações expressas](../hybrid/how-to-connect-install-express.md).

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: +1 4255551234.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

## <a name="fields-populated"></a>Campos populados

Se você usar as configurações padrão no Azure AD Connect, serão realizados os seguintes mapeamentos:

| Active Directory local | Azure AD |
| --- | --- |
| telephoneNumber | Telefone comercial |
| Serviço Móvel | Telefone celular |

Depois que um usuário verifica seu número de telefone celular, o campo *telefone* em **autenticação informações de contato** no Azure ad também é preenchido com esse número.

## <a name="authentication-contact-info"></a>Informações de contato de autenticação

Na página **métodos de autenticação** de um usuário do Azure AD na portal do Azure, um administrador global pode definir manualmente as informações de contato de autenticação, conforme mostrado no seguinte exemplo de captura de tela:

![Informações de contato de autenticação em um usuário no Azure AD][Contact]

* Se o campo **telefone** for preenchido e o **telefone celular** estiver habilitado na política SSPR, o usuário verá esse número na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.
* O campo **telefone alternativo** não é usado para redefinição de senha.
* Se o campo de **email** for preenchido e o **email** estiver habilitado na política SSPR, o usuário verá esse email na página de registro de redefinição de senha e durante o fluxo de trabalho de redefinição de senha.
* Se o campo de **email alternativo** for preenchido e o **email** estiver habilitado na política SSPR, o usuário **não** verá esse email na página de registro de redefinição de senha, mas o verá durante o fluxo de trabalho de redefinição de senha.

## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas em seu locatário do Azure AD e podem ser acessadas somente por usuários pelo [Portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver, configurar nem modificar o conteúdo das perguntas e respostas dos outros usuários.

## <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra

Quando um usuário se registra, a página de registro define os seguintes campos:

* **Telefone de autenticação**
* **E-mail de autenticação**
* **Perguntas de segurança e respostas**

Se você forneceu um valor para **Celular** ou **Email Alternativo**, os usuários poderão usar esses valores imediatamente para redefinir as senhas, mesmo que não tenham se registrado no serviço. Além disso, os usuários visualizam esses valores ao se registrarem pela primeira vez, e os modificam, se for desejado. Depois que eles são registrados com êxito, esses valores são persistidos nos campos **telefone de autenticação** e **email de autenticação** , respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação usando o PowerShell

Os campos a seguir podem ser definidos usando o PowerShell:

* **Email alternativo**
* **Telefone celular**
* **Telefone comercial**: só poderá ser definido se não for sincronizar com um diretório local

### <a name="use-powershell-version-1"></a>Usar o PowerShell versão 1

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler dados de autenticação com o PowerShell versão 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de Telefone de Autenticação e Email de Autenticação

Para ler o **Telefone de Autenticação** e **Email de Autenticação** quando você usa o PowerShell versão 1, use os seguintes comandos:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usar o PowerShell versão 2

Para começar, é preciso [baixar e instalar o módulo PowerShell do Azure AD versão 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

Para instalar rapidamente de versões recentes do PowerShell que dão suporte ao Módulo de instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler dados de autenticação com o PowerShell versão 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Os administradores globais podem modificar informações de contato de autenticação de um usuário"
