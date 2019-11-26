---
title: Learn to audit the contents of virtual machines
description: Learn how Azure Policy uses the Guest Configuration agent to audit settings inside virtual machines.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f68bbc64ee8f0da02d213895a70e4c533b9a5f63
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463798"
---
# <a name="understand-azure-policys-guest-configuration"></a>Entender a Configuração de Convidado do Azure Policy

Beyond auditing and [remediating](../how-to/remediate-resources.md) Azure resources, Azure Policy can audit settings inside a machine. A validação é executada pela extensão e pelo cliente de Configuração de Convidado. A extensão, por meio do cliente, valida as configurações como:

- The configuration of the operating system
- Configuração ou presença do aplicativo
- Configurações do ambiente

Neste momento, a Configuração de Convidado do Azure Policy audita somente as configurações dentro do computador. Ela não aplica configurações.

## <a name="extension-and-client"></a>Extensão e cliente

To audit settings inside a machine, a [virtual machine extension](../../../virtual-machines/extensions/overview.md) is enabled. A extensão baixa a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limits set on the extension

To limit the extension from impacting applications running inside the machine, the Guest Configuration isn't allowed to exceed more than 5% of CPU utilization. This limitation exists for both built-in and custom definitions.

## <a name="register-guest-configuration-resource-provider"></a>Registrar o provedor de recursos de Configuração de Convidado

Antes de usar a Configuração de Convidado, você precisa registrar o provedor de recursos. Registre-se por meio do portal ou do PowerShell. The resource provider is registered automatically if assignment of a Guest Configuration policy is done through the portal.

### <a name="registration---portal"></a>Registro – Portal

Para registrar o provedor de recursos da Configuração de Convidado por meio do portal do Azure, siga estas etapas:

1. Inicie o portal do Azure e clique em **Todos os serviços**. Pesquise e selecione **Assinaturas**.

1. Localize e clique na assinatura para a qual você deseja habilitar a Configuração de Convidado.

1. No menu à esquerda da página **Assinatura**, clique em **Provedores de recursos**.

1. Filtre ou role até localizar **Microsoft.GuestConfiguration** e, em seguida, clique em **Registrar** na mesma linha.

### <a name="registration---powershell"></a>Registro – PowerShell

Para registrar o provedor de recursos da Configuração de Convidado por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Ferramentas de validação

Inside the machine, the Guest Configuration client uses local tools to run the audit.

A tabela a seguir mostra uma lista das ferramentas locais usadas em cada sistema operacional com suporte:

|Sistema operacional|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| O Ruby e o Python são instalados pela extensão de Configuração de Convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração Convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado for recebida, as configurações serão verificadas em um intervalo de 15 minutos. Os resultados são enviados ao provedor de recursos de configuração do convidado assim que a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de Configuração do Convidado. This update causes Azure Policy to evaluate the Azure Resource Manager properties. An on-demand Azure Policy evaluation retrieves the latest value from the Guest Configuration resource provider. However, it doesn't trigger a new audit of the configuration within the machine.

## <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte em imagens do Azure:

|Publicador|name|Versões|
|-|-|-|
|Canônico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows Client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Guest Configuration can audit nodes running a supported OS. If you would like to audit virtual machines that use a custom image, you need to duplicate the **DeployIfNotExists** definition and modify the **If** section to include your image properties.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

Windows Server Nano Server isn't supported in any version.

## <a name="guest-configuration-extension-network-requirements"></a>Guest Configuration Extension network requirements

To communicate with the Guest Configuration resource provider in Azure, machines require outbound access to Azure datacenters on port **443**. If you're using a private virtual network in Azure that doesn't allow outbound traffic, configure exceptions with [Network Security Group](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) rules. A service tag doesn't currently exist for Azure Policy Guest Configuration.

For IP address lists, you can download [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. You only need to allow outbound access to the IPs in the regions where your VMs are deployed.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento. Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana. É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição da Configuração de Convidado

Each audit run by Guest Configuration requires two policy definitions, a **DeployIfNotExists** definition and an **AuditIfNotExists** definition. The **DeployIfNotExists** definition is used to prepare the machine with the Guest Configuration agent and other components to support the [validation tools](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Validate the machine has been assigned a configuration to evaluate. If no assignment is currently present, get the assignment and prepare the machine by:
  - Authenticating to the machine using a [managed identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando a versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalando [ferramentas de validação](#validation-tools) e dependências se necessário

If the **DeployIfNotExists** assignment is Non-compliant, a [remediation task](../how-to/remediate-resources.md#create-a-remediation-task) can be used.

Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment uses the local validation tools to determine if the configuration assignment is Compliant or Non-compliant. A ferramenta de validação fornece os resultados para o cliente de Configuração de Convidado. O cliente encaminha os resultados para a Extensão de Convidado, o que os disponibiliza por meio do provedor de recursos da Configuração de Convidado.

O Azure Policy usa a propriedade **complianceStatus** dos provedores de recursos da Configuração de Convidado para relatar a conformidade no nó **Conformidade**. Para obter mais informações, confira [Obtendo dados de conformidade](../how-to/get-compliance-data.md).

> [!NOTE]
> The **DeployIfNotExists** policy is required for the **AuditIfNotExists** policy to return results. Without the **DeployIfNotExists**, the **AuditIfNotExists** policy shows "0 of 0" resources as status.

Todas as políticas internas da Configuração de Convidado são incluídas em uma iniciativa para agrupar as definições a serem usadas em atribuições. The built-in initiative named _\[Preview\]: Audit Password security settings inside Linux and Windows machines_ contains 18 policies. Há seis pares de **DeployIfNotExists** e **AuditIfNotExists** para o Windows e três para o Linux. The [policy definition](definition-structure.md#policy-rule) logic validates that only the target operating system is evaluated.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditing operating system settings following industry baselines

One of the initiatives available in Azure Policy provides the ability to audit operating system settings inside virtual machines following a "baseline" from Microsoft. The definition, _\[Preview\]: Audit Windows VMs that do not match Azure security baseline settings_ includes a complete set of audit rules based on settings from Active Directory Group Policy.

Most of the settings are available as parameters. This functionality allows you to customize what is audited to align the policy with your organizational requirements or to map the policy to third party information such as industry regulatory standards.

Some parameters support an integer value range. For example, the Maximum Password Age parameter can be set using a range operator to give flexibility to machine owners. You could audit that the effective Group Policy setting requiring users to change their passwords should be no more than 70 days, but shouldn't be less than one day. As described in the info-bubble for the parameter, to make this business policy the effective audit value, set the value to "1,70".

If you assign the policy using an Azure Resource Manager deployment template, you can use a parameters file to manage these settings from source control. Using a tool such as Git to manage changes to Audit policies with comments at each check-in documents evidence as to why an assignment should be an exception to the expected value.

#### <a name="applying-configurations-using-guest-configuration"></a>Applying configurations using Guest Configuration

The latest feature of Azure Policy configures settings inside machines. The definition _Configure the time zone on Windows machines_ makes changes to the machine by configuring the time zone.

When assigning definitions that begin with _Configure_, you must also assign the definition _Deploy prerequisites to enable Guest Configuration Policy on Windows VMs_. You can combine these definitions in an initiative if you choose.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assigning policies to machines outside of Azure

The Audit policies available for Guest Configuration include the **Microsoft.HybridCompute/machines** resource type. Any machines onboarded to [Azure Arc for Servers](../../../azure-arc/servers/overview.md) that are in the scope of the policy assignment are automatically included.

### <a name="multiple-assignments"></a>Multiple assignments

Guest Configuration policies currently only support assigning the same Guest Assignment once per machine, even if the Policy assignment uses different parameters.

## <a name="built-in-resource-modules"></a>Built-in resource modules

When installing the Guest Configuration extension, the 'GuestConfiguration' PowerShell module is included with the latest version of DSC resource modules. This module can be downloaded from the PowerShell Gallery by using the 'Manual Download' link from the module page [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). The '.nupkg' file format can be renamed to '.zip' to uncompress and review.

## <a name="client-log-files"></a>Client log files

The Guest Configuration extension writes log files to the following locations:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Where `<version>` refers to the current version number.

### <a name="collecting-logs-remotely"></a>Collecting logs remotely

The first step in troubleshooting Guest Configuration configurations or modules should be to use the `Test-GuestConfigurationPackage` cmdlet following the steps in [Test a Guest Configuration package](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
If that isn't successful, collecting client logs can help diagnose issues.

#### <a name="windows"></a>Windows

To use the Azure VM Run Command capability to capture information from log files in Windows machines, the following example PowerShell script can be helpful. For more information, see [Run PowerShell scripts in your Windows VM with Run Command](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

To use the Azure VM Run Command capability to capture information from log files in Linux machines, the following example Bash script can be helpful. For more information, see [Run shell scripts in your Linux VM with Run Command](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Guest Configuration samples

Samples for Policy Guest Configuration are available in the following locations:

- [Samples index - Guest Configuration](../samples/index.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Próximos passos

- Review examples at [Azure Policy samples](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
