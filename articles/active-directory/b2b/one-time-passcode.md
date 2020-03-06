---
title: Autenticação de senha única para usuários convidados B2B – Azure AD
description: Como usar a senha avulsa por email para autenticar os usuários convidados de B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376646"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Autenticação por senha avulsa por email (versão prévia)

|     |
| --- |
| A senha avulsa por email é a versão prévia pública de um recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como habilitar a autenticação de senha de uso único de email para usuários de convidado B2B. O recurso de senha avulsa por Email autentica os usuários convidados de B2B quando eles não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Com a autenticação por senha avulsa, não é necessário criar uma conta Microsoft. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada.

Esse recurso está atualmente disponível em versão prévia (confira [Aceitar a versão prévia](#opting-in-to-the-preview) abaixo). Após a versão prévia, esse recurso será ativado por padrão para todos os locatários.

> [!NOTE]
> Os usuários de senha descartável devem entrar usando um link que inclui o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>`, `https://portal.azure.com/<tenant id>` ou, no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. No momento, os usuários convidados não conseguem entrar usando pontos de extremidade sem contexto do locatário. Por exemplo, o uso de `https://myapps.microsoft.com`, `https://portal.azure.com` ou do ponto de extremidade comum das Equipes resultará em erro. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do usuário para usuários convidados com senha avulsa
Com a autenticação por senha avulsa, o usuário convidado pode resgatar seu convite clicando em um link direto ou usando o email de convite. Em ambos os casos, uma mensagem no navegador indica que um código será enviado ao endereço de email do usuário convidado. O usuário convidado seleciona **Enviar código**:
 
   ![Captura de tela mostrando o botão enviar código](media/one-time-passcode/otp-send-code.png)
 
Uma senha é enviada ao endereço de email do usuário. O usuário recupera a senha de email e insere-a na janela do navegador:
 
   ![Captura de tela mostrando a página inserir código](media/one-time-passcode/otp-enter-code.png)
 
O usuário convidado está agora autenticado e pode ver o recurso compartilhado ou continuar o processo de entrada. 

> [!NOTE]
> Senhas de uso único são válidas por 30 minutos. Após 30 minutos, essa senha avulsa específica não é mais válida e o usuário precisa solicitar uma nova. Sessões de usuário expiram após 24 horas. Após esse tempo, o usuário convidado recebe uma nova senha quando acessa o recurso. A expiração de sessão fornece maior segurança, especialmente quando um usuário convidado deixa a empresa ou não precisa mais de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando um usuário convidado obtém uma senha avulsa?

Quando um usuário convidado resgata um convite ou usa um link para um recurso que foi compartilhado consigo, ele recebe uma senha avulsa se:
- Ele não tem uma conta do Azure AD 
- Ele não tem uma conta Microsoft 
- O locatário que convida não configurou a federação do Google para usuários @gmail.com e @googlemail.com 

No momento do convite, não há nenhuma indicação de que o usuário que você está convidando usará a autenticação de senha avulsa. Mas quando o usuário convidado entrar, a autenticação de senha avulsa será o método de fallback se nenhum outro método de autenticação puder ser usado. 

Você pode exibir os usuários convidados que se autenticam com senhas de uso único no portal do Azure, acessando **Azure Active Directory** > **Relações organizacionais** > **Usuários de outras organizações**.

![Captura de tela mostrando um usuário de senha de uso único com o valor de origem de OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Quando um usuário resgatar uma senha avulsa e, posteriormente, obtém uma MSA, conta do Azure AD ou outra conta federada, ele continuará a ser autenticado usando uma senha avulsa. Se quiser atualizar o método de autenticação do usuário, você poderá excluir a conta de usuário convidado dele e convidá-lo novamente.

### <a name="example"></a>Exemplo
O usuário convidado alexdoe@gmail.com é convidado para a Fabrikam, que não tem a federação do Google configurada. Alex não tem uma conta Microsoft. Eles receberão uma senha de uso único para autenticação.

## <a name="opting-in-to-the-preview"></a>Aceitação da versão prévia 
Pode levar alguns minutos para que a ação de aceitação entre em vigor. Depois disso, somente os usuários recém-convidados que atenderem às condições acima usarão a autenticação de senha avulsa. Usuários convidados que anteriormente resgataram um convite continuarão a usar o mesmo método de autenticação.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Para aceitar usando o portal do Azure AD
1.  Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.
2.  No painel de navegação, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Relações Organizacionais**.
4.  Escolha a opção **Configurações**.
5.  Em **Habilitar Senha Avulsa por Email para convidados (Versão Prévia)** , selecione **Sim**.
 
### <a name="to-opt-in-using-powershell"></a>Para aceitar usando o PowerShell

Primeiro, você precisará instalar a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). Em seguida, você poderá determinar se as políticas de B2B já existem e executar os comandos apropriados.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Pré-requisito: instalar o módulo AzureADPreview mais recente
Primeiro, verifique quais módulos estão instalados. Abra o Windows PowerShell como usuário com privilégios elevados (Executar como administrador) e execute o seguinte comando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Se o módulo AzureADPreview for exibido sem uma mensagem que indica uma versão posterior, você estará pronto. Caso contrário, com base na saída, realize um destes procedimentos:

- Se nenhum resultado for retornado, execute o seguinte comando para instalar o módulo AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas o módulo AzureAD for exibido nos resultados, execute os comandos a seguir para instalar o módulo AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se somente o módulo AzureADPreview for exibido nos resultados, mas você receber uma mensagem que indica uma versão posterior, execute os comandos a seguir para atualizar o módulo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Talvez você receba um aviso de que está instalando o módulo de um repositório não confiável. Isso ocorrerá se você não tiver definido o repositório PSGallery como confiável anteriormente. Pressione **Y** para instalar o módulo.

#### <a name="check-for-existing-policies-and-opt-in"></a>Verificar as políticas existentes e aceitar

Em seguida, verifique se uma B2BManagementPolicy existe atualmente, executando o seguinte:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Se a saída for False, a política não existe no momento. Crie uma nova B2BManagementPolicy e aceite a versão prévia, executando o seguinte:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Se a saída for True, a política B2BManagementPolicy existe atualmente. Para atualizar a política e aceitar a versão prévia, execute o seguinte:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Recusar a versão prévia depois de aceitar
Pode levar alguns minutos para que a ação de recusa entre em vigor. Se você desativar a versão prévia, quaisquer usuários convidados que tiverem resgatado uma senha avulsa não poderão entrar. Você pode excluir o usuário convidado e convidar o usuário novamente para que ele possa entrar novamente usando outro método de autenticação.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Para desativar a versão prévia usando o portal do Azure AD
1.  Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.
2.  No painel de navegação, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Relações Organizacionais**.
4.  Escolha a opção **Configurações**.
5.  Em **Habilitar Senha Avulsa por Email para convidados (Versão Prévia)** , selecione **Não**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Para desativar a versão prévia usando o PowerShell
Instale o módulo AzureADPreview mais recente se você ainda não o tiver (consulte [pré-requisito: instalar o módulo AzureADPreview mais recente](#prerequisite-install-the-latest-azureadpreview-module) acima). Em seguida, verifique se a política de versão prévia de senha avulsa existe atualmente, executando o seguinte:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Se a saída for True, recuse a versão prévia, executando o seguinte:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

