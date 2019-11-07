---
title: Reconfigurar o tempo de execução de integração do Azure-SSIS
description: Saiba como reconfigurar o tempo de execução de integração do Azure-SSIS no Azure Data Factory depois de já tê-lo provisionado.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 09ef8560edc47a6bb12fad782ae3d34987471127
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672548"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurar o tempo de execução de integração do Azure-SSIS
Este artigo descreve como reconfigurar um tempo de execução de integração existente do Azure-SSIS. Para criar um IR (tempo de execução de integração) do Azure-SSIS no Azure Data Factory, confira [Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>IU do Data Factory 
Use a interface do usuário do Data Factory para parar, editar/reconfigurar ou excluir um IR do Azure SSIS. 

1. Na **interface do usuário do data Factory**, alterne para a guia **Editar** . Para iniciar a interface do usuário do Data Factory, clique em **criar & monitor** na home page do data Factory.
2. No painel esquerdo, clique em **Conexões**.
3. No painel direito, mude para os **Tempos de Execução de Integração**. 
4. Use os botões na coluna Ações para **parar**, **editar** ou **excluir** o tempo de execução de integração. O botão **Código** na coluna **Ações** permite que você exiba a definição de JSON associada ao tempo de execução de integração.  
    
    ![Ações para IR do Azure SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar um IR do Azure SSIS
1. Pare o tempo de execução de integração clicando em **Parar** na coluna **Ações**. Para atualizar a exibição de lista, clique em **Atualizar** na barra de ferramentas. Depois que o IR for interrompido, você verá que a primeira ação permite iniciar o IR. 

    ![Ações para IR do Azure SSIS – após a interrupção](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edite/reconfigure o IR clicando no botão **Editar** na coluna **Ações**. Na janela **Configuração do Tempo de Execução de Integração**, altere as configurações (por exemplo, tamanho do nó, número de nós ou número máximo de execuções paralelas por nó). 
3. Para reiniciar o IR, clique no botão **Iniciar** na coluna **Ações**.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de provisionar e iniciar uma instância do tempo de execução de integração do Azure-SSIS, você pode reconfigurá-lo executando uma sequência de cmdlets do PowerShell `Stop` - `Set` - `Start` consecutivamente. Por exemplo, o script do PowerShell a seguir altera o número de nós alocados para a instância de tempo de execução de integração do Azure-SSIS para cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar um IR do Azure-SSIS

1. Primeiro, pare o tempo de execução de integração do Azure-SSIS usando o cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Este comando libera todos os seus nós e interrompe a cobrança.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Em seguida, reconfigure o Azure-SSIS IR usando o cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . O comando de exemplo a seguir escala horizontalmente um tempo de execução de integração do Azure-SSIS para cinco nós.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Em seguida, inicie o tempo de execução de integração do Azure-SSIS usando o cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Esse comando aloca todos os seus nós para executar pacotes do SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Excluir um IR do Azure-SSIS
1. Primeiro, liste todos os IRs do Azure SSIS em seu data factory.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Em seguida, pare todos os IRs do Azure SSIS em seu data factory.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Em seguida, remova todos os IRs do Azure SSIS de seu data factory um por um.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Por fim, remova o data factory.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Se tiver criado um novo grupo de recursos, remova-o.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Próximas etapas
Confira estes tópicos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure e unir o IR a uma rede virtual. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa unir-se à rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
 
