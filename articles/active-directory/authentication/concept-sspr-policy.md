---
title: Políticas de redefinição de senha por autoatendimento – Azure Active Directory
description: Configurar as opções da política de redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263927"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de senha do Active Directory do Azure

Este artigo descreve as políticas de senha e os requisitos de complexidade associados a contas de usuário no locatário do Azure AD (Azure Active Directory).

## <a name="administrator-reset-policy-differences"></a>Diferenças da política de redefinição de senha do administrador

**A Microsoft impõe uma política de redefinição de senha forte de *duas portas* padrão para qualquer função de administrador do Azure**; essa política pode ser diferente daquela que você definiu para os usuários e não pode ser alterada. Sempre teste a funcionalidade de redefinição de senha como um usuário sem funções de administrador do Azure atribuídas.

Com uma política de duas portas, os **administradores não podem usar perguntas de segurança**.

A política de duas portas requer dois tipos de dados de autenticação, como um **endereço de email**, **aplicativo autenticador** ou um **número de telefone**. Uma política de duas portas aplica-se nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de assistência técnica
  * Administrador de suporte a serviço
  * Administrador de cobrança
  * Suporte de camada 1 do parceiro
  * Suporte de camada 2 do parceiro
  * Administrador do Exchange
  * Administrador do Skype for Business
  * Administrador de usuários
  * Gravadores de diretório
  * Administrador global ou administrador da empresa
  * Administrador do SharePoint
  * Administrador de conformidade
  * Administrador de aplicativos
  * Administrador de segurança
  * Administrador de função com privilégios
  * Administrador do Intune
  * Administrador de serviços de Proxy do aplicativo
  * Administrador do Dynamics 365
  * Administrador de serviços do Power BI
  * Administrador de autenticação
  * Administrador de autenticação privilegiada

* Caso tenham se passado 30 dias decorridos de uma assinatura de avaliação; ou
* Um domínio personalizado foi configurado para seu locatário do Azure AD, como *contoso.com*; or
* O Azure AD Connect está sincronizando identidades do seu diretório local

### <a name="exceptions"></a>Exceções

Uma política de duas portas requer um tipo de dados de autenticação, como um endereço de email *ou* um número de telefone. Uma política de uma porta aplica-se nas seguintes circunstâncias:

* Ela esteja dentro dos primeiros 30 dias de uma assinatura; ou
* Um domínio personalizado não foi configurado para seu locatário do Azure AD, portanto, está usando o padrão * *. onmicrosoft.com*. Observe que o domínio padrão * *. onmicrosoft.com* não é recomendado para uso em produção; e
* O Azure AD Connect não está sincronizando identidades

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no Azure AD deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado à respectiva conta. A tabela a seguir descreve as políticas que aplicam-se a ambas as contas de usuário locais do Active Directory que são sincronizadas na nuvem e a contas de usuário somente em nuvem:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere "\@\" que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto "." imediatamente antes do símbolo "\@\"</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo de "\@\"</li><li>Pode haver até 48 caracteres após o símbolo de "\@\"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário na nuvem

A tabela a seguir descreve as configurações de política de senha aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ "();</li> <li>espaço em branco</li></ul> |
| Caracteres não permitidos | Caracteres Unicode. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três de quatro dos seguintes itens:<ul><li>Caracteres minúsculos.</li><li>Caracteres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (veja as restrições de senha acima).</li></ul></li></ul> |
| Duração da expiração da senha (duração máxima da senha) |<ul><li>Valor padrão: **90** dias.</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy` do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha (quando os usuários são notificados da expiração da senha) |<ul><li>Valor padrão: **14** dias (antes do vencimento de senha).</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiração de senha (Deixe a senha nunca expirar) |<ul><li>Valor padrão: **false** (indica que a senha tem uma data de validade).</li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet `Set-MsolUser`.</li></ul> |
| Histórico de alteração de senha | A última senha *não pode* ser usada novamente quando o usuário alterar uma senha. |
| Histórico de redefinição de senha | A última senha *pode* ser usada novamente quando o usuário redefine uma senha esquecida. |
| Bloqueio de conta | Após 10 tentativas malsucedidas de conexão com a senha incorreta, o usuário será bloqueado por um minuto. Quanto mais tentativas de conexão com senha incorreta, por mais tempo o usuário ficará bloqueado. [O bloqueio inteligente](howto-password-smart-lockout.md) rastreia os últimos três hashes de senha incorreta para evitar o incremento do contador de bloqueio para a mesma senha. Se alguém inserir a mesma senha incorreta várias vezes, esse comportamento não causará o bloqueio da conta. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de senha no Azure AD

Um administrador global ou administrador de usuário para um serviço de nuvem da Microsoft pode usar o Módulo Microsoft Azure AD para Windows PowerShell para definir senhas de usuário para não expirar. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar. 

Esta instrução se aplica a outros provedores, como o Intune e o Office 365, que também dependem do Azure AD para serviços de identidade e diretório. A expiração da senha é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para saber mais sobre a sincronização de diretório, confira [Conectar AD ao Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar políticas de senha usando o PowerShell

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Verificar a política de expiração de uma senha

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o UPN (por exemplo, *abril\@contoso.onmicrosoft.com*) ou a ID de usuário do usuário que você deseja verificar:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Para ver a configuração a **senha nunca expira** para todos os usuários, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que a senha expire, execute o seguinte cmdlet usando o UPN ou a ID de usuário do usuário:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Para definir as senhas de todos os usuários na organização para que eles expirem, use o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para nunca expirar, execute o seguinte cmdlet usando o UPN ou a ID de usuário do usuário:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Para definir as senhas de todos os usuários de uma organização para nunca expirar, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Senhas definidas como `-PasswordPolicies DisablePasswordExpiration` ainda expiram com base no atributo `pwdLastSet`. Se você definir as senhas de usuário para nunca expirar e, então, mais de 90 dias depois, as senhas expiram. Com base no atributo `pwdLastSet`, se você alterar a expiração para `-PasswordPolicies None`, todas as senhas que tenham um `pwdLastSet` com idade acima de 90 dias exigirão que o usuário as altere na próxima vez que entrarem. Essa alteração pode afetar um grande número de usuários.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure AD:

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)
