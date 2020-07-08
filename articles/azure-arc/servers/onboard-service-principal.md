---
title: Conectar computadores híbridos ao Azure em escala
description: Neste artigo, você aprende a conectar computadores ao Azure usando o Azure Arc para servidores (versão prévia) usando uma entidade de serviço.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 45a61b5bc6f1082b84bf94db7e8ad5ce49ec068f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648063"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Conectar computadores híbridos ao Azure em escala

Você pode habilitar o Azure Arc para servidores (versão prévia) para vários computadores Windows ou Linux no ambiente com várias opções flexíveis, dependendo dos requisitos. Usando o script de modelo fornecido, você pode automatizar cada etapa da instalação, incluindo estabelecer a conexão com o Azure Arc. No entanto, é necessário executar esse script interativamente com uma conta que tenha permissões elevadas no computador de destino e no Azure. Para conectar os computadores ao Azure Arc para servidores, você pode usar uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory, em vez de usar a identidade privilegiada para [conectar-se interativamente ao computador](onboard-portal.md). Uma entidade de serviço é uma identidade de gerenciamento limitada especial que recebe apenas a permissão mínima necessária para conectar computadores ao Azure usando o comando `azcmagent`. Isso é mais seguro do que usar uma conta com privilégios maiores como um Administrador de Locatários e segue nossas melhores práticas de segurança de controle de acesso. A entidade de serviço só é usada durante a integração. Ela não é usada para outras finalidades.  

Os métodos de instalação e configuração do agente do Connected Machine requerem que o método automatizado usado tenha permissões de administrador nos computadores. No Linux, usando a conta raiz, e no Windows, como membro do grupo de Administradores Locais.

Antes de começar, examine os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Ao final desse processo, você terá conectado com êxito os computadores híbridos ao Azure Arc para servidores.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar uma Entidade de Serviço para integração em escala

Você pode usar o [Azure PowerShell](/powershell/azure/install-az-ps) para criar uma entidade de serviço com o cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Ou então, você pode seguir as etapas listadas em [Criar uma Entidade de Serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para concluir essa tarefa.

> [!NOTE]
> Quando você cria uma entidade de serviço, sua conta deve ser um Proprietário ou Administrador de Acesso de Usuário na assinatura que deseja usar para integração. Se você não tiver permissões suficientes para criar atribuições de função, a entidade de serviço poderá ser criada, mas não poderá integrar computadores.
>

Para criar a entidade de serviço usando o PowerShell, faça o seguinte.

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

3. Na saída, encontre o valor da senha no campo **senha** e copie-o. Além disso, encontre o valor no campo **ApplicationId** e copie-o também. Salve-os para mais tarde em um local seguro. Se você esquecer ou perder a senha da entidade de serviço, poderá redefini-la usando o cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

Os valores das propriedades a seguir são usados com os parâmetros passados para o `azcmagent`:

* O valor da propriedade **ApplicationId** é usado para o valor do parâmetro `--service-principal-id`
* O valor da propriedade **senha** é usado para o parâmetro `--service-principal-secret` usado para conectar o agente.

> [!NOTE]
> Use a propriedade **ApplicationId** da entidade de serviço, não a propriedade **Id**.
>

A função **Integração do Azure Connected Machine** contém apenas as permissões necessárias para carregar um computador. Você pode atribuir a permissão da entidade de serviço para possibilitar que o escopo inclua um grupo de recursos ou uma assinatura. Para adicionar a atribuição de função, confira [Adicionar ou remover atribuições de função usando o Azure RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md) ou [Adicionar ou remover atribuições de função usando o Azure RBAC e a CLI do Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

As etapas a seguir instalam e configuram o agente do Connected Machine nos computadores híbridos, usando o modelo de script que executa etapas semelhantes descritas no artigo [Conectar computadores híbridos ao Azure no portal do Azure](onboard-portal.md). A diferença está na etapa final em que você estabelece a conexão com o Azure Arc usando o comando `azcmagent` com a entidade de serviço. 

Estas são as configurações do comando `azcmagent` a ser usado para a entidade de serviço.

* `tenant-id`: O identificador exclusivo (GUID) que representa a instância dedicada do Azure AD.
* `subscription-id`: A ID da assinatura (GUID) do Azure onde você deseja que os computadores estejam.
* `resource-group`: O nome do grupo de recursos ao qual você deseja que os computadores conectados pertençam.
* `location`: Confira [Regiões do Azure com suporte](overview.md#supported-regions). Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.
* `resource-name`: (*Opcional*) Usado para a representação de recursos do Azure do seu computador local. Se você não especificar esse valor, o nome do host do computador será usado.

Você pode saber mais sobre a ferramenta de linha de comando `azcmagent` examinando a [Referência de Azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script de instalação do Windows

Este é um exemplo do script de instalação do agente do Connected Machine para Windows que foi modificado para usar a entidade de serviço, a fim de oferecer suporte a uma instalação totalmente automatizada e não interativa do agente.

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

Este é um exemplo do script de instalação do agente do Connected Machine para Linux que foi modificado para usar a entidade de serviço, a fim de oferecer suporte a uma instalação totalmente automatizada e não interativa do agente.

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

- Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configuração de convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificando se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) e muito mais.

- Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).
