---
title: Usar o armazenamento de BLOB para o IIS e o armazenamento de tabelas para eventos no Azure Monitor | Microsoft Docs
description: Azure Monitor pode ler os logs dos serviços do Azure que gravam diagnósticos em armazenamento de tabela ou logs do IIS gravados no armazenamento de BLOBs.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 2d2d8d43cb5ee91810d639c4ee8925d185987954
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893545"
---
# <a name="collect-azure-resource-logs-from-azure-storage"></a>Coletar logs de recursos do Azure do armazenamento do Azure

Azure Monitor pode ler os logs para os seguintes serviços que gravam diagnósticos em armazenamento de tabela ou logs do IIS gravados no armazenamento de BLOBs:

* Clusters do Service Fabric (visualização)
* Máquinas virtuais
* Funções Web/de Trabalho

Antes que Azure Monitor possa coletar dados em um espaço de trabalho Log Analytics para esses recursos, o diagnóstico do Azure deve ser habilitado.

Depois que o diagnóstico estiver habilitado, você poderá usar o portal do Azure ou o PowerShell configurar o espaço de trabalho para coletar os logs.

O Diagnóstico do Azure é uma extensão do Azure que permite coletar dados de diagnóstico de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser coletados por Azure Monitor.

Para Azure Monitor coletar esses logs de Diagnóstico do Azure, os logs devem estar nos seguintes locais:

| Tipo de Log | Tipo de recurso | Location |
| --- | --- | --- |
| Logs do IIS |Máquinas virtuais <br> Funções da Web <br> Funções de trabalho |wad-iis-logfiles (Armazenamento de Blobs) |
| syslog |Máquinas virtuais |LinuxsyslogVer2v0 (Armazenamento de Tabelas) |
| Eventos operacionais do Service Fabric |Nós do Service Fabric |WADServiceFabricSystemEventTable |
| Eventos dos Reliable Actors do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableActorEventTable |
| Arquitetura de Serviços Confiáveis do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableServiceEventTable |
| Logs de Eventos do Windows |Nós do Service Fabric <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (Armazenamento de Tabelas) |
| Logs do ETW do Windows |Nós do Service Fabric <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (Armazenamento de Tabelas) |

> [!NOTE]
> Atualmente, não há suporte para logs do IIS nos sites do Azure.
>
>

Para máquinas virtuais, você também tem a opção de instalar o [agente do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) em sua máquina virtual para habilitar insights adicionais. Além de ser capaz de analisar os logs do IIS e os Logs de Eventos, também será possível executar outras análises, inclusive controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS

Use o procedimento a seguir para habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o Portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para habilitar os diagnósticos do Azure em uma máquina virtual com o Portal do Azure

1. Instale o Agente de VM quando criar uma máquina virtual. Se a máquina virtual já existir, verifique se o Agente de VM já está instalado.

   * No portal do Azure, navegue para criar uma máquina virtual, selecione **Configuração Opcional**, depois selecione **Diagnóstico** e defina o **Status** como **Ativo**.

     Após a conclusão, a VM fará com que a extensão de diagnóstico do Azure seja instalada e esteja em execução. Essa extensão é responsável por coletar seus dados de diagnóstico.
2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e, em seguida, configurar o log de eventos, execute as seguintes etapas:

   1. Selecione a VM.
   2. Clique em **Monitoramento**.
   3. Clique em **Diagnóstico**.
   4. Defina o **Status** como **ATIVO**.
   5. Selecione cada log de diagnóstico que deseja coletar.
   6. Clique em **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Habilitar diagnóstico do Azure em uma função web para coleta de eventos e log do IIS

Consulte [How To Enable Diagnostics in a Cloud Service (Como habilitar o diagnóstico em um serviço de nuvem)](../../cloud-services/cloud-services-dotnet-diagnostics.md) para ver etapas gerais sobre como habilitar o diagnóstico do Azure. As instruções abaixo usam essas informações e as personalizam para uso com o Log Analytics.

Com o diagnóstico do Azure habilitado:

* Logs do IIS são armazenados por padrão, e os dados de log são transferidos ao intervalo de transferência de scheduledTransferPeriod.
* Logs de eventos do Windows não são transferidos por padrão.

### <a name="to-enable-diagnostics"></a>Para habilitar o diagnóstico

Para habilitar Logs de eventos do Windows ou alterar o scheduledTransferPeriod, configure o diagnóstico do Azure usando o arquivo de configuração XML (wadcfg), conforme mostra a [Etapa 4: Criar seu arquivo de configuração do Diagnostics e instalar a extensão](../../cloud-services/cloud-services-dotnet-diagnostics.md)

O arquivo de configuração de exemplo a seguir coleta os Logs do IIS e todos os Eventos dos logs do Aplicativo e do Sistema:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Certifique-se de que ConfigurationSettings especifique uma conta de armazenamento assim como no exemplo a seguir:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores **AccountName** e **AccountKey** são encontrados no Portal do Azure, no painel da conta de armazenamento, em Gerenciar Chaves de Acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois que a configuração de diagnóstico atualizada for aplicada ao serviço de nuvem e estiver gravando diagnósticos no armazenamento do Azure, você estará pronto para configurar o espaço de trabalho Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usar o Portal do Azure para coletar logs do Armazenamento do Azure

Você pode usar o portal do Azure para configurar um espaço de trabalho do Log Analytics no Azure Monitor para coletar os logs para os seguintes serviços do Azure:

* Clusters do Service Fabric
* Máquinas virtuais
* Funções Web/de Trabalho

No Portal do Azure, navegue até o espaço de trabalho do Log Analytics e execute as seguintes tarefas:

1. Clique em *Logs das contas de armazenamento*
2. Clique na tarefa *Adicionar*
3. Selecione a conta de armazenamento que contém os logs de diagnóstico
   * Essa conta pode ser uma conta de armazenamento clássica ou uma conta de armazenamento do Azure Resource Manager
4. Selecione o tipo de dados que você deseja coletar logs para
   * As escolhas são Logs do IIS, Eventos, Syslog (Linux), Logs do ETW e Eventos do Service Fabric
5. O valor de origem será preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para salvar a configuração

Repita as etapas 2-6 para contas de armazenamento e tipos de dados adicionais que você deseja coletar no espaço de trabalho.

Em aproximadamente 30 minutos, você poderá ver os dados da conta de armazenamento no espaço de trabalho Log Analytics. Você só verá os dados gravados no armazenamento após a configuração ser aplicada. O espaço de trabalho não lê os dados preexistentes da conta de armazenamento.

> [!NOTE]
> O portal não valida se a origem existe na conta de armazenamento ou se novos dados estão sendo gravados.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Use as etapas em [Configurando Azure monitor para indexar o diagnóstico do Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) para usar o PowerShell para ler do diagnóstico do Azure que são gravados no armazenamento de tabelas.

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure.
Para obter mais informações, consulte [Habilitando o diagnóstico em Máquinas Virtuais do Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

É possível habilitar e atualizar o diagnóstico do Azure usando o seguinte script do PowerShell.
Você também pode usar esse script com uma configuração de log personalizada.
Modifique o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.
O script usando os cmdlets para máquinas virtuais clássicas.

Revise o exemplo de script a seguir, copie-o, modifique-o conforme necessário, salve o exemplo como um arquivo de script do PowerShell e execute o script.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Próximos passos

* [Coletar logs e métricas para serviços do Azure](collect-azure-metrics-logs.md) para serviços do Azure com suporte.
* [Habilitar Soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.