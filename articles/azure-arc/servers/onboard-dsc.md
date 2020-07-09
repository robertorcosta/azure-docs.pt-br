---
title: Instalar o agente do computador conectado usando o DSC do Windows PowerShell
description: Neste artigo, você aprenderá a conectar computadores ao Azure usando o Azure ARC para servidores (versão prévia) usando o DSC do Windows PowerShell.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: f3dee468764f27d930081b5a3cd415c48bb79c0d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104002"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Como instalar o agente do computador conectado usando o DSC do Windows PowerShell

Usando a DSC ( [configuração de estado desejado) do Windows PowerShell](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) , você pode automatizar a instalação e a configuração de software para um computador Windows. Este artigo descreve como usar a DSC para instalar o Azure ARC para servidores conectados ao agente do computador em computadores Windows híbridos.

## <a name="requirements"></a>Requisitos

- Windows PowerShell versão 4,0 ou superior

- O módulo DSC do [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Uma entidade de serviço para conectar os computadores ao arco do Azure para servidores de forma não interativa. Siga as etapas na seção [criar uma entidade de serviço para integração em escala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se você ainda não criou uma entidade de serviço para o Arc para servidores.

## <a name="install-the-connectedmachine-dsc-module"></a>Instalar o módulo DSC do ConnectedMachine

1. Para instalar o módulo manualmente, baixe o código-fonte e descompacte o conteúdo do diretório do projeto para o `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Ou execute o seguinte comando para instalar a partir da galeria do PowerShell usando o PowerShellGet (no PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Para confirmar a instalação, execute o comando a seguir e verifique se você vê os recursos de DSC do computador conectado do Azure disponíveis.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na saída, você verá algo semelhante ao seguinte:

   ![Confirmação do exemplo de instalação do módulo DSC do computador conectado](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

Os recursos neste módulo são projetados para gerenciar a configuração do agente de máquina conectado do Azure. Também está incluído um script `AzureConnectedMachineAgent.ps1` do PowerShell, encontrado na `AzureConnectedMachineDsc\examples` pasta. Ele usa recursos da Comunidade para automatizar o download e a instalação e estabelecer uma conexão com o arco do Azure. Esse script executa etapas semelhantes descritas no artigo [conectar máquinas híbridas ao Azure do portal do Azure](onboard-portal.md) .

Se o computador precisar se comunicar por meio de um servidor proxy para o serviço, depois de instalar o agente, você precisará executar um comando descrito [aqui](onboard-portal.md#configure-the-agent-proxy-setting). Isso define a variável de ambiente `https_proxy` do sistema do servidor proxy. Em vez de executar o comando manualmente, você pode executar essa etapa com a DSC usando o módulo [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) .

>[!NOTE]
>Para permitir que a DSC seja executada, o Windows precisa ser configurado para receber comandos remotos do PowerShell, mesmo quando você estiver executando uma configuração de localhost. Para configurar seu ambiente corretamente de forma simples, basta executar `Set-WsManQuickConfig -Force` em um Terminal do PowerShell elevado.
>

Os documentos de configuração (Arquivos MOF) podem ser aplicados ao computador usando o `Start-DscConfiguration` cmdlet.

A seguir estão os parâmetros que você passa para o script do PowerShell a ser usado.

- `TenantId`: O identificador exclusivo (GUID) que representa sua instância dedicada do Azure AD.

- `SubscriptionId`: A ID da assinatura (GUID) da sua assinatura do Azure na qual você deseja que os computadores.

- `ResourceGroup`: O nome do grupo de recursos ao qual você deseja que seus computadores conectados pertençam.

- `Location`: Consulte [regiões do Azure com suporte](overview.md#supported-regions). Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.

- `Tags`: Matriz de cadeia de caracteres de marcas que devem ser aplicadas ao recurso de computador conectado.

- `Credential`: Um objeto de credencial do PowerShell com o **ApplicationId** e a **senha** usados para registrar computadores em escala usando uma [entidade de serviço](onboard-service-principal.md). 

1. Em um console do PowerShell, navegue até a pasta em que você salvou o `.ps1` arquivo.

2. Execute os seguintes comandos do PowerShell para compilar o documento MOF (para obter informações sobre compilação de configurações DSC, consulte [Configurações DSC](/powershell/scripting/dsc/configurations/configurations?view=powershell-7)):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Isso criará um `localhost.mof file` em uma nova pasta chamada `C:\dsc` .

Depois de instalar o agente e configurá-lo para se conectar ao Azure Arc para servidores (versão prévia), acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Adicionando a configurações existentes

Esse recurso pode ser adicionado às configurações de DSC existentes para representar uma configuração de ponta a ponta para um computador. Por exemplo, talvez você queira adicionar esse recurso a uma configuração que define as configurações de sistema operacional seguro.

O módulo [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) da galeria do PowerShell pode ser usado para criar um [recurso de composição](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) da configuração de exemplo, para simplificar ainda mais a combinação de configurações.

## <a name="next-steps"></a>Próximas etapas

- Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) e muito mais.

- Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).
