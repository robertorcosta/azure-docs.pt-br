---
title: Monitore o desempenho em VMs azure - Azure Application Insights
description: Monitoramento de desempenho de aplicativos para conjuntos de escala de máquinas virtuais Azure VM e Azure. Mapeie o tempo de carga e o tempo de resposta, as informações de dependência e defina alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661321"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Implantar o Azure Monitor Application Insights Agent em máquinas virtuais do Azure e conjuntos de escala de máquinas virtuais do Azure

Permitir o monitoramento em seus aplicativos web baseados em .NET em execução em [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/) e [conjuntos de escala de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) agora é mais fácil do que nunca. Obtenha todos os benefícios de usar o Application Insights sem modificar seu código.

Este artigo orienta você a permitir o monitoramento de Insights de aplicativos usando o Agente de Insights de Aplicativos e fornece orientações preliminares para automatizar o processo para implantações em larga escala.

> [!IMPORTANT]
> O Azure Application Insights Agent for .NET está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço, e não recomendamos para cargas de trabalho de produção. Alguns recursos podem não ser suportados, e alguns podem ter recursos restritos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Existem duas maneiras de habilitar o monitoramento de aplicativos para máquinas virtuais do Azure e conjuntos de dispositivos virtuais do Azure hospedados:

* **Sem código** via Agente de Insights de Aplicativo
    * Este método é o mais fácil de habilitar, e nenhuma configuração avançada é necessária. É muitas vezes referido como monitoramento "runtime".

    * Para máquinas virtuais Azure e conjuntos de escala de máquinas virtuais Do Zure, recomendamos, no mínimo, permitir esse nível de monitoramento. Depois disso, com base no seu cenário específico, você pode avaliar se a instrumentação manual é necessária.

    * O Agente de Insights de Aplicativo coleta automaticamente os mesmos sinais de dependência fora da caixa que o .NET SDK. Consulte [a coleção automática dependency](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) para saber mais.
        > [!NOTE]
        > Atualmente, apenas aplicativos hospedados no .Net IIS são suportados. Use um SDK para instrumentar aplicativos ASP.NET Core, Java e Node.js hospedados em máquinas virtuais do Azure e conjuntos de escala de máquinas virtuais.

* **Baseado em código** via SDK

    * Essa abordagem é muito mais personalizável, mas requer [a adição de uma dependência dos pacotes SDK NuGet do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Este método, também significa que você tem que gerenciar as atualizações para a versão mais recente dos pacotes você mesmo.

    * Se você precisar fazer chamadas de API personalizadas para rastrear eventos/dependências não capturados por padrão com monitoramento baseado em agente, você precisará usar esse método. Confira a [API para eventos personalizados e artigo de métricas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais.

> [!NOTE]
> Se o monitoramento baseado no agente e a instrumentação manual baseada em SDK forem detectadas, apenas as configurações de instrumentação manual serão honradas. Isto é para evitar que dados duplicados sejam enviados. Para saber mais sobre isso, confira a [seção de solução de problemas](#troubleshooting) abaixo.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gerenciar o Agent de Insights de Aplicativos para aplicações .NET em máquinas virtuais Do Azure usando o PowerShell

> [!NOTE]
> Antes de instalar o Agente de Insights de Aplicativo, você precisará de uma seqüência de conexões. [Crie um novo recurso de insights de aplicativos](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) ou copie a seqüência de conexões de um recurso de insights de aplicativo existente.

> [!NOTE]
> Novo para powershell? Confira o [Guia Get Started](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Instale ou atualize o Application Insights Agent como uma extensão para máquinas virtuais do Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Você pode instalar ou atualizar o Agent de Insights de aplicativo como uma extensão em várias máquinas virtuais em escala usando um loop Powershell.

Desinstale a extensão do Agente de Insights de Aplicativos da máquina virtual do Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Status de extensão do Agente de Insights do Aplicativo de Consulta para máquina virtual Do Zure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenha lista de extensões instaladas para a máquina virtual do Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Você também pode ver extensões instaladas na lâmina da máquina virtual do [Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) no Portal.

> [!NOTE]
> Verifique a instalação clicando em Live Metrics Stream dentro do Recurso de Insights de Aplicativo associado à seqüência de conexões que você usou para implantar a Extensão do Agente de Insights do Aplicativo. Se você estiver enviando dados de várias máquinas virtuais, selecione as máquinas virtuais azure de destino em Nome do Servidor. Pode levar até um minuto para os dados começarem a fluir.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gerenciar o Agent de insights de aplicativos para aplicativos .NET em conjuntos de escala de máquina virtual do Azure usando powershell

Instale ou atualize o Agent de Insights de Aplicativo como uma extensão para o conjunto de escalas de máquina virtual do Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Desinstale a extensão de monitoramento de aplicativos dos conjuntos de escala de máquinavirtual do Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Status de extensão de monitoramento de aplicativos de consulta para conjuntos de escala de máquina virtual do Azure
```powershell
# Not supported by extensions framework
```

Obtenha lista de extensões instaladas para conjuntos de escalade máquinas virtuais do Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Solução de problemas

Encontre dicas de solução de problemas para o Application Insights Monitoring Agent Extension para aplicativos .NET em execução em máquinas virtuais do Azure e conjuntos de escala de máquinas virtuais.

> [!NOTE]
> Os aplicativos .NET Core, Java e Node.js são suportados apenas em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais Do Zure por meio de instrumentação manual baseada em SDK e, portanto, as etapas abaixo não se aplicam a esses cenários.

A saída de execução da extensão é registrada nos arquivos localizados nos seguintes diretórios:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Próximas etapas
* Saiba como [implantar um aplicativo em um conjunto de escalas de máquina virtual do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configure os testes web de disponibilidade](monitor-web-app-availability.md) para serem alertados se o ponto final estiver errado.
