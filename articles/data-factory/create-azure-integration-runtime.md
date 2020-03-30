---
title: Crie o tempo de execução da integração do Azure na fábrica de dados do Azure
description: Saiba como criar o Integration Runtime do Azure no Azure Data Factory, que é usado para copiar dados e expedir atividades de transformação.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260755"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Integration Runtime do Azure
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter mais informações sobre o IR, consulte [Integration Runtime](concepts-integration-runtime.md).

O IR do Azure fornece uma computação totalmente gerenciada para executar a movimentação de dados nativamente e expedir atividades de transformação de dados para serviços de computação como o HDInsight. Ele é hospedado no ambiente do Azure e dá suporte à conexão para recursos no ambiente de rede pública com pontos de extremidade públicos acessíveis.

Este documento apresenta como você pode criar e configurar o Integration Runtime do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>IR do Azure padrão
Por padrão, cada data factory tem um IR do Azure no back-end que dá suporte a operações em armazenamentos de dados na nuvem e serviços de computação na rede pública. O local desse IR do Azure é resolvido automaticamente. Se a propriedade **connectVia** não for especificada na definição do serviço vinculado, o IR do Azure padrão será usado. Você precisa criar explicitamente um IR do Azure apenas quando deseja definir explicitamente o local do IR ou se desejar agrupar virtualmente as execuções de atividade em diferentes IRs para fins de gerenciamento. 

## <a name="create-azure-ir"></a>Criar IR do Azure
Integração O tempo de execução pode ser criado usando o **cmdlet Set-AzDataFactoryV2IntegrationRuntime** PowerShell. Para criar um IR do Azure, você especifica o nome, o local e o tipo para o comando. Aqui está um exemplo de comando para criar um IR do Azure com o local definido como "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o IR do Azure, o tipo deve ser definido como **Managed**. Você não precisa especificar detalhes de computação porque ele é totalmente gerenciado elasticamente na nuvem. Especifique os detalhes de computação como o tamanho do nó e a contagem do nó quando desejar criar o IR Azure-SSIS. Para obter mais informações, consulte [Criar e configurar o IR do Azure-SSIS](create-azure-ssis-integration-runtime.md).

Você pode configurar um Azure IR existente para alterar sua localização usando o cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Para obter mais informações sobre o local de um IR do Azure, consulte [Introdução ao Integration Runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Usar o IR do Azure

Depois de criar um IR do Azure, você pode referenciá-lo em sua definição de Serviço Vinculado. Abaixo está um exemplo de como você pode referenciar o Integration Runtime do Azure criado acima de um Serviço Vinculado do Armazenamento do Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para criar outros tipos de runtimes de integração:

- [Crie tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md)
- [Criar o Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
