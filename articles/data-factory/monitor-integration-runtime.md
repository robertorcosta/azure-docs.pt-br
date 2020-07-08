---
title: Monitorar o tempo de execução de integração no Azure Data Factory
description: Saiba como monitorar diferentes tipos de runtime de integração no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: cfb40375fe841dd363681aea3d2cf6355046cd51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84113699"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorar um runtime de integração no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
O **runtime de integração** é a infraestrutura de computação usada pelo Azure Data Factory para fornecer diversas funcionalidades de integração de dados entre diferentes ambientes de rede. São oferecidos três tipos de runtimes de integração pelo Data Factory:

- Azure Integration runtime
- runtime de integração auto-hospedada
- runtime de integração do Azure-SSIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter o status de uma instância de runtime de integração (IR), execute o seguinte comando do PowerShell: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet retorna diferentes informações para diferentes tipos de runtime de integração. Este artigo explica as propriedades e os status para cada tipo de runtime de integração.  

## <a name="azure-integration-runtime"></a>Azure Integration runtime
O recurso de computação para um Azure Integration runtime é totalmente gerenciado com elasticidade no Azure. A tabela a seguir fornece descrições das propriedades retornadas pelo comando **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Propriedades
A tabela a seguir fornece descrições de propriedades retornadas pelo cmdlet para um Azure Integration runtime:

| Property | Descrição |
-------- | ------------- | 
| Nome | Nome do Azure Integration runtime. |  
| Estado | Status do Azure Integration runtime. | 
| Location | Local do Azure Integration runtime. Para obter detalhes sobre o local de Azure Integration runtime, consulte [Introdução ao runtime de integração](concepts-integration-runtime.md). |
| DataFactoryName | Nome do Data Factory ao qual o Azure Integration runtime pertence. | 
| ResourceGroupName | Nome do grupo de recursos ao qual o data factory pertence.  |
| Descrição | Descrição do runtime de integração.  |

### <a name="status"></a>Status
A tabela a seguir fornece os possíveis status do Azure Integration runtime:

| Status | Comentários/Cenários | 
| ------ | ------------------ |
| Online | O Azure Integration runtime está online e pronto para ser usado. | 
| Offline | O Azure Integration runtime está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>runtime de integração auto-hospedada
Esta seção fornece descrições para propriedades retornadas pelo cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> As propriedades retornadas e o status contêm informações sobre o runtime de integração auto-hospedada geral e cada nó no runtime.  

### <a name="properties"></a>Propriedades

A tabela a seguir apresenta descrições das Propriedades de monitoramento para **cada nó**:

| Property | Descrição | 
| -------- | ----------- | 
| Nome | Nome do runtime de integração auto-hospedada e nós associados a ele. O nó é um computador local do Windows em que o runtime de integração auto-hospedada está instalado. |  
| Status | O status do runtime de integração auto-hospedada geral e de cada nó. Exemplo: online/offline/Limited/etc. Para obter informações sobre esses status, consulte a próxima seção. | 
| Versão | A versão do runtime de integração auto-hospedada geral e de cada nó. A versão do runtime de integração auto-hospedada é determinada com base na versão da maioria dos nós no grupo. Se há nós com versões diferentes na configuração do runtime de integração auto-hospedada, somente os nós com o mesmo número de versão que o runtime de integração auto-hospedada funcionam corretamente. Os outros estão no modo limitado e precisam ser atualizados manualmente (somente caso a atualização automática falhe). | 
| Memória disponível | Memória disponível em um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. | 
| Utilização da CPU | Utilização da CPU de um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. |
| Rede (Entrada/Saída) | Utilização da rede de um nó de runtime de integração auto-hospedada. Esse valor é um instantâneo quase em tempo real. | 
| Trabalhos Simultâneos (Executando/Limite) | **Em execução**. Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. <br/><br/>**Limite**. Limite significa a máxima de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho do computador. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que as atividades estão atingindo o tempo limite mesmo quando há subutilização de memória, CPU ou rede. Essa funcionalidade também está disponível com runtime de integração auto-hospedada de nó único. |
| Função | Há dois tipos de funções em um runtime de integração auto-hospedada com vários nós – dispatcher e de trabalho. Todos os nós são de trabalho, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e distribuí-los para nós de trabalho diferentes. O nó dispatcher também é um nó de trabalho. |

Algumas configurações das propriedades fazem mais sentido quando há dois ou mais nós no runtime de integração auto-hospedada (ou seja, no cenário de expansão).

#### <a name="concurrent-jobs-limit"></a>Limite de trabalhos simultâneos

O valor padrão do limite de trabalhos simultâneos é definido com base no tamanho do computador. Os fatores usados para calcular esse valor dependem da quantidade de RAM e do número de núcleos de CPU do computador. Portanto, quanto mais núcleos e mais memória, maior será o limite padrão de trabalhos simultâneos.

Você escala horizontalmente aumentando o número de nós. Quando você aumenta o número de nós, o limite de trabalhos simultâneos é a soma dos valores de limite de trabalhos simultâneos de todos os nós disponíveis.  Por exemplo, se um nó permitir que você execute um máximo de 12 trabalhos simultâneos, adicionar mais três nós semelhantes permitirá que você execute um máximo de 48 trabalhos simultâneos (ou seja, 4 × 12). É recomendável aumentar o limite de trabalhos simultâneos apenas quando você observar um baixo uso de recursos com os valores padrão em cada nó.

Você pode substituir o valor padrão calculado no portal do Azure. Selecione Autor > Conexões > Integration Runtimes > Editar > Nós > Modificar valor de trabalhos simultâneos por nó. Você também pode usar o comando [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) do PowerShell.
  
### <a name="status-per-node"></a>Status (por nó)
A tabela a seguir fornece os possíveis status de um nó de runtime de integração auto-hospedada:

| Status | Descrição |
| ------ | ------------------ | 
| Online | O nó está conectado ao serviço Data Factory. |
| Offline | O nó está offline. |
| Atualizando | O nó está sendo atualizado automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido a um problema de HTTP da porta 8050, problema de conectividade do barramento de serviço ou problema de sincronização de credenciais. |
| Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria. |

Um nó pode ficar inativo quando ele não pode se conectar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (runtime de integração auto-hospedada geral)
A tabela a seguir fornece os possíveis status de um runtime de integração auto-hospedada. Esse status depende dos status de todos os nós que pertencem ao runtime. 

| Status | Descrição |
| ------ | ----------- | 
| Precisa de registro | Nenhum nó está registrado a esse runtime de integração auto-hospedada. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós no runtime de integração auto-hospedada estão em um estado íntegro. Esse status é um aviso de que alguns nós podem estar inativos. Esse status pode ser devido a um problema de sincronização de credenciais no nó dispatcher/de trabalho. |

Use o cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** para buscar o conteúdo JSON que contém as propriedades detalhadas do tempo de execução de integração auto-hospedado e seus valores de instantâneo durante o tempo de execução do cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Exemplo de saída (supõe que há dois nós associados a esse runtime de integração auto-hospedada):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>runtime de integração do Azure-SSIS
O runtime de integração do SSIS do Azure é um cluster totalmente gerenciado das máquinas virtuais (ou nós) do Azure dedicado a executar os pacotes de SSIS. Ele não executa nenhuma outra atividade do Azure Data Factory. Depois de provisionado, você pode consultar suas propriedades e monitorar os status geral/de nós específicos.

### <a name="properties"></a>Propriedades

| Propriedade/Status | Descrição |
| --------------- | ----------- |
| CreateTime | A hora UTC em que o runtime de integração do Azure-SSIS foi criado. |
| Nós | Os nós alocados/disponíveis de seu runtime de integração do Azure-SSIS com status específicos dos nós (iniciando/disponível/reciclando/indisponível) e erros acionáveis. |
| OtherErrors | Os erros acionáveis não específicos dos nós em seu runtime de integração do Azure-SSIS. |
| LastOperation | O resultado da última operação de iniciar/parar em seu runtime de integração do Azure-SSIS com erros acionáveis se houve falha. |
| Estado | O status geral (inicial/iniciando/iniciado/parando/parado) de seu runtime de integração do Azure-SSIS. |
| Location | O local do seu runtime de integração do Azure-SSIS. |
| NodeSize | O tamanho de cada nó do seu runtime de integração do Azure-SSIS. |
| NodeCount | O número de nós em seu runtime de integração do Azure-SSIS. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó em seu runtime de integração do Azure-SSIS. |
| CatalogServerEndpoint | O ponto de extremidade de seu banco de dados SQL/SQL existente Instância Gerenciada para hospedar o SSISDB. |
| CatalogAdminUserName | O nome de usuário do administrador do banco de dados SQL/SQL Instância Gerenciada existente. O serviço Data Factory usa essas informações para preparar e gerenciar o SSISDB por você. |
| CatalogAdminPassword | A senha de administrador do banco de dados SQL/SQL Instância Gerenciada existente. |
| CatalogPricingTier | O tipo de preço para SSISDB hospedado pelo banco de dados SQL.  Não aplicável ao SQL Instância Gerenciada que hospeda o SSISDB. |
| VNetId | A ID de recursos da rede virtual para o runtime de integração do Azure-SSIS ingressar. |
| Sub-rede | O nome da sub-rede para o runtime de integração do Azure-SSIS ingressar. |
| ID | A ID de recursos do seu runtime de integração do Azure-SSIS. |
| Tipo | O tipo (Gerenciado/Auto-hospedado) de seu runtime de integração do Azure-SSIS. |
| ResourceGroupName | O nome do seu grupo de recursos do Azure, no qual seu data factory e o runtime de integração do Azure-SSIS foram criados. |
| DataFactoryName | O nome de seu data factory do Azure. |
| Nome | O nome do seu runtime de integração do Azure-SSIS. |
| Descrição | A descrição do seu runtime de integração do Azure-SSIS. |

  
### <a name="status-per-node"></a>Status (por nó)

| Status | Descrição |
| ------ | ----------- | 
| Iniciando | Este nó está sendo preparado. |
| Disponível | Este nó está pronto para implantar/executar pacotes do SSIS. |
| Reciclando | Este nó está sendo reparado/está reiniciando. |
| Indisponível | Este nó não está pronto para implantar/executar pacotes do SSIS e tem problemas/erros acionáveis que você pode resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (runtime de integração do Azure-SSIS geral)

| Status geral | Descrição | 
| -------------- | ----------- | 
| Inicial | Os nós de seu runtime de integração do Azure-SSIS não foram alocados/preparados. | 
| Iniciando | Os nós de seu runtime de integração do Azure-SSIS estão sendo alocados/preparados e a cobrança foi iniciada. |
| Iniciado | Os nós de seu runtime de integração do Azure-SSIS foram alocados/preparados e estão prontos para implantar/executar pacotes do SSIS. |
| Parando  | Os nós de seu runtime de integração do Azure-SSIS estão sendo liberados. |
| Parado | Os nós de seu runtime de integração do Azure-SSIS foram liberados e a cobrança parou. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitorar o runtime de integração do Azure-SSIS no portal do Azure

As capturas de tela a seguir mostram como selecionar o IR do Azure-SSIS a ser monitorado e fornecer um exemplo das informações exibidas.

![Selecionar o runtime de integração do Azure-SSIS para monitorar](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Exibir informações sobre o runtime de integração do Azure-SSIS](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitorar o runtime de integração do Azure-SSIS com PowerShell

Usar um script como o exemplo a seguir para verificar o status do IR do Azure-SSIS.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Mais informações sobre o runtime de integração do Azure-SSIS

Consulte os artigos a seguir para saber mais sobre o runtime de integração do Azure-SSIS:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um Azure-SSIS IR e usa o banco de dados SQL para hospedar o catálogo do SSIS. 
- [Como: Criar um runtime de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o SQL Instância Gerenciada e ingressar o IR em uma rede virtual. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
- [Unir um IR do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md). Este artigo fornece informações conceituais sobre como unir um IR do Azure-SSIS a uma rede virtual do Azure. Ele também apresenta as etapas para usar o portal do Azure para configurar a rede virtual para que o IR do Azure-SSIS possa se unir à rede virtual. 

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para monitorar os pipelines de maneiras diferentes: 

- [Início rápido: criar um data Factory](quickstart-create-data-factory-dot-net.md).
- [Usar o Azure Monitor para monitorar os pipelines de Data Factory](monitor-using-azure-monitor.md)
