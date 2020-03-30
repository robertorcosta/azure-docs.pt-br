---
title: Habilite o Monitor Azure para um ambiente híbrido | Microsoft Docs
description: Este artigo descreve como você habilita o Monitor Azure para VMs para um ambiente de nuvem híbrida que contém uma ou mais máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480735"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Habilite o Monitor Azure para VMs para um ambiente híbrido

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo explica como ativar o Azure Monitor para VMs para máquinas virtuais ou computadores físicos hospedados em seu data center ou outro ambiente em nuvem. Ao final deste processo, você terá começado a monitorar suas máquinas virtuais em seu ambiente e aprender se elas estão enfrentando algum problema de desempenho ou disponibilidade.

Antes de começar, examine os [pré-requisitos](vminsights-enable-overview.md) e verifique se a sua assinatura e os recursos atendem aos requisitos. Examine os requisitos e os métodos de implantação do [agente do Linux e do Windows do Log Analytics](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados do Map são sempre transmitidos pelo agente Log Analytics para o serviço Azure Monitor, diretamente ou através do [gateway do Operations Management Suite](../../azure-monitor/platform/gateway.md) se suas políticas de segurança de TI não permitirem que computadores da rede se conectem à internet.

As etapas para concluir esta tarefa são resumidas da seguinte forma:

1. Instale o agente Log Analytics para Windows ou Linux. Antes de instalar o agente, revise o artigo de visão geral do [agente log analytics](../platform/log-analytics-agent.md) para entender os pré-requisitos do sistema e os métodos de implantação.

2. Baixar e instalar o Dependency Agent de Mapa do Azure Monitor para VMs para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Habilitar a coleta de contadores de desempenho.

4. Implantar o Azure Monitor para VMs.

>[!NOTE]
>As informações descritas neste artigo para a implantação do agente de dependência também são aplicáveis à [solução Mapa de Serviço](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows

Instale o Dependency Agent manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar esse arquivo executável sem opções, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de forma interativa.

>[!NOTE]
>*Os* privilégios do administrador são necessários para instalar ou desinstalar o agente.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o `/?` programa de instalação com o parâmetro, **digite InstallDependencyAgent-Windows.exe /?**

Os arquivos do Dependency Agent do Windows são instalados em *C:\Arquivos de Programas\Microsoft Dependency Agent* por padrão. Se o agente Dependency não for iniciada após o término da configuração, verifique os registros para obter informações detalhadas sobre erros. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux

O Dependency Agent é instalado em servidores Linux por meio de *InstallDependencyAgent-Linux64.bin*, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.
>

| Parâmetro | Descrição |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -S | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Por exemplo, para executar o `-help` programa de instalação com o parâmetro, **digite InstallDependencyAgent-Linux64.bin -help**.

Instale o agente de dependência do `sh InstallDependencyAgent-Linux64.bin`Linux como raiz executando o comando .

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Nos agentes Linux, o diretório de log é */var/opt/microsoft/dependency-agent/log*.

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Location |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente de dependência em vários servidores ao mesmo tempo, o exemplo de script a seguir é fornecido para baixar e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de Shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Para implantar o agente de dependência por meio da Desired State Configuration (DSC), você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Habilitar contadores de desempenho

Se o espaço de trabalho do Log Analytics referenciado pela solução ainda não estiver configurado para coletar os contadores de desempenho necessários para a solução, você precisará habilitá-los. Você pode fazê-lo de duas maneiras:
* Manualmente, conforme descrito em [Fontes de dados de desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script PowerShell disponível na [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Implantar o Azure Monitor para VMs

Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no espaço de trabalho do Log Analytics.

Se você não sabe como implantar recursos usando um modelo, consulte:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para usar o Cli Azure, primeiro você precisa instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Para instalar ou atualizar o Cli do Azure, consulte [Instalar o Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Salve esse arquivo como *installsolutionsforvminsights.json* em uma pasta local.

1. Capture os valores de *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. O valor para *WorkspaceName* é o nome do seu espaço de trabalho do Log Analytics. O valor de *WorkspaceLocation* é a região na qual o workspace foi definido.

1. Você está pronto para implantar esse modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A mudança de configuração pode levar alguns minutos para ser concluída. Quando termina, uma mensagem é exibida semelhante ao seguinte e inclui o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
   Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição do estado de integridade e das métricas para o computador híbrido.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>VM não aparece no mapa

Se a instalação do seu agente de dependência for bem sucedida, mas você não ver seu computador no mapa, diagnostice o problema seguindo essas etapas.

1. O Agente de Dependência foi instalado com êxito? Confirme isso verificando se o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Agente de Dependência da Microsoft".

    **Linux**: procure o processo "microsoft-dependency-agent" em execução.

2. Você está no [nível de preços gratuitos do Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) O plano Free permite até cinco computadores exclusivos. Quaisquer computadores subseqüentes não aparecerão no mapa, mesmo que os cinco anteriores não estejam mais enviando dados.

3. O computador está enviando dados de log e perf para o Azure Monitor Logs? Execute a seguinte consulta para o seu computador:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Ele retornou um ou mais resultados? Os dados são recentes? Nesse caso, o agente log analytics está operando corretamente e se comunicando com o serviço. Caso contrário, verifique o agente no servidor: [Solução de problemas do agente do Log Analytics para Windows](../platform/agent-windows-troubleshoot.md) ou [Solução de problemas do agente do Log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Computador aparece no mapa, mas não tem processos

Se você ver seu servidor no mapa, mas ele não tiver nenhum processo ou dados de conexão, isso indica que o agente Dependency está instalado e em execução, mas o driver do kernel não carregou.

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o arquivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.


## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está ativado para suas máquinas virtuais, essas informações estão disponíveis para análise com o Monitor Azure para VMs.

- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).

- Para identificar gargalos e utilização geral com o desempenho da VM, consulte Exibir o desempenho da VM do [Azure](vminsights-performance.md).
