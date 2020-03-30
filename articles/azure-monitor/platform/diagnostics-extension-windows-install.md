---
title: Instale e configure a extensão de diagnósticodo Windows Azure (WAD)
description: Saiba como coletar dados de diagnóstico do Azure em uma conta do Azure Storage para que você possa visualizá-los com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672252"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instale e configure a extensão de diagnósticodo Windows Azure (WAD)
A extensão de diagnóstico do Azure é um agente no Azure Monitor que coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Este artigo fornece detalhes sobre a instalação e configuração da extensão de diagnósticos do Windows e uma descrição de como os dados são armazenados e a conta do Azure Storage.

A extensão de diagnóstico é implementada como uma [extensão](../../virtual-machines/extensions/overview.md) de máquina virtual no Azure, por isso suporta as mesmas opções de instalação usando modelos de Gerenciador de Recursos, PowerShell e CLI. Consulte [extensões e recursos da máquina virtual para windows para](../../virtual-machines/extensions/features-windows.md) obter detalhes sobre a instalação e manutenção de extensões de máquinas virtuais.

## <a name="install-with-azure-portal"></a>Instale com o portal Azure
Você pode instalar e configurar a extensão de diagnóstico em uma máquina virtual individual no portal Azure que fornece uma interface em vez de trabalhar diretamente com a configuração. Quando você habilita a extensão de diagnóstico, ele usará automaticamente uma configuração padrão com os contadores de desempenho e eventos mais comuns. Você pode modificar essa configuração padrão de acordo com seus requisitos específicos.

> [!NOTE]
> Existem configurações de extensão de diagnóstico que você não pode configurar usando o portal Azure, incluindo o envio de dados para o Azure Event Hubs. Você deve usar um dos outros métodos de configuração para essas configurações.

1. Abra o menu para uma máquina virtual no portal Azure.
2. Clique em **Configurações de diagnóstico** na seção **Monitoramento** do menu VM.
3. Clique **em Ativar o monitoramento em nível de hóspedes** se a extensão de diagnóstico ainda não estiver ativada.
4. Uma nova conta do Azure Storage será criada para a VM com o nome será baseada no nome do grupo de recursos para a VM. Você pode anexar a VM a outra conta de armazenamento selecionando a guia **Agente.**

![Configurações de Diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Você pode modificar a configuração padrão assim que a extensão de diagnóstico estiver ativada. A tabela a seguir descreve as opções que você pode modificar nas diferentes guias. Algumas opções possuem um **comando Custom** que permite especificar configurações mais detalhadas; consulte [o esquema de extensão de diagnósticos do Windows](diagnostics-extension-schema-windows.md) para obter detalhes sobre diferentes configurações.

| Tab | Descrição |
|:---|:---|
| Visão geral | Exibe a configuração atual com links para as outras guias. |
| Contadores de desempenho | Selecione os contadores de desempenho para coletar e a taxa de amostra para cada um.  |
| Logs | Selecione os dados de log a serem coletados. Isso inclui registros de eventos do Windows, logs de IIS, registros de aplicativos .NET e eventos ETW.  |
| Despejos de memória | Habilite o crash dump para diferentes processos. |
| Coletores | Habilite sinks de dados para enviar dados para destinos, além do Armazenamento Azure.<br>Monitor Azure - Envia dados de desempenho para o Azure Monitor Metrics.<br>Insights do aplicativo - Envie dados para um aplicativo Despontados. |
| Agente | Modificar a seguinte configuração para o agente:<br>- Mude a conta de armazenamento.<br>- Especifique o disco local máximo usado para o agente.<br>- Configure registros para a saúde do próprio agente.|


> [!NOTE]
> Embora a configuração para extensão de diagnóstico possa ser formatado em JSON ou XML, qualquer configuração feita no portal Azure será sempre armazenada como JSON. Se você usar XML com outro método de configuração e, em seguida, alterar sua configuração com o portal Azure, as configurações serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Veja [Usar o monitoramento e os diagnósticos com modelos do Windows VM e do Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) na implantação da extensão de diagnóstico com os modelos do Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure
O Azure CLI pode ser usado para implantar a extensão Azure Diagnostics em uma máquina virtual existente usando [o conjunto de extensão az vm](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) como no exemplo a seguir. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

As configurações protegidas são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) do esquema de configuração. A seguir está um exemplo mínimo de um arquivo de configurações protegida que define a conta de armazenamento. Consulte [Exemplo de configuração](diagnostics-extension-schema-windows.md#privateconfig-element) para obter detalhes completos das configurações privadas.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
As configurações públicas são definidas no [elemento Público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. A seguir está um exemplo mínimo de um arquivo de configurações públicas que permite a coleta de registros de infra-estrutura de diagnóstico, um único contador de desempenho e um único registro de eventos. Consulte [Exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações públicas.

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
O PowerShell pode ser usado para implantar a extensão Azure Diagnostics em uma máquina virtual existente usando [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) como no exemplo a seguir. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

As configurações privadas são definidas no [elemento PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)enquanto as configurações públicas são definidas no [elemento Público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Você também pode optar por especificar os detalhes da conta de armazenamento como parâmetros do cmdlet Set-AzVMDiagnosticsExtension em vez de incluí-los nas configurações privadas.

A seguir está um exemplo mínimo de um arquivo de configuração que permite a coleta de registros de infra-estrutura de diagnóstico, um único contador de desempenho e um único registro de evento. Consulte [Exemplo de configuração](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações privadas e públicas. 

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
A tabela a seguir lista os diferentes tipos de dados coletados da extensão de diagnóstico e se eles são armazenados como uma tabela ou uma bolha. Os dados armazenados em tabelas também podem ser armazenados em blobs, dependendo da [configuração StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) em sua configuração pública.


| Dados | Tipo de armazenamento | Descrição |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | O monitor de diagnóstico e as alterações de configuração. |
| WADDirectoriesTable | Tabela | Diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, logs de solicitação do IIS com falha e diretórios personalizados.  O local do arquivo de log de blob é especificado no campo Container e o nome do blob está no campo RelativePath.  O campo AbsolutePath indica o local e o nome do arquivo como existia na máquina virtual do Azure. |
| WadLogsTable | Tabela | Logs escritos em código usando o ouvinte de rastreamento. |
| WADPerformanceCountersTable | Tabela | Contadores de desempenho. |
| WADWindowsEventLogsTable | Tabela | Logs de eventos do Windows. |
| wad-iis-failedreqlogfiles | Blob | Contém informações dos registros de solicitação com falha do IIS. |
| wad-iis-logfiles | Blob | Contém informações sobre registros IIS. |
| "personalizado" | Blob | Um recipiente personalizado baseado na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois de serem transferidos para o armazenamento. Por exemplo: 

* Gerenciador de Servidores no Visual Studio – Se tiver instalado as Ferramentas do Azure para o Microsoft Visual Studio, será possível usar o nó do Armazenamento do Azure no Gerenciador de Servidores para exibir os dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, veja [Procurando e gerenciando recursos de armazenamento com o Gerenciador de Servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Gerenciamento de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite trabalhar facilmente com os dados de Armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de Diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnósticos coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Próximas etapas
- Veja [Enviar dados da extensão de diagnósticos do Windows Azure para o Event Hubs](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre o encaminhamento de dados de monitoramento para o Azure Event Hubs.
