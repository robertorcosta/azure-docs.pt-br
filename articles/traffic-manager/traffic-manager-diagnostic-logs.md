---
title: Habilitar o log de diagnósticos no Gerenciador de Tráfego do Microsoft Azure
description: Saiba como habilitar o log de diagnósticos para o perfil do Gerenciador de Tráfego e acessar os arquivos de log que são criados como resultado.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938668"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Habilitar o log de diagnósticos no Gerenciador de Tráfego do Microsoft Azure

Este artigo descreve como habilitar o log de diagnósticos e os dados do log de acesso para um perfil do Gerenciador de Tráfego.

Os logs de diagnósticos do Gerenciador de Tráfego do Azure podem fornecer informações sobre o comportamento do recurso de perfil do Gerenciador de Tráfego. Por exemplo, é possível usar os dados do log do perfil para determinar por que as investigações individuais atingiram o tempo limite em um ponto de extremidade.

## <a name="enable-diagnostic-logging"></a>Habilitar o registro em log de diagnósticos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell pelo computador, será necessário ter o módulo do Azure PowerShell do Azure, 1.0.0 ou posterior. É possível executar `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, também será necessário executar `Login-AzAccount` entrar no Azure.

1. **Recuperar o perfil do Gerenciador de Tráfego:**

    Para habilitar o log de diagnósticos, será necessário ter a ID de um perfil do Gerenciador de Tráfego. Recuperar o perfil do Gerenciador de Tráfego para o qual você quer habilitar o log de diagnósticos [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). A saída inclui as informações da ID do perfil do Gerenciador de Tráfego.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Habilitar o log de diagnósticos para o perfil do Gerenciador de Tráfego:**

    Habilite o log de diagnósticos para o perfil do Gerenciador de Tráfego, usando a ID obtida na etapa anterior com [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). O comando a seguir armazena logs detalhados do perfil do Gerenciador de Tráfego em uma conta de Armazenamento do Azure especificada. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verificar configurações de diagnóstico:**

      Verifique as configurações de diagnóstico para o perfil do Gerenciador de Tráfego, usando [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). O comando a seguir exibe as categorias registradas em log para um recurso.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Assegure-se de que todas as categorias de log associadas ao recurso de perfil do Gerenciador de Tráfego sejam exibidas como habilitadas. Além disso, verifique se a conta de armazenamento está configurada corretamente.

## <a name="access-log-files"></a>Acessar arquivos de log
1. Entre no [portal do Azure](https://portal.azure.com). 
1. Navegue até a conta de Armazenamento do Azure no portal.
2. Na página de **Visão Geral** da conta de armazenamento do Azure, em **Serviços**, selecione **Blobs**.
3. Para **Contêineres**, selecione **insights-logs-probehealthstatusevents**, navegue até o arquivo PT1H.json e clique em **Baixar** para baixar e salvar uma cópia desse arquivo de log.

    ![Acessar arquivos de log do perfil do Gerenciador de Tráfego de um armazenamento de blobs](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Esquema de log do Gerenciador de Tráfego

Todos os logs de diagnóstico disponíveis por meio do Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço emitir propriedades exclusivas para seus próprios eventos. Para o esquema de logs de diagnóstico de nível superior, consulte [Serviços, esquemas e categorias com suporte para Logs de Diagnóstico do Azure](../azure-monitor/platform/tutorial-dashboards.md).

A tabela a seguir inclui o esquema de logs específico para o recurso de perfil do Gerenciador de Tráfego do Azure.

|||||
|----|----|---|---|
|**Nome do Campo**|**Tipo de campo**|**Definição**|**Exemplo**|
|EndpointName|String|O nome do ponto de extremidade do Gerenciador de Tráfego cujo status de integridade está sendo registrado.|*myPrimaryEndpoint*|
|Status|String|O status de integridade do ponto de extremidade do Gerenciador de Tráfego que foi investigado. O status pode ser **Operante** ou **Inoperante**.|**Operante**|
|||||

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

