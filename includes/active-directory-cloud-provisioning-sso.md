---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504346"
---
## <a name="steps-to-enable-single-sign-on"></a>Etapas para habilitar o cadarção único
O provisionamento em nuvem funciona com o Single Sign-on.  Atualmente não há uma opção para ativar o SSO quando o agente está instalado, no entanto, você pode usar as etapas abaixo para ativar o SSO e usá-lo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Passo 1: Baixar e extrair arquivos do Azure AD Connect
1.  Primeiro, baixe a versão mais recente do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Abra um prompt de comando usando privilégios administrativos e navegue até o msi que você acabou de baixar.
3.  Execute o seguinte:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Alterar filepath e extractfolder para corresponder ao caminho do arquivo e ao nome da pasta de extração.  O conteúdo deve agora estar na pasta de extração.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Passo 2: Importe o módulo SSO PowerShell sem costura

1. Baixe e [instale o Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 3: Obtenha a lista de florestas de diretório ativo nas quais o SSO sem emenda foi ativado

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as suas credenciais de Administrador global do locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do Active Directory (veja a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Etapa 4: Habilitar o SSO Contínuo para cada floresta do Active Directory

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de administrador de domínio da floresta do Active Directory pretendida.

   > [!NOTE]
   >As credenciais de administrador de domínio devem ser inseridas no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o Controlador de Domínio do Administrador de Domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo Usuários Protegidos. Se assim for, a operação falhará.

2. Repita as etapas anteriores para cada floresta do Active Directory onde você configurou o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Etapa 5. Habilitar o recurso em seu locatário

Para habilitar o recurso no locatário, chame `Enable-AzureADSSO -Enable $true`.
