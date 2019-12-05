---
title: Tutorial`:`Usar uma identidade gerenciada da VM do Windows para acessar o grafo do Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar a API do Azure AD Graph.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ef467adb8970d410404c151d0028ee4cda92b9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183015"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar a API do Azure AD Graph

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema de uma VM (máquina virtual) do Windows para acessar a API do Graph do Azure AD a fim de recuperar as respectivas associações a um grupo. As identidades gerenciadas para recursos do Azure são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código.  Neste tutorial, você consultará a associação da sua identidade da VM em grupos do Microsoft Azure AD. As informações de grupo geralmente são usadas em decisões de autorização, por exemplo. Nos bastidores, a identidade gerenciada da sua VM é representada por uma **Entidade de Serviço** no Microsoft Azure AD. Antes de fazer a consulta de grupo, adicione a entidade de serviço que representa a identidade da VM a um grupo no Azure AD. Você pode fazer isso usando o Azure PowerShell, o PowerShell do Azure AD ou a CLI do Azure.

> [!div class="checklist"]
> * Conecte-se ao AD do Azure
> * Adicione sua identidade de VM a um grupo no Azure AD 
> * Conceder à identidade da VM acesso ao Microsoft Azure AD Graph 
> * Obter um token de acesso usando a identidade da VM e usá-lo para chamar o Microsoft Azure AD Graph

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Para conceder um acesso de identidade da VM ao Microsoft Azure AD Graph, sua conta precisa receber a atribuição da função **Administrador Global** no Microsoft Azure AD.
- Instale o [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) mais recente, caso ainda não tenha feito isso. 

## <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure

Você precisa se conectar ao Microsoft Azure AD para atribuir a VM a um grupo, bem como conceder permissão à VM para recuperar suas associações de grupo. Use o cmdlet Connect-AzureAD diretamente ou com o parâmetro TenantId caso tenha vários locatários.

```powershell
Connect-AzureAD
```
OU
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Adicione sua identidade de VM a um grupo no Azure AD

Quando você habilitou a identidade gerenciada atribuída pelo sistema na VM do Windows, foi criada uma entidade de serviço no Microsoft Azure AD.  Agora você precisa adicionar a VM a um grupo.  O exemplo a seguir cria um novo grupo no Azure AD e adiciona a entidade de serviço da sua VM a ele:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Conceder à VM acesso à API do Graph do Azure AD

Usando a identidades gerenciadas para recursos do Azure, seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure AD. A API do Microsoft Azure AD Graph é compatível com a autenticação do Microsoft Azure AD. Nesta etapa, você concederá acesso à entidade de serviço da identidade da sua VM ao Microsoft Azure AD Graph para que ele possa consultar as associações de grupo. As entidades de serviço recebem acesso à Microsoft ou Azure AD Graph por meio de **Permissões de Aplicativo**. O tipo de permissão de aplicativo que você precisa conceder depende da entidade que você deseja acessar no MS ou Azure AD Graph.

Neste tutorial, você concederá à identidade da VM a capacidade de consultar associações de grupo usando a permissão de aplicativo ```Directory.Read.All```. Para conceder essa permissão, você precisará de uma conta de usuário que recebeu a atribuição da função de Administrador Global no Microsoft Azure AD. Normalmente, uma permissão de aplicativo é concedida visitando o registro do aplicativo no portal do Azure e adicionando a permissão nele. No entanto, identidades gerenciadas para recursos do Azure não registram objetos de aplicativo no Microsoft Azure AD, registrando apenas as entidades de serviço. Para registrar a permissão de aplicativo, você usará a ferramenta de linha de comando do Microsoft Azure AD PowerShell. 

Azure AD Graph:
- AppId da Entidade de Serviço (usado ao conceder permissão de aplicativo): 00000002-0000-0000-c000-000000000000
- ID do recurso (usada ao solicitar o token de acesso de identidades gerenciadas para recursos do Azure): https://graph.windows.net
- Referência de escopo de permissão: [Referência das permissões do grafo do Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Conceder permissões de aplicativo usando o PowerShell do Azure AD

Você precisará do Azure AD PowerShell para usar essa opção. Se ele não estiver instalado, [baixe a versão mais recente](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) antes de continuar.

1. Abra uma janela do PowerShell e conecte-se ao Azure AD:

   ```powershell
   Connect-AzureAD
   ```
   Para se conectar a um Azure Active Directory específico, use o parâmetro _TenantId_ da seguinte maneira:

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. Execute os seguintes comandos do PowerShell para atribuir a permissão de aplicativo ``Directory.Read.All`` à entidade de serviço que representa a identidade da sua VM.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   A saída do comando final deve ser semelhante a esta, retornando a ID da atribuição:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Se a chamada para `New-AzureAdServiceAppRoleAssignment` falhar com o erro `bad request, one or more properties are invalid`, talvez a permissão de aplicativo já tenha sido atribuída à entidade de serviço da identidade da VM. Você pode usar os seguintes comandos do PowerShell para verificar se a permissão de aplicativo já existe entre a identidade da sua VM e o Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Você pode usar os seguintes comandos do PowerShell para listar todas as permissões de aplicativo que foram concedidas ao Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Você pode usar os seguintes comandos do PowerShell para remover as permissões de aplicativo tem sido concedidas à identidade da VM para o Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Obter um token de acesso usando a identidade da VM para chamar o Azure AD Graph 

Para usar a identidade gerenciada atribuída ao sistema da VM para autenticação para o Azure AD Graph, você precisa fazer solicitações da VM.

1. No portal, navegue até **Máquinas Virtuais**, acesse a VM Windows e, na folha **Visão geral**, clique em **Conectar**.  
2. Insira seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows.
3. Agora que você criou uma Conexão de Área de Trabalho Remota com a máquina virtual, abra o PowerShell na sessão remota.  
4. Usando Invoke-WebRequest do PowerShell, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Converta a resposta de um objeto JSON para um objeto do PowerShell.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraia o token de acesso da resposta.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Usando a ID de objeto da entidade de serviço da sua identidade VM (você pode recuperar esse valor de variável declarada nas etapas anteriores: ``$ManagedIdentitiesServicePrincipal.ObjectId``), você pode consultar a API do Azure AD Graph para recuperar suas associações de grupo. Substitua `<OBJECT ID>` pela ID de Objeto da etapa anterior e <`ACCESS-TOKEN>` pelo token de acesso obtido anteriormente:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   A resposta contém o `Object ID` do grupo ao qual você adicionou a entidade de serviço da sua VM nas etapas anteriores.

   Resposta:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Microsoft Azure AD Graph.  Para saber mais sobre o Azure AD Graph, confira:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
