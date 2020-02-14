---
title: Conectar máquinas híbridas ao Azure em escala
description: Neste artigo, você aprenderá a conectar computadores ao Azure usando o Azure ARC para servidores (versão prévia) usando uma entidade de serviço.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192266"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Conectar máquinas híbridas ao Azure em escala

Você pode habilitar o Azure ARC para servidores (versão prévia) para vários computadores Windows ou Linux em seu ambiente com várias opções flexíveis, dependendo de seus requisitos. Usando o script de modelo que fornecemos, você pode automatizar cada etapa da instalação, incluindo estabelecer a conexão com o arco do Azure. No entanto, é necessário executar esse script interativamente com uma conta que tenha permissões elevadas no computador de destino e no Azure. Para conectar os computadores ao arco do Azure para servidores, você pode usar uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory em vez de usar sua identidade privilegiada para [conectar-se interativamente à máquina](onboard-portal.md). Uma entidade de serviço é uma identidade de gerenciamento limitada especial que recebe apenas a permissão mínima necessária para conectar computadores ao Azure usando o comando `azcmagent`. Isso é mais seguro do que usar uma conta com privilégios mais alto como uma Administrador de Locatários e segue nossas práticas recomendadas de segurança de controle de acesso. A entidade de serviço é usada somente durante a integração, ela não é usada para nenhuma outra finalidade.  

Os métodos de instalação para instalar e configurar o agente do computador conectado requer que o método automatizado usado tenha permissões de administrador nos computadores. No Linux, usando a conta raiz e no Windows, como um membro do grupo Administradores local.

Antes de começar, certifique-se de revisar os [pré-requisitos](overview.md#prerequisites) e verificar se sua assinatura e seus recursos atendem aos requisitos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

No final desse processo, você terá conectado com êxito suas máquinas híbridas ao Azure ARC para servidores.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar uma entidade de serviço para integração em escala

Você pode usar [Azure PowerShell](/powershell/azure/install-az-ps) para criar uma entidade de serviço com o cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . Ou você pode seguir as etapas listadas em [criar uma entidade de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para concluir esta tarefa.

> [!NOTE]
> Quando você cria uma entidade de serviço, sua conta deve ser um administrador de acesso de usuário ou proprietário na assinatura que você deseja usar para integração. Se você não tiver permissões suficientes para criar atribuições de função, a entidade de serviço poderá ser criada, mas não poderá integrar computadores.
>

Para criar a entidade de serviço usando o PowerShell, execute o seguinte.

1. Execute o comando a seguir. Você deve armazenar a saída do cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) em uma variável ou não poderá recuperar a senha necessária em uma etapa posterior.

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

2. Para recuperar a senha armazenada na variável `$sp`, execute o seguinte comando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Na saída, localize o valor da senha na **senha** do campo e copie-o. Encontre também o valor sob o campo **ApplicationId** e copie-o também. Salve-os para mais tarde em um local seguro. Se você esquecer ou perder sua senha de entidade de serviço, poderá redefini-la usando o cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

Os valores das propriedades a seguir são usados com parâmetros passados para o `azcmagent`:

* O valor da propriedade **ApplicationId** é usado para o valor do parâmetro `--service-principal-id`
* O valor da propriedade **password** é usado para o parâmetro `--service-principal-secret` usado para conectar o agente.

> [!NOTE]
> Certifique-se de usar a propriedade **ApplicationId** de entidade de serviço, não a propriedade **ID** .
>

A função de **integração do computador conectado do Azure** contém apenas as permissões necessárias para carregar um computador. Você pode atribuir a permissão de entidade de serviço para permitir que seu escopo inclua um grupo de recursos ou uma assinatura. Para adicionar a atribuição de função, consulte [Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure](../../role-based-access-control/role-assignments-portal.md) ou [Adicionar ou remover atribuições de função usando o RBAC e CLI do Azure do Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

As etapas a seguir instalam e configuram o agente de computador conectado em seus computadores híbridos usando o modelo de script, que executa etapas semelhantes descritas no artigo [conectar máquinas híbridas ao Azure do portal do Azure](onboard-portal.md) . A diferença está na etapa final em que você estabelece a conexão com o arco do Azure usando o comando `azcmagent` usando a entidade de serviço. 

A seguir estão as configurações que você configura o comando `azcmagent` a ser usado para a entidade de serviço.

* `tenant-id`: o identificador exclusivo (GUID) que representa sua instância dedicada do Azure AD.
* `subscription-id`: a ID da assinatura (GUID) da sua assinatura do Azure na qual você deseja que os computadores estejam.
* `resource-group`: o nome do grupo de recursos ao qual você deseja que seus computadores conectados pertençam.
* `location`: consulte [regiões do Azure com suporte](overview.md#supported-regions). Esse local pode ser o mesmo ou diferente, como o local do grupo de recursos.
* `resource-name`: (*opcional*) usado para a representação de recursos do Azure do seu computador local. Se você não especificar esse valor, o nome do host do computador será usado.

Você pode aprender mais sobre a ferramenta de linha de comando `azcmagent` examinando a [referência Azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script de instalação do Windows

Veja a seguir um exemplo de um script de instalação do agente do computador conectado para Windows que foi modificado para usar a entidade de serviço para dar suporte a uma instalação totalmente automatizada e não interativa do agente.

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

Veja a seguir um exemplo do script de instalação do agente do computador conectado para Linux que foi modificado para usar a entidade de serviço para dar suporte a uma instalação totalmente automatizada e não interativa do agente.

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

Depois de instalar o agente e configurá-lo para se conectar ao arco do Azure para servidores (versão prévia), vá para o portal do Azure para verificar se o servidor foi conectado com êxito. Exiba seus computadores na [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md), para tarefas como [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)de VM, verificar se o computador está relatando para o espaço de trabalho esperado log Analytics, habilitar o monitoramento com o [Azure monitor com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e muito mais.

- Saiba mais sobre o [agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, gerenciá-lo usando runbooks de automação ou soluções como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-intro.md).
