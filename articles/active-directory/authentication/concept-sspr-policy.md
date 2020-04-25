---
title: Políticas de redefinição de senha por autoatendimento – Azure Active Directory
description: Saiba mais sobre as diferentes opções de política de redefinição de senha de autoatendimento Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8b6d08dd2073de80ac0f7fd08f510d9cda80545
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143231"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de redefinição de senha de autoatendimento no Azure Active Directory

Este artigo descreve as políticas de senha e os requisitos de complexidade associados a contas de usuário no locatário do Azure AD (Azure Active Directory).

## <a name="administrator-reset-policy-differences"></a>Diferenças da política de redefinição de senha do administrador

**A Microsoft impõe uma política padrão forte de redefinição de senha de *dois portão* para qualquer função de administrador do Azure**. Essa política pode ser diferente da que você definiu para os usuários e essa política não pode ser alterada. Sempre teste a funcionalidade de redefinição de senha como um usuário sem funções de administrador do Azure atribuídas.

Com uma política de duas portas, **os administradores não têm a capacidade de usar perguntas de segurança**.

A política de duas portas requer dois tipos de dados de autenticação, como um *endereço de email*, *aplicativo autenticador* ou um *número de telefone*. Uma política de duas portas aplica-se nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de assistência técnica
  * Administrador de suporte a serviço
  * Administrador de cobrança
  * Suporte de camada 1 do parceiro
  * Suporte de camada 2 do parceiro
  * Administradores do Exchange
  * Administrador do Skype for Business
  * Administrador do usuário
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

Uma política de duas portas requer um tipo de dados de autenticação, como um endereço de email ou um número de telefone. Uma política de uma porta aplica-se nas seguintes circunstâncias:

* Ela esteja dentro dos primeiros 30 dias de uma assinatura; ou
* Um domínio personalizado não foi configurado para seu locatário do Azure AD, portanto, está usando o padrão **. onmicrosoft.com*. O domínio padrão **. onmicrosoft.com* não é recomendado para uso em produção; e
* O Azure AD Connect não está sincronizando identidades

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no Azure AD deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado à respectiva conta. A tabela a seguir descreve as políticas que se aplicam a contas de usuário Active Directory Domain Services locais que são sincronizadas para a nuvem e para contas de usuário somente em nuvem:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere "\@\" que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto "." imediatamente antes do símbolo "\@\"</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo de "\@\"</li><li>Pode haver até 48 caracteres após o símbolo de "\@\"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário na nuvem

A tabela a seguir descreve as configurações de política de senha aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \: ',. ? / \`~ " ( ) ;</li> <li>espaço em branco</li></ul> |
| Caracteres não permitidos | Caracteres Unicode. |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 256 caracteres.</li><li>Requer três de quatro dos seguintes itens:<ul><li>Caracteres minúsculos.</li><li>Caracteres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (veja as restrições de senha acima).</li></ul></li></ul> |
| Duração da expiração da senha (duração máxima da senha) |<ul><li>Valor padrão: **90** dias.</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy` do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha (quando os usuários são notificados da expiração da senha) |<ul><li>Valor padrão: **14** dias (antes de a senha expirar).</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiração de senha (permitir que as senhas nunca expirem) |<ul><li>Valor padrão: **false** (indica que a senha tem uma data de validade).</li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet `Set-MsolUser`.</li></ul> |
| Histórico de alteração de senha | A última senha *não pode* ser usada novamente quando o usuário alterar uma senha. |
| Histórico de redefinição de senha | A última senha *pode* ser usada novamente quando o usuário redefine uma senha esquecida. |
| Bloqueio de conta | Após 10 tentativas malsucedidas de conexão com a senha incorreta, o usuário será bloqueado por um minuto. Quanto mais tentativas de conexão com senha incorreta, por mais tempo o usuário ficará bloqueado. O [bloqueio inteligente](howto-password-smart-lockout.md) rastreia os três últimos hashes de senha inválidos para evitar incrementar o contador de bloqueios para a mesma senha. Se alguém inserir a mesma senha inválida várias vezes, esse comportamento não fará com que a conta seja bloqueada. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de senha no Azure AD

Um administrador *global* ou *administrador de usuário* para um serviço de nuvem da Microsoft pode usar o *módulo Microsoft Azure ad para Windows PowerShell* para definir senhas de usuário para não expirar. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar.

Esta instrução se aplica a outros provedores, como o Intune e o Office 365, que também dependem do Azure AD para serviços de identidade e diretório. A expiração da senha é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para saber mais sobre a sincronização de diretório, confira [Conectar AD ao Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar políticas de senha usando o PowerShell

Para começar, [Baixe e instale o módulo do PowerShell do Azure ad](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) e [Conecte-o ao seu locatário do Azure ad](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples). Depois que o módulo for instalado, use as etapas a seguir para configurar cada campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Verificar a política de expiração de uma senha

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o UPN (por exemplo, *abril de\@contoso.onmicrosoft.com*) ou a ID de usuário do usuário que você deseja verificar:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Para ver a configuração **Senha nunca expira** para todos os usuários, execute o seguinte cmdlet: 

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela expire, execute o seguinte cmdlet usando o UPN ou a ID do usuário: 

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: 

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de usuário ou administrador da empresa.
1. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela nunca expire, execute o seguinte cmdlet usando o UPN ou a ID do usuário: 

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Para definir as senhas de todos os usuários de uma organização para nunca expirar, execute o seguinte cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Senhas definidas como `-PasswordPolicies DisablePasswordExpiration` ainda expiram com base no atributo `pwdLastSet`. Com base no atributo `pwdLastSet`, se você alterar a expiração para `-PasswordPolicies None`, todas as senhas que tenham um `pwdLastSet` com idade acima de 90 dias exigirão que o usuário as altere na próxima vez que entrarem. Essa alteração pode afetar um grande número de usuários.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o SSPR, consulte [tutorial: permitir que os usuários desbloqueiem sua conta ou redefinam senhas usando Azure Active Directory redefinição de senha de autoatendimento](tutorial-enable-sspr.md).

Se você ou os usuários tiverem problemas com o SSPR, consulte [solucionar problemas de redefinição de senha de autoatendimento](active-directory-passwords-troubleshoot.md)
