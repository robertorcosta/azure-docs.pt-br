---
title: Visão geral do agente de Azure Monitor
description: Visão geral do AMA (agente de Azure Monitor), que coleta dados de monitoramento do sistema operacional convidado de máquinas virtuais.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ff70beef89f6db240db244de1e11e54193858be0
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705768"
---
# <a name="azure-monitor-agent-overview-preview"></a>Visão geral do agente de Azure Monitor (versão prévia)
O agente de Azure Monitor (AMA) coleta dados de monitoramento do sistema operacional convidado de máquinas virtuais e as entrega ao Azure Monitor. Este artigo fornece uma visão geral do agente de Azure Monitor, incluindo como instalá-lo e como configurar a coleta de dados.

## <a name="relationship-to-other-agents"></a>Relação com outros agentes
O agente de Azure Monitor substitui os seguintes agentes que são usados atualmente pelo Azure Monitor para coletar dados de convidado de máquinas virtuais:

- [Log Analytics Agent](log-analytics-agent.md) – envia dados para log Analytics espaço de trabalho e oferece suporte a soluções de Azure monitor para VMs e monitoramento.
- [Extensão de diagnóstico](diagnostics-extension-overview.md) – envia dados para Azure monitor métricas (somente Windows), hubs de eventos do Azure e armazenamento do Azure.
- [Agente Telegraf](collect-custom-metrics-linux-telegraf.md) – envia dados para Azure monitor métricas (somente Linux).

Além de consolidar essa funcionalidade em um único agente, o agente de Azure Monitor fornece os seguintes benefícios em relação aos agentes existentes:

- Escopo do monitoramento. Configure centralmente a coleta para diferentes conjuntos de dados de diferentes conjuntos de VMs.  
- Hospedagem múltipla do Linux: enviar dados de VMs do Linux para vários espaços de trabalho.
- Filtragem de eventos do Windows: use consultas XPATH para filtrar quais eventos do Windows são coletados.
- Gerenciamento de extensão aprimorado: o agente de Azure Monitor usa um novo método de tratamento de extensibilidade que é mais transparente e controlável do que os pacotes de gerenciamento e plug-ins do Linux nos agentes de Log Analytics atuais.

### <a name="changes-in-data-collection"></a>Alterações na coleta de dados
Os métodos para definir a coleta de dados para os agentes existentes são distintos diferentes uns dos outros, e cada um tem desafios que são resolvidos com o agente de Azure Monitor.

- Log Analytics Agent obtém sua configuração de um espaço de trabalho Log Analytics. Isso é fácil de configurar de forma centralizada, mas difícil de definir definições independentes para diferentes máquinas virtuais. Ele só pode enviar dados para um espaço de trabalho Log Analytics.

- A extensão de diagnóstico tem uma configuração para cada máquina virtual. Isso é fácil de definir definições independentes para diferentes máquinas virtuais, mas difícil de gerenciar de forma centralizada. Ele só pode enviar dados para Azure Monitor métricas, hubs de eventos do Azure ou armazenamento do Azure. Para agentes do Linux, o agente Telegraf de código-fonte aberto é necessário para enviar dados para Azure Monitor métricas.

O agente de Azure Monitor usa [as regras de coleta de dados (DCR)](data-collection-rule-overview.md) para configurar os dados a serem coletados de cada agente. As regras de coleta de dados habilitam a gerenciabilidade das configurações de coleta em escala e ainda habilitam configurações exclusivas e com escopo para subconjuntos de computadores. Eles são independentes do espaço de trabalho e independentes da máquina virtual, o que permite que eles sejam definidos uma vez e reutilizados em computadores e ambientes. Consulte [Configurar a coleta de dados para o agente de Azure monitor (versão prévia)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Limitações atuais
As seguintes limitações se aplicam durante a visualização pública do agente de Azure Monitor:

- O agente de Azure Monitor não oferece suporte a soluções e informações como Azure Monitor para VMs e a central de segurança do Azure. O único cenário com suporte no momento é coletar dados usando as regras de coleta de dados que você configura. 
- As regras de coleta de dados devem ser criadas na mesma região que qualquer Log Analytics espaço de trabalho usado como destino.
- No momento, somente as máquinas virtuais do Azure têm suporte. As máquinas virtuais locais, os conjuntos de dimensionamento de máquinas virtuais, o Arc para servidores, o serviço kubernetes do Azure e outros tipos de recursos de computação não têm suporte no momento.
- A máquina virtual deve ter acesso aos seguintes pontos de extremidade HTTPS:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Coexistência com outros agentes
O agente de Azure Monitor pode coexistir com os agentes existentes para que você possa continuar a usar sua funcionalidade existente durante a avaliação ou migração. Isso é particularmente importante devido às limitações da visualização pública no suporte a soluções existentes. Você deve ter cuidado ao coletar dados duplicados, pois isso poderia distorcer os resultados da consulta e resultar em cobranças adicionais para a ingestão e retenção de dados.

Por exemplo, Azure Monitor para VMs usa o agente de Log Analytics para enviar dados de desempenho para um espaço de trabalho Log Analytics. Você também pode ter configurado o espaço de trabalho para coletar eventos do Windows e de syslog de agentes. Se você instalar o agente de Azure Monitor e criar uma regra de coleta de dados para esses mesmos eventos e dados de desempenho, isso resultará em dados duplicados.


## <a name="costs"></a>Custos
Não há nenhum custo para Azure Monitor Agent, mas você pode incorrer em encargos pelos dados ingeridos. Consulte [Azure monitor preços](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes sobre coleta e retenção de dados de log Analytics e métricas de clientes.

## <a name="data-sources-and-destinations"></a>Fontes de dados e destinos
A tabela a seguir lista os tipos de dados que você pode coletar atualmente com o agente de Azure Monitor usando regras de coleta de dados e onde você pode enviar esses dados. Veja [o que é monitorado pelo Azure monitor?](../monitor-reference.md) para obter uma lista de informações, soluções e outras soluções que usam o agente de Azure monitor para coletar outros tipos de dados.


O agente de Azure Monitor envia dados para Azure Monitor métricas ou um espaço de trabalho Log Analytics que oferece suporte a logs de Azure Monitor.

| Fonte de dados | Destinos | Descrição |
|:---|:---|:---|
| Desempenho        | Métricas do Azure Monitor<br>Workspace do Log Analytics | Valores numéricos que medem o desempenho de diferentes aspectos do sistema operacional e das cargas de trabalho. |
| Log de eventos do Windows | Workspace do Log Analytics | Informações enviadas ao sistema de registro de evento do Windows. |
| syslog             | Workspace do Log Analytics | Informações enviadas ao sistema de registro de evento do Linux. |


## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
Os sistemas operacionais a seguir têm suporte no momento pelo agente de Azure Monitor.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14, 4 LTS, 16, 4 LTS, 18, 4 LTS

> [!IMPORTANT]
> <sup>1</sup> Para que essas distribuições enviem dados syslog, você deve remover o rsyslog e instalar o syslog-ng.


## <a name="security"></a>Segurança
O agente de Azure Monitor não requer nenhuma chave, mas requer uma [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Você deve ter uma identidade gerenciada atribuída pelo sistema habilitada em cada máquina virtual antes de implantar o agente.


## <a name="install-the-azure-monitor-agent"></a>Instalar o agente de Azure Monitor
O agente de Azure Monitor é implementado como uma [extensão de VM do Azure](../../virtual-machines/extensions/overview.md) com os detalhes na tabela a seguir. 

| Propriedade | Windows | Linux |
|:---|:---|:---|
| Publicador | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

Instale o agente de Azure Monitor usando qualquer um dos métodos para instalar agentes de máquina virtual, incluindo o seguinte usando o PowerShell ou a CLI. Como alternativa, você pode instalar o agente e configurar a coleta de dados em máquinas virtuais em sua assinatura do Azure usando o portal com o procedimento descrito em [Configurar coleta de dados para o agente de Azure monitor (versão prévia)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Próximas etapas

- [Crie uma regra de coleta de dados](data-collection-rule-azure-monitor-agent.md) para coletar dados do agente e enviá-los para Azure monitor.
