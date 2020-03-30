---
title: Gerenciar políticas personalizadas com o PowerShell
titleSuffix: Azure AD B2C
description: Use o cmdlet PowerShell do Azure Active Directory (Azure AD) para gerenciamento programático de suas políticas personalizadas Azure AD B2C. Crie, leia, atualize e exclua políticas personalizadas com o PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187399"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Gerenciar políticas personalizadas azure AD B2C com o Azure PowerShell

O Azure PowerShell fornece vários cmdlets para gerenciamento de políticas personalizadas baseadas em linha de comando e script no seu inquilino Azure AD B2C. Saiba como usar o módulo Azure AD PowerShell para:

* Liste as políticas personalizadas em um inquilino Azure AD B2C
* Baixe uma apólice de um inquilino
* Atualize uma política existente substituindo seu conteúdo
* Envie uma nova política para o seu inquilino Azure AD B2C
* Exclua uma política personalizada de um inquilino

## <a name="prerequisites"></a>Pré-requisitos

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)e credenciais para um usuário no diretório com a função [de administrador de políticas b2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) enviadas ao seu inquilino
* [Azure AD PowerShell para **módulo de visualização** de gráficos](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Conecte a sessão PowerShell ao inquilino B2C

Para trabalhar com políticas personalizadas no seu inquilino Azure AD B2C, primeiro você precisa conectar sua sessão PowerShell ao inquilino usando o comando [Connect-AzureAD.][Connect-AzureAD]

Execute o seguinte comando, substituindo `{b2c-tenant-name}` com o nome do seu inquilino Azure AD B2C. Faça login com uma conta que é atribuída à função de Administrador de [Políticas do IEF B2C](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) no diretório.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Saída de comando exemplo mostrando uma entrada de login bem-sucedida:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Liste todas as políticas personalizadas no inquilino

A descoberta de políticas personalizadas permite que um administrador AD B2C do Azure revise, gerencie e adicione lógica de negócios às suas operações. Use o comando [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para retornar uma lista de IDs das políticas personalizadas em um inquilino Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Saída de comando exemplo:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Baixe uma política

Depois de revisar a lista de IDs de diretiva, você pode direcionar uma política específica com [get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para baixar seu conteúdo.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Neste exemplo, a política com *id B2C_1A_signup_signin* é baixada:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Para editar o conteúdo da diretiva localmente, canalize a saída de comando para um arquivo com o `-OutputFilePath` argumento e, em seguida, abra o arquivo em seu editor favorito.

Exemplo de comando enviando saída para um arquivo:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Atualize uma política existente

Depois de editar um arquivo de diretiva que você criou ou baixou, você pode publicar a diretiva atualizada no Azure AD B2C usando o comando [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Se você `Set-AzureADMSTrustFrameworkPolicy` emitir o comando com o ID de uma política que já existe no seu inquilino Azure AD B2C, o conteúdo dessa diretiva será substituído.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exemplo de comando:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Para obter exemplos adicionais, consulte a referência de comando [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

## <a name="upload-a-new-policy"></a>Faça upload de uma nova política

Quando você faz uma alteração em uma política personalizada que está em execução em produção, você pode querer publicar várias versões da política para cenários de teste de recuo ou A/B. Ou, você pode querer fazer uma cópia de uma política existente, modificá-la com algumas pequenas alterações e, em seguida, carregá-la como uma nova política para uso por um aplicativo diferente.

Use o comando [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] para carregar uma nova política:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exemplo de comando:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Exclua uma política personalizada

Para manter um ciclo de vida de operações limpas, recomendamos que você remova periodicamente políticas personalizadas não utilizadas. Por exemplo, você pode querer remover versões de diretivas antigas depois de executar uma migração para um novo conjunto de políticas e verificar a funcionalidade das novas políticas. Além disso, se você tentar publicar um conjunto de políticas personalizadas e receber um erro, pode fazer sentido remover as políticas que foram criadas como parte da versão falhada.

Use o comando [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] para excluir uma política do seu inquilino.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Exemplo de comando:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Upload de política de solução de problemas

Quando você tenta publicar uma nova diretiva personalizada ou atualizar uma política existente, a formatação xml inadequada e erros na cadeia de herança de arquivos de diretiva podem causar falhas de validação.

Por exemplo, aqui está uma tentativa de atualizar uma política com conteúdo que contém XML malformado (a saída é truncada para brevidade):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Para obter informações sobre a solução de problemas de políticas personalizadas, consulte ['Solução de problemas' Azure AD B2C políticas personalizadas e O Framework de Experiência de Identidade](active-directory-b2c-guide-troubleshooting-custom.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o uso do PowerShell para implantar políticas personalizadas como parte de um pipeline de integração contínua/entrega contínua (CI/CD), consulte [Implantar políticas personalizadas a partir de um pipeline Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
