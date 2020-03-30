---
title: Instale o agente de máquina conectada usando o Windows PowerShell DSC
description: Neste artigo, você aprende a conectar máquinas ao Azure usando o Azure Arc para servidores (visualização) usando o Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164677"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Como instalar o agente de máquina conectada usando o Windows PowerShell DSC

Usando [o DSC (Desired State Configuration, configuração do estado desejado do Windows PowerShell),](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) você pode automatizar a instalação e a configuração do software para um computador Windows. Este artigo descreve como usar o DSC para instalar o Azure Arc para servidores agente de máquina conectada em máquinas Windows híbridas.

## <a name="requirements"></a>Requisitos

- Windows PowerShell versão 4.0 ou superior

- O módulo [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC

- Um princípio de serviço para conectar as máquinas ao Azure Arc para servidores não interativos. Siga as etapas abaixo da seção [Crie um Diretor de Serviço para embarcar em escala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se você ainda não criou um diretor de serviço para Arc para servidores.

## <a name="install-the-connectedmachine-dsc-module"></a>Instale o módulo DSC ConnectedMachine

1. Para instalar manualmente o módulo, baixe o código-fonte e desfaça o conteúdo do diretório do projeto para o `$env:ProgramFiles\WindowsPowerShell\Modules folder`. Ou, execute o seguinte comando para instalar na galeria PowerShell usando o PowerShellGet (no PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Para confirmar a instalação, execute o seguinte comando e certifique-se de ver os recursos DSC da máquina conectada do Azure disponíveis.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na saída, você deve ver algo semelhante ao seguinte:

   ![Confirmação do exemplo de instalação do módulo DSC da máquina conectada](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e conecte-se ao Azure

Os recursos deste módulo foram projetados para gerenciar a configuração do Azure Connected Machine Agent. Também está incluído um `AzureConnectedMachineAgent.ps1`script PowerShell, encontrado na `AzureConnectedMachineDsc\examples` pasta. Ele usa recursos comunitários para automatizar o download e a instalação e estabelecer uma conexão com o Azure Arc. Este script executa etapas semelhantes descritas nas [máquinas híbridas Connect para o Azure a partir do artigo do portal Azure.](onboard-portal.md)

Se a máquina precisar se comunicar através de um servidor proxy para o serviço, depois de instalar o agente você precisa executar um comando descrito [aqui](onboard-portal.md#configure-the-agent-proxy-setting). Isso define a variável de ambiente `https_proxy` do sistema do servidor proxy. Em vez de executar o comando manualmente, você pode executar esta etapa com o DSC usando o módulo [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Para permitir que o DSC seja executado, o Windows precisa ser configurado para receber comandos remotos PowerShell mesmo quando você estiver executando uma configuração localhost. Para configurar seu ambiente corretamente de forma simples, basta executar `Set-WsManQuickConfig -Force` em um Terminal do PowerShell elevado.
>

Documentos de configuração (arquivos MOF) podem `Start-DscConfiguration` ser aplicados à máquina usando o cmdlet.

A seguir, os parâmetros que você passa para o script PowerShell para usar.

- `TenantId`: O identificador exclusivo (GUID) que representa a sua instância dedicada do Azure AD.

- `SubscriptionId`: O ID de assinatura (GUID) da sua assinatura do Azure que você deseja que as máquinas entrem.

- `ResourceGroup`: O nome do grupo de recursos ao qual você deseja que suas máquinas conectadas pertençam.

- `Location`: Veja [as regiões azure apoiadas](overview.md#supported-regions). Este local pode ser o mesmo ou diferente, como a localização do grupo de recursos.

- `Tags`: Matriz de strings de tags que devem ser aplicadas ao recurso da máquina conectada.

- `Credential`: Um objeto de credencial PowerShell com o **ApplicationId** e **a senha** usados para registrar máquinas em escala usando um principal [de serviço](onboard-service-principal.md). 

1. Em um console PowerShell, navegue até `.ps1` a pasta onde você salvou o arquivo.

2. Execute os seguintes comandos do PowerShell para compilar o documento MOF (para obter informações sobre compilação de configurações DSC, consulte [Configurações DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Isso criará `localhost.mof file` uma nova `C:\dsc`pasta chamada .

Depois de instalar o agente e configurá-lo para se conectar ao Azure Arc para servidores (versão prévia), acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Adicionando às configurações existentes

Esse recurso pode ser adicionado às configurações DSC existentes para representar uma configuração de ponta a ponta para uma máquina. Por exemplo, você pode querer adicionar esse recurso a uma configuração que define configurações seguras do sistema operacional.

O [módulo CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) da Galeria PowerShell pode ser usado para criar um [recurso composto](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) da configuração de exemplo, para simplificar ainda mais a combinação de configurações.

## <a name="next-steps"></a>Próximas etapas

- Saiba como gerenciar sua máquina usando [a Política do Azure,](../../governance/policy/overview.md)para coisas como [configuração de hóspedes](../../governance/policy/concepts/guest-configuration.md)vM, verificando se a máquina está reportando ao espaço de trabalho esperado do Log Analytics, habilite o monitoramento com [o Monitor Azure com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e muito mais.

- Saiba mais sobre o [agente log analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-intro.md).