---
title: Criação do pool de hosts do Windows Virtual Desktop Environment-Azure
description: Como solucionar e resolver problemas de locatário e pool de hosts durante a instalação de um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65a61babe58e1cb9438262186a7f4cf37cb10a34
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612516"
---
# <a name="host-pool-creation"></a>Criação de pool de hosts

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo aborda problemas durante a configuração inicial do locatário da área de trabalho virtual do Windows e da infraestrutura do pool de hosts da sessão relacionada.

## <a name="provide-feedback"></a>Envie comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirindo a imagem de várias sessões do Windows 10 Enterprise

Para usar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, **selecione** > introdução ao**Microsoft Windows 10** > e [Windows 10 Enterprise Multi-Session, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemas com o uso do portal do Azure para criar pools de hosts

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Erro: "criar uma conta gratuita" é exibido ao acessar o serviço

![Uma imagem mostrando a portal do Azure exibir a mensagem "criar uma conta gratuita"](media/create-new-account.png)

**Causa**: não há assinaturas ativas na conta com a qual você entrou no Azure, ou a conta não tem permissões para exibir as assinaturas. 

**Correção**: entre na assinatura em que você implantará as VMs (máquinas virtuais) do host de sessão com uma conta que tenha pelo menos acesso de nível de colaborador.

### <a name="error-exceeding-quota-limit"></a>Erro: "excedendo o limite de cota"

Se a operação ultrapassar o limite de cota, você poderá executar uma das seguintes ações: 

- Crie um novo pool de hosts com os mesmos parâmetros, mas menos VMs e núcleos de VM.

- Abra o link que você vê no campo statusMessage em um navegador para enviar uma solicitação para aumentar a cota de sua assinatura do Azure para a SKU de VM especificada.

## <a name="azure-resource-manager-template-errors"></a>Erros de modelo de Azure Resource Manager

Siga estas instruções para solucionar problemas de implantações sem êxito de modelos de Azure Resource Manager e DSC do PowerShell.

1. Examine os erros na implantação usando [Exibir operações de implantação com o Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Se não houver erros na implantação, examine os erros no log de atividades usando [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/resource-group-audit.md).
3. Depois que o erro for identificado, use a mensagem de erro e os recursos em [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e tente novamente implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: falha na implantação....\<nome do host>/JoinDomain

![Falha na captura de tela da implantação.](media/failure-joindomain.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais fornecidas para ingressar VMs no domínio estão incorretas.

**Correção 1:** Consulte o erro "credenciais incorretas" para as VMs não são unidas ao domínio na [sessão máquina de host configuração](troubleshoot-vm-configuration.md).

**Causa 2:** O nome de domínio não é resolvido.

**Correção 2:** Consulte o [erro: o nome de domínio não é resolvido](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) na [configuração da VM do host de sessão](troubleshoot-vm-configuration.md).

**Causa 3:** Sua configuração de DNS de VNET (rede virtual) está definida como **padrão**.

Para corrigir isso, faça o seguinte:

1. Abra o portal do Azure e vá para a guia **redes virtuais** .
2. Localize sua VNET e, em seguida, selecione **servidores DNS**.
3. O menu servidores DNS deve aparecer no lado direito da tela. Nesse menu, selecione **personalizado**.
4. Verifique se os servidores DNS listados em Personalizar correspondem ao seu controlador de domínio ou Active Directory domínio. Se você não vir o servidor DNS, poderá adicioná-lo inserindo seu valor no campo **Adicionar servidor DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Erro: falha na implantação. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A assinatura que você está usando é um tipo que não pode acessar os recursos necessários na região em que o cliente está tentando implantar. Por exemplo, assinaturas do MSDN, gratuitas ou educacionais podem mostrar esse erro.

**Correção:** Altere seu tipo de assinatura ou região para um que possa acessar os recursos necessários.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Falha na captura de tela de sua implantação com o estado de provisionamento do terminal.](media/failure-vmextensionprovisioning.png)

**Causa 1:** Erro transitório com o ambiente de área de trabalho virtual do Windows.

**Causa 2:** Erro transitório com conexão.

**Correção:** Confirme se o ambiente de área de trabalho virtual do Windows está íntegro ao entrar usando o PowerShell. Conclua o registro da VM manualmente em [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: o nome de usuário do administrador especificado não é permitido

![A captura de tela de sua implantação falhou na qual um administrador especificado não é permitido.](media/failure-username.png)

Exemplo de erro bruto:

```Error
 { …{ "provisioningOperation": 
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId": 
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message": 
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Causa:** A senha fornecida contém subcadeias de caracteres proibidas (administrador, administrador, raiz).

**Correção:** Atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: a VM relatou uma falha ao processar a extensão

![Falha na captura de tela da operação de recurso concluída com o estado de provisionamento de terminal em sua implantação.](media/failure-processing.png)

Exemplo de erro bruto:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code": 
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Causa:** A extensão DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** Confirme se o nome de usuário e a senha têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – a configuração de DSC do PowerShell ' FirstSessionHost ' foi concluída com erro (s)

![Falha na captura de tela da implantação com a configuração DSC do PowerShell ' FirstSessionHost ' concluída com erro (s).](media/failure-dsc.png)

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

**Causa:** A extensão DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** Confirme se o nome de usuário e a senha fornecidos têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

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

**Causa:** Parte do nome do grupo de recursos é usada para determinados recursos que estão sendo criados pelo modelo. Devido ao nome que corresponde aos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo de Azure Resource Manager para implantar VMs de host de sessão, torne os dois primeiros caracteres exclusivos para o nome do grupo de recursos de assinatura.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

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

**Causa:** Esse erro ocorre porque a NIC criada com o modelo de Azure Resource Manager tem o mesmo nome que outra NIC já está na VNET.

**Correção:** Use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – erro ao baixar

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

**Causa:** Esse erro ocorre devido a uma rota estática, regra de firewall ou NSG que bloqueia o download do arquivo zip vinculado ao modelo de Azure Resource Manager.

**Correção:** Remova a rota estática de bloqueio, a regra de firewall ou NSG. Opcionalmente, abra o arquivo JSON do modelo de Azure Resource Manager em um editor de texto, leve o link para o arquivo zip e baixe o recurso para um local permitido.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
