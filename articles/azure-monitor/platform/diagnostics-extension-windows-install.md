---
title: Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)
description: Saiba como coletar dados de diagnóstico do Azure em uma conta de armazenamento do Azure para que você possa exibi-los com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472681"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)
A extensão de diagnóstico do Azure é um agente no Azure Monitor que coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Este artigo fornece detalhes sobre como instalar e configurar a extensão de diagnóstico do Windows e uma descrição de como os dados são armazenados no e na conta de armazenamento do Azure.

A extensão de diagnóstico é implementada como uma [extensão de máquina virtual](/virtual-machines/extensions/overview) no Azure, portanto, ela dá suporte às mesmas opções de instalação usando modelos do Resource Manager, PowerShell e CLI. Consulte [extensões e recursos de máquina virtual para Windows](/virtual-machines/extensions/features-windows) para obter detalhes sobre como instalar e manter extensões de máquina virtual.

## <a name="install-with-azure-portal"></a>Instalar com o portal do Azure
Você pode instalar e configurar a extensão de diagnóstico em uma máquina virtual individual no portal do Azure que fornece uma interface em vez de trabalhar diretamente com a configuração. Quando você habilita a extensão de diagnóstico, ela automaticamente usará uma configuração padrão com os contadores de desempenho e eventos mais comuns. Você pode modificar essa configuração padrão de acordo com seus requisitos específicos.

> [!NOTE]
> Há configurações de extensão de diagnóstico que você não pode configurar usando o portal do Azure incluindo o envio de dados para os hubs de eventos do Azure. Você deve usar um dos outros métodos de configuração para essas configurações.

1. Abra o menu de uma máquina virtual no portal do Azure.
2. Clique em **configurações de diagnóstico** na seção **monitoramento** do menu da VM.
3. Clique em **habilitar monitoramento em nível de convidado** se a extensão de diagnóstico ainda não tiver sido habilitada.
4. Uma nova conta de armazenamento do Azure será criada para a VM com o nome será baseada no nome do grupo de recursos para a VM. Você pode anexar a VM a outra conta de armazenamento selecionando a guia **agente** .

![Configurações de Diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Você pode modificar a configuração padrão depois que a extensão de diagnóstico tiver sido habilitada. A tabela a seguir descreve as opções que podem ser modificadas nas guias diferentes. Algumas opções têm um comando **personalizado** que permite que você especifique uma configuração mais detalhada; consulte [esquema de extensão de diagnóstico do Windows](diagnostics-extension-schema-windows.md) para obter detalhes sobre configurações diferentes.

| Tab | DESCRIÇÃO |
|:---|:---|
| Visão geral | Exibe a configuração atual com links para as outras guias. |
| Contadores de desempenho | Selecione os contadores de desempenho a serem coletados e a taxa de amostragem para cada um.  |
| Logs | Selecione os dados de log a serem coletados. Isso inclui logs de eventos do Windows, logs do IIS, logs de aplicativos .NET e eventos do ETW.  |
| Despejos de memória | Habilite o despejo de memória para processos diferentes. |
| Coletores | Habilite coletores de dados para enviar dados para destinos além do armazenamento do Azure.<br>Azure Monitor-envia dados de desempenho para Azure Monitor métricas.<br>Application Insights-enviar dados para um aplicativo Application Insights. |
| Agente | Modifique a seguinte configuração para o agente:<br>-Alterar a conta de armazenamento.<br>-Especifique o máximo de disco local usado para o agente.<br>-Configure os logs para a integridade do próprio agente.|


> [!NOTE]
> Embora a configuração da extensão de diagnóstico possa ser formatada em JSON ou XML, qualquer configuração feita na portal do Azure sempre será armazenada como JSON. Se você usar XML com outro método de configuração e, em seguida, alterar sua configuração com o portal do Azure, as configurações serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Consulte [usar monitoramento e diagnóstico com uma VM do Windows e Azure Resource Manager modelos](../../virtual-machines/extensions/diagnostics-template.md) de implantação de extensão de diagnóstico com modelos de Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure
O CLI do Azure pode ser usado para implantar a extensão de Diagnóstico do Azure em uma máquina virtual existente usando [AZ VM Extension Set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) como no exemplo a seguir. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

As configurações protegidas são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) do esquema de configuração. A seguir, um exemplo mínimo de um arquivo de configurações protegida que define a conta de armazenamento. Consulte [exemplo de configuração](diagnostics-extension-schema-windows.md#privateconfig-element) para obter detalhes completos das configurações particulares.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
As configurações públicas são definidas no [elemento público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. A seguir, um exemplo mínimo de um arquivo de configurações públicas que habilita a coleta de logs de infraestrutura de diagnóstico, um único contador de desempenho e um único log de eventos. Consulte [exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações públicas.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Implantação do PowerShell
O PowerShell pode ser usado para implantar a extensão de Diagnóstico do Azure em uma máquina virtual existente usando [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) , como no exemplo a seguir. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

As configurações particulares são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element), enquanto as configurações públicas são definidas no [elemento público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Você também pode optar por especificar os detalhes da conta de armazenamento como parâmetros do cmdlet Set-AzVMDiagnosticsExtension em vez de incluí-los nas configurações privadas.

A seguir, um exemplo mínimo de um arquivo de configuração que habilita a coleta de logs de infraestrutura de diagnóstico, um único contador de desempenho e um único log de eventos. Consulte [exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações públicas e privadas. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Consulte também [Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Armazenamento de dados
A tabela a seguir lista os diferentes tipos de dados coletados da extensão de diagnóstico e se eles são armazenados como uma tabela ou um blob. Os dados armazenados em tabelas também podem ser armazenados em BLOBs, dependendo da [configuração de storagetype](diagnostics-extension-schema-windows.md#publicconfig-element) em sua configuração pública.


| data | Tipo de armazenamento | DESCRIÇÃO |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Monitor de diagnóstico e alterações de configuração. |
| WADDirectoriesTable | Tabela | Diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, logs de solicitação do IIS com falha e diretórios personalizados.  O local do arquivo de log de blob é especificado no campo Container e o nome do blob está no campo RelativePath.  O campo AbsolutePath indica o local e o nome do arquivo como existia na máquina virtual do Azure. |
| WadLogsTable | Tabela | Logs gravados no código usando o ouvinte de rastreamento. |
| WADPerformanceCountersTable | Tabela | Contadores de desempenho. |
| WADWindowsEventLogsTable | Tabela | Logs de eventos do Windows. |
| wad-iis-failedreqlogfiles | Blob | Contém informações de logs de solicitação com falha do IIS. |
| wad-iis-logfiles | Blob | Contém informações sobre logs do IIS. |
| Personalizar | Blob | Um contêiner personalizado com base na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois de serem transferidos para o armazenamento. Por exemplo:

* Gerenciador de Servidores no Visual Studio – Se tiver instalado as Ferramentas do Azure para o Microsoft Visual Studio, será possível usar o nó do Armazenamento do Azure no Gerenciador de Servidores para exibir os dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, veja [Procurando e gerenciando recursos de armazenamento com o Gerenciador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Gerenciamento de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite trabalhar facilmente com os dados de Armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de Diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnósticos coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Próximas etapas
- Consulte [enviar dados da extensão de diagnóstico do Windows Azure para os hubs de eventos](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre como encaminhar os dados de monitoramento para os hubs de eventos do Azure.
