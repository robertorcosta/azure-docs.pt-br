---
title: Criação do pool de host do windows virtual desktop - Azure
description: Como solucionar problemas e resolver problemas de inquilinos e pools de host durante a configuração de um ambiente de inquilino do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371589"
---
# <a name="tenant-and-host-pool-creation"></a>Criação do pool de host e de locatário

Este artigo abrange problemas durante a configuração inicial do inquilino do Windows Virtual Desktop e da infra-estrutura de pool de host de sessão relacionada.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirindo a imagem multi-sessão do Windows 10 Enterprise

Para usar a imagem multi-sessão do Windows 10 Enterprise, vá para o Azure Marketplace, selecione **Get Started** > **Microsoft Windows 10** > e [Windows 10 Enterprise para Desktops Virtuais, Versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Uma captura de tela de seleção do Windows 10 Enterprise para Desktops Virtuais, Versão 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criando o inquilino da Área de Trabalho Virtual do Windows

Esta seção cobre possíveis problemas ao criar o inquilino do Windows Virtual Desktop.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

![Captura de tela da janela PowerShell na qual um usuário não está autorizado a consultar o serviço de gerenciamento.](media/UserNotAuthorizedNewTenant.png)

Exemplo de erro bruto:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** O usuário que fez o seu signo não foi designado para o Papel TenantCreator em seu Diretório Ativo do Azure.

**Correção:** Siga as instruções em [Atribuir a função de aplicativo TenantCreator a um usuário no seu inquilino do Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Depois de seguir as instruções, você terá um usuário designado para a função TenantCreator.

![Captura de tela da função TenantCreator atribuída.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criando VMs de banco de mesa virtual do Windows

As VMs de host de sessão podem ser criadas de várias maneiras, mas a equipe do Windows Virtual Desktop só suporta problemas de provisionamento de VM relacionados à oferta do [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Para obter mais informações, consulte [Problemas usando o Windows Virtual Desktop - Provision a host pool Azure Marketplace oferece](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas usando o Windows Virtual Desktop – Provisão um pool de host a oferta do Azure Marketplace

O Windows Virtual Desktop – Provision a host pool template está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao usar o link do GitHub, a mensagem "Criar uma conta gratuita" é exibida

![Captura de tela para criar uma conta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Não há assinaturas ativas na conta usada para fazer login no Azure ou a conta usada não tem permissões para visualizar as assinaturas.

**Correção 1:** Faça login com uma conta que tenha acesso ao contribuinte (no mínimo) à assinatura onde as VMs de host de sessão serão implantadas.

**Causa 2:** A assinatura que está sendo usada faz parte de um inquilino do Microsoft Cloud Service Provider (CSP).

**Correção 2:** Vá até a localização do GitHub para **criar e provisionar o novo pool de hosts do Windows Virtual Desktop** e siga estas instruções:

1. Clique com o botão direito do mouse em **Implantar no Azure** e selecione **Copiar endereço de link**.
2. Abra **o Bloco de Notas** e cole o link.
3. Antes do caractere #, insira o nome do inquilino final do cliente CSP.
4. Abra o novo link em um navegador e o portal Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Erro: Você recebe erro "implantação de modelo não é válida"

![Captura de tela de "implantação de modelo ... não é válido" erro](media/troubleshooting-marketplace-validation-error-generic.png)

Antes de tomar medidas específicas, você precisará verificar o registro de atividades para ver o erro detalhado para a validação de implantação com falha.

Para visualizar o erro no registro de atividades:

1. Saia da atual oferta de implantação do Azure Marketplace.
2. Na barra de pesquisa superior, procure e selecione **Registro de atividades**.
3. Encontre uma atividade chamada **Validar a implantação** que tenha um status de **Falha** e selecione a atividade.
   ![Captura de tela da atividade individual **Validar a implantação** com um status **Falha**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecione JSON e, em seguida, role até a parte inferior da tela até ver o campo "statusMessage".
   ![Captura de tela da atividade com falha, com uma caixa vermelha em torno da propriedade statusMessage do texto JSON.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Se o modelo de operação ultrapassar o limite de cotas, você pode fazer uma das seguintes coisas para corrigi-lo:

 - Execute o Azure Marketplace com os parâmetros que você usou da primeira vez, mas desta vez use menos VMs e núcleos VM.
 - Abra o link que você vê no campo **statusMessage** em um navegador para enviar uma solicitação para aumentar a cota de sua assinatura do Azure para o VM SKU especificado.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modelo do Gerenciador de recursos do Azure e erros de Configuração de Estado Desejado (DSC) do PowerShell

Siga estas instruções para solucionar sem problemas implantações mal sucedidas dos modelos do Azure Resource Manager e do PowerShell DSC.

1. Revisar erros na implantação usando [operações de implantação de visualização com o Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Se não houver erros na implantação, revise erros no registro de atividades usando [registros de atividade saquepara auditoria de ações sobre recursos](../azure-resource-manager/resource-group-audit.md).
3. Uma vez identificado o erro, use a mensagem de erro e os recursos em [Solucionar erros comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e tente novamente implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: Sua implantação\<falhou.... nome de host>/joindomain

![Sua captura de tela falha de implantação.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais fornecidas para a adesão de VMs ao domínio estão incorretas.

**Correção 1:** Consulte o erro "Credenciais incorretas" para VMs não estão unidos ao domínio na [configuração VM do host session](troubleshoot-vm-configuration.md).

**Causa 2:** O nome de domínio não se resolve.

**Correção 2:** Ver [erro: o nome de domínio não se resolve](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) na [configuração de VM do host de sessão](troubleshoot-vm-configuration.md).

**Causa 3:** A configuração de DNS da rede virtual (VNET) está definida **como Padrão**.

Para corrigir isso, faça as seguintes coisas:

1. Abra o Portal Azure e vá para a guia **Redes Virtuais.**
2. Encontre seu VNET e selecione **servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito da tela. Nesse menu, selecione **Personalizado**.
4. Certifique-se de que os servidores DNS listados em Personalizado correspondam ao controle de domínio ou ao domínio do Active Directory. Se você não ver o servidor DNS, você pode adicioná-lo inserindo seu valor no campo **do servidor Adicionar DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Erro: Sua implantação falhou...\Não autorizada

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A assinatura que você está usando é um tipo que não pode acessar os recursos necessários na região onde o cliente está tentando implantar. Por exemplo, as assinaturas MSDN, Free ou Education podem mostrar esse erro.

**Correção:** Altere seu tipo de assinatura ou região para uma que possa acessar os recursos necessários.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisionProvisionProvisionError

![Captura de tela de sua implantação Falha com falha no estado de provisionamento do terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente Windows Virtual Desktop.

**Causa 2:** Erro transitório com conexão.

**Correção:** Confirme que o ambiente de desktop virtual do Windows é saudável ao fazer login usando o PowerShell. Termine o registro de VM manualmente em [Criar um pool de host com o PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de usuário do Admin especificado não é permitido

![Falha na captura de tela da sua implantação, na qual um admin especificado não é permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** A senha fornecida contém substrings proibidas (administrador, administrador, raiz).

**Correção:** Atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM reportou uma falha ao processar a extensão

![Captura de tela da operação de recursos concluída com o estado de provisionamento de terminal em Sua Falha de Implantação.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** A extensão do PowerShell DSC não foi capaz de obter acesso de admin na VM.

**Correção:** Confirme que nome de usuário e senha têm acesso administrativo na máquina virtual e execute novamente o modelo do Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: ImplantaçãoFalha – Configuração do PowerShell DSC 'FirstSessionHost' concluída com erro(s)

![Falha na captura de tela da implantação com a configuração 'FirstSessionHost' da configuração do PowerShell DSC concluída com Erros.](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemplo de erro bruto:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** A extensão do PowerShell DSC não foi capaz de obter acesso de admin na VM.

**Correção:** Confirme o nome de usuário e a senha fornecidos para ter acesso administrativo na máquina virtual e executar novamente o modelo do Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplantaçãoFalha – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte do nome do grupo de recursos é usado para certos recursos que estão sendo criados pelo modelo. Devido à correspondência de nome dos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo do Azure Resource Manager para implantar VMs host de sessão, torne os dois primeiros caracteres exclusivos para o nome do seu grupo de recursos de assinatura.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplantaçãoFalha – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Esse erro ocorre porque a NIC criada com o modelo Do Azure Resource Manager tem o mesmo nome de outra NIC já no VNET.

**Correção:** Use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: ImplantaçãoFalha – Download de erro

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Esse erro deve-se a uma rota estática, regra de firewall ou NSG bloqueando o download do arquivo zip vinculado ao modelo do Azure Resource Manager.

**Correção:** Remova a rota estática de bloqueio, a regra de firewall ou o NSG. Opcionalmente, abra o arquivo json do azure Resource Manager em um editor de texto, leve o link para arquivo zip e baixe o recurso para um local permitido.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

Exemplo de erro bruto:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** O admin de inquilino do Windows Virtual Desktop especificado não tem uma atribuição de função válida.

**Correção:** O usuário que criou o inquilino do Windows Virtual Desktop precisa fazer login no Windows Virtual Desktop PowerShell e atribuir ao usuário tentado uma atribuição de função. Se estiver executando os parâmetros do modelo do GitHub Azure Resource Manager, siga estas instruções usando comandos PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Erro: O usuário requer a autenticação multifatorial do Azure (MFA)

![A captura de tela da sua implantação falhou devido à falta de Autenticação Multifatorial (MFA)](media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de inquilino do Windows Virtual Desktop especificado requer que o MFA (Multi-Factor Authentication, autenticação multifatorial) do Azure faça login.

**Correção:** Crie um diretor de serviço e atribua-lhe uma função para o seu inquilino do Windows Virtual Desktop seguindo as etapas do [Tutorial: Crie diretores de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md). Depois de verificar se você pode fazer login no Windows Virtual Desktop com o diretor de serviço, execute novamente a oferta do Azure Marketplace ou o modelo do GitHub Azure Resource Manager, dependendo do método que você estiver usando. Siga as instruções abaixo para inserir os parâmetros corretos para o seu método.

Se você estiver executando a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros para autenticar corretamente no Windows Virtual Desktop:

- Proprietário rds do inquilino do Windows Virtual Desktop: principal do serviço
- ID do aplicativo: A identificação do aplicativo do novo diretor de serviço que você criou
- Senha/Senha de Confirmação: O segredo de senha que você gerou para o diretor do serviço
- Azure AD Tenant ID: O Azure AD Tenant ID do diretor de serviço que você criou

Se você estiver executando o modelo do GitHub Azure Resource Manager, forneça valores para os seguintes parâmetros para autenticar corretamente no Windows Virtual Desktop:

- Nome principal do usuário do Inquilino Admin (UPN) ou ID do aplicativo: A identificação do aplicativo do novo diretor de serviço que você criou
- Senha do Inquilino: O segredo de senha que você gerou para o diretor do serviço
- IsServicePrincipal: **verdadeiro**
- AadTenantId: O ID do Inquilino Azure AD do diretor de serviço que você criou

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de clientes do Windows Virtual Desktop, consulte [conexões de serviço do Windows Virtual Desktop](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes de desktop remoto, consulte [Solucionar problemas do cliente de desktop remoto](troubleshoot-client.md)
- Para solucionar problemas ao usar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o [ambiente Windows Virtual Desktop](environment-setup.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
