---
title: Perfil de aplicativos web em um Azure VM - Application Insights Profiler
description: Crie perfis de aplicativos Web em uma VM do Azure com o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671572"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Crie perfis de aplicativos Web em execução em uma máquina virtual do Azure ou um conjunto de dimensionamento de máquinas virtuais definido com o Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você também pode implantar o Azure Application Insights Profiler nestes serviços:
* [Serviço de aplicativo do Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Tecido de serviço azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Implantar o Profiler em uma máquina virtual ou em um conjunto de dimensionamento de máquinas virtuais
Este artigo orientará você pelas etapas necessárias para a execução do Application Insights Profiler em seu conjunto de dimensionamento de máquinas virtuais do Azure ou na VM (máquina virtual) do Azure. O Profiler é instalado com a extensão de Diagnóstico do Azure para VMs. Configure a extensão para executar o Profiler e criar o SDK do Application Insights em seu aplicativo.

1. Adicione o Application Insights SDK ao seu [aplicativo ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Para exibir perfis das suas solicitações, você precisa enviar a telemetria de solicitação para o Application Insights.

1. Instale a extensão do Diagnóstico do Azure na VM. Para exemplos completos de modelos do Resource Manager, consulte:  
   * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Conjunto de escala de máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A chave fundamental é a ApplicationInsightsProfilerSink no WadCfg. Para que o Diagnóstico do Azure habilite o Profiler a enviar dados para o iKey, adicione outro coletor a esta seção.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Implante a definição de implantação de ambiente modificada.  

   A aplicação das modificações geralmente envolve uma implantação de modelo completo ou uma publicação baseada em serviço de nuvem por meio dos cmdlets do PowerShell ou do Visual Studio.  

   Os seguintes comandos PowerShell são uma abordagem alternativa para máquinas virtuais existentes que tocam apenas a extensão Azure Diagnostics. Adicione o ProfilerSink anteriormente mencionado à configuração que é devolvida pelo comando Get-AzVMDiagnosticsExtension. Em seguida, passe a configuração atualizada para o comando Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se o aplicativo desejado estiver em execução por meio do [IIS](https://www.microsoft.com/web/downloads/platform.aspx), habilite o recurso do Windows `IIS Http Tracing`.

   a. Estabeleça acesso remoto ao ambiente e, em seguida, use a janela [Adicionar recursos do Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/). Ou execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto for um problema, você poderá usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o comando a seguir:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implante seu aplicativo.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Defina o Profiler Sink usando o Azure Resource Explorer
Ainda não temos uma maneira de definir o dissipador de perfis de insights de aplicativos a partir do portal. Em vez de usar powershell como descrito acima, você pode usar o Azure Resource Explorer para definir a pia. Mas note, se você implantar a VM novamente, a pia será perdida. Você precisará atualizar a configuração que você usa ao implantar a VM para preservar esta configuração.

1. Verifique se a extensão do Windows Azure Diagnostics está instalada visualizando as extensões instaladas para sua máquina virtual.  

    ![Verifique se a extensão WAD está instalada][wadextension]

2. Encontre a extensão VM Diagnostics para sua VM. Vá [https://resources.azure.com](https://resources.azure.com)para. Expanda seu grupo de recursos, Microsoft.Compute virtualMachines, nome da máquina virtual e extensões.  

    ![Navegue até a configuração WAD no Azure Resource Explorer][azureresourceexplorer]

3. Adicione o dissipador de perfis de insights do aplicativo ao nó SinksConfig em WadCfg. Se você ainda não tiver uma seção SinksConfig, talvez seja necessário adicionar uma. Certifique-se de especificar o aplicativo insights iKey adequado em suas configurações. Você precisará mudar o modo exploradores para Ler/Gravar no canto superior direito e pressionar o botão azul 'Editar'.

    ![Adicionar sink de perfil de insights de aplicativos][resourceexplorersinksconfig]

4. Quando terminar de editar a configuração, pressione 'Put'. Se a colocação for bem sucedida, uma verificação verde aparecerá no meio da tela.

    ![Enviar solicitação de envio para aplicar alterações][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>O Profiler pode ser executado em servidores locais?
Não temos planos para dar suporte ao Application Insights Profiler para servidores locais.

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
