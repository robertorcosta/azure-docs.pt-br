---
title: Conecte máquinas híbridas ao Azure em escala
description: Neste artigo, você aprende a conectar máquinas ao Azure usando o Azure Arc para servidores (visualização) usando um principal de serviço.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192266"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Conecte máquinas híbridas ao Azure em escala

Você pode habilitar o Azure Arc para servidores (visualização) para várias máquinas Windows ou Linux em seu ambiente com várias opções flexíveis, dependendo de suas necessidades. Usando o script de modelo que fornecemos, você pode automatizar cada etapa da instalação, incluindo o estabelecimento da conexão com o Azure Arc. No entanto, você é obrigado a executar interativamente este script com uma conta que tenha permissões elevadas na máquina de destino e no Azure. Para conectar as máquinas ao Azure Arc para servidores, você pode usar um diretor de [serviço](../../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory em vez de usar sua identidade privilegiada para [conectar interativamente a máquina](onboard-portal.md). Um diretor de serviço é uma identidade de gerenciamento limitada especial que é `azcmagent` concedida apenas a permissão mínima necessária para conectar máquinas ao Azure usando o comando. Isso é mais seguro do que usar uma conta privilegiada mais alta como um administrador de inquilinos e segue nossas práticas recomendadas de segurança de controle de acesso. O principal do serviço é usado apenas durante o onboarding, não é usado para qualquer outro propósito.  

Os métodos de instalação para instalar e configurar o agente Máquina Conectada exigem que o método automatizado que você usa tenha permissões de administrador nas máquinas. No Linux, usando a conta raiz e no Windows, como membro do grupo Administradores Locais.

Antes de começar, examine os [pré-requisitos](overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

No final deste processo, você terá conectado com sucesso suas máquinas híbridas ao Azure Arc para servidores.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Crie um Diretor de Serviço para onboarding em escala

Você pode usar [o Azure PowerShell](/powershell/azure/install-az-ps) para criar um principal de serviço com o cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Ou você pode seguir as etapas listadas em [Criar um Diretor de Serviço usando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para concluir essa tarefa.

> [!NOTE]
> Ao criar um diretor de serviço, sua conta deve ser um Administrador de Acesso ao Proprietário ou usuário na assinatura que você deseja usar para onboarding. Se você não tiver permissões suficientes para criar atribuições de função, a entidade de serviço poderá ser criada, mas não poderá integrar computadores.
>

Para criar o principal de serviço usando o PowerShell, execute o seguinte.

1. Execute o comando a seguir. Você deve armazenar a [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) saída do cmdlet em uma variável, ou você não será capaz de recuperar a senha necessária em uma etapa posterior.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Para recuperar a senha `$sp` armazenada na variável, execute o seguinte comando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Na saída, encontre o valor da senha a **senha** do campo e copie-a. Encontre também o valor no campo **ApplicationId** e copie-o também. Guarde-os para mais tarde em um lugar seguro. Se você esquecer ou perder a senha principal do [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) seu serviço, você pode redefini-la usando o cmdlet.

Os valores das seguintes propriedades são `azcmagent`utilizados com parâmetros passados para:

* O valor da propriedade **ApplicationId** `--service-principal-id` é usado para o valor do parâmetro
* O valor **password** da propriedade de `--service-principal-secret` senha é usado para o parâmetro usado para conectar o agente.

> [!NOTE]
> Certifique-se de usar a propriedade **de aplicativo** principal do serviço, não a propriedade **Id.**
>

A **função Onboarding da máquina conectada do Azure** contém apenas as permissões necessárias para embarcar em uma máquina. Você pode atribuir a permissão principal do serviço para permitir que seu escopo inclua um grupo de recursos ou uma assinatura. Para adicionar atribuição de função, consulte [Adicionar ou remover atribuições de função usando o Azure RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md) ou [Adicionar ou remover atribuições de função usando O Azure RBAC e a Cli do Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e conecte-se ao Azure

As etapas a seguir instalam e configuram o agente Connected Machine em suas máquinas híbridas usando o modelo de script, que executa etapas semelhantes descritas nas [máquinas híbridas Connect para o Azure a partir do artigo do portal Azure.](onboard-portal.md) A diferença está na etapa final onde você estabelece a `azcmagent` conexão com o Azure Arc usando o comando usando o principal de serviço. 

A seguir estão as configurações `azcmagent` que você configura o comando a ser usado para o diretor de serviço.

* `tenant-id`: O identificador exclusivo (GUID) que representa a sua instância dedicada do Azure AD.
* `subscription-id`: O ID de assinatura (GUID) da sua assinatura do Azure que você deseja que as máquinas entrem.
* `resource-group`: O nome do grupo de recursos ao qual você deseja que suas máquinas conectadas pertençam.
* `location`: Veja [as regiões azure apoiadas](overview.md#supported-regions). Este local pode ser o mesmo ou diferente, como a localização do grupo de recursos.
* `resource-name`: (*Opcional*) Usado para a representação de recursos do Azure da sua máquina no local. Se você não especificar este valor, o nome de host da máquina será usado.

Você pode aprender `azcmagent` mais sobre a ferramenta de linha de comando revisando a [referência azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script de instalação do Windows

A seguir, um exemplo do script de instalação da Máquina Conectada para Windows que foi modificado para usar o diretor de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Script de instalação do Linux

A seguir, um exemplo do script de instalação connected machine para Linux que foi modificado para usar o diretor de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Depois de instalar o agente e configurá-lo para se conectar ao Azure Arc para servidores (versão prévia), acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como gerenciar sua máquina usando [a Política do Azure,](../../governance/policy/overview.md)para coisas como [configuração de hóspedes](../../governance/policy/concepts/guest-configuration.md)vM, verificando se a máquina está reportando ao espaço de trabalho esperado do Log Analytics, habilite o monitoramento com [o Monitor Azure com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e muito mais.

- Saiba mais sobre o [agente log analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).
