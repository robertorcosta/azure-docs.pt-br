---
title: Módulos do PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Use o PowerShell para criar e gerenciar espaços de trabalho (clássicos) do Azure Machine Learning Studio (clássicos), experimentos, serviços web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204283"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Módulos PowerShell para Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usando módulos PowerShell, você pode gerenciar programáticamente seus recursos e ativos do Studio (clássico), como espaços de trabalho, conjuntos de dados e serviços web.

Você pode interagir com os recursos do Studio (clássico) usando três módulos Powershell:

* [Az do Azure PowerShell](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016, substituído por PowerShell Az
* [PowerShell do Azure Machine Learning clássico](#classic) lançado em 2016

Embora esses módulos PowerShell tenham algumas semelhanças, cada um foi projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e ajuda você a decidir quais escolher.  

Verifique a [tabela de suporte](#support-table) abaixo para ver quais recursos são compatíveis com cada módulo. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Az e AzureRM do Azure PowerShell

O Az agora é o módulo do PowerShell indicado para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber as correções de bugs, mas não receberá nenhum novo recurso ou cmdlet.  O Az e o AzureRM gerenciam soluções implantadas usando o modelo de implantação do **Azure Resource Manager**. Esses recursos incluem espaços de trabalho studio (clássicos) e studio (clássico) serviços web "Novos". 

O clássico PowerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos". No entanto, não é recomendável ter o Az e o AzureRM instalados ao mesmo tempo. Para decidir entre o Az e o AzureRM, a Microsoft recomenda o Az para todas as implantações futuras.  Saiba mais sobre Az versus AzureRM e o caminho de migração em [introdução ao Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar a trabalhar com o Az, siga as [instruções de instalação do Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell clássico

O módulo clássico do Studio (clássico) [PowerShell](https://aka.ms/amlps) permite gerenciar os recursos implantados usando o **modelo clássico de implantação**. Esses recursos incluem ativos de usuário do Studio (clássico), serviços web "clássicos" e pontos finais de serviços web "clássicos".

No entanto, a Microsoft recomenda que você use o modelo de implantação do Resource Manager para todos os recursos futuros para simplificar a implantação e o gerenciamento de recursos. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Para começar a usar o PowerShell clássico, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell.

O clássico PowerShell pode ser instalado ao lado de Az ou AzureRM para cobrir os tipos de recursos "novos" e "clássicos".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabela de compatibilidade do PowerShell


| | **Az** |  **Clássico do PowerShell** |
| --- | --- | --- |
| Criar/excluir espaços de trabalho | [Modelos do Gerenciador de Recursos](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerencie planos de compromisso no espaço de trabalho | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerenciar os usuários do workspace |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerenciar serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("novos" serviços web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(Serviços web "clássicos") |
| Gerenciar pontos finais/chaves do serviço web |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerenciar conjuntos de dados do usuário/modelos treinados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerenciar experimentos de usuário |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerenciar módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação completa destes módulos PowerShell:
* [Clássico do PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
