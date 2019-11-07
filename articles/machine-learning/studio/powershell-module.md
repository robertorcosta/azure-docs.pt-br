---
title: Módulos do PowerShell
titleSuffix: ML Studio (classic) Azure
description: Use o PowerShell para criar e gerenciar espaços de trabalho do Azure Machine Learning Studio (clássico), experimentos, serviços Web e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: 7de760e9bdaaf9a00cf170b3383e0e93e8806a87
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670842"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Módulos do PowerShell para Azure Machine Learning Studio (clássico)

Usando módulos do PowerShell, você pode gerenciar programaticamente seus recursos e ativos de estúdio (clássico), como espaços de trabalho, conjuntos de fontes e serviços Web.

Você pode interagir com a versão clássica dos recursos do estúdio usando três módulos do PowerShell:

* [Az do Azure PowerShell](#az-rm) lançado em 2018, inclui todas as funcionalidades do AzureRM, embora com nomes de cmdlet diferentes
* [AzureRM](#az-rm) lançado em 2016, substituído pelo cmdlet AZ do PowerShell
* [PowerShell do Azure Machine Learning clássico](#classic) lançado em 2016

Embora esses módulos do PowerShell tenham algumas semelhanças, cada um é projetado para cenários específicos. Este artigo descreve as diferenças entre os módulos do PowerShell e ajuda você a decidir quais escolher.  

Verifique a [tabela de suporte](#support-table) abaixo para ver quais recursos são compatíveis com cada módulo. 

## <a name="az-rm"></a> Az e AzureRM do Azure PowerShell

O Az agora é o módulo do PowerShell indicado para interagir com o Azure e inclui toda a funcionalidade anterior do AzureRM. O AzureRM continuará a receber as correções de bugs, mas não receberá nenhum novo recurso ou cmdlet.  O Az e o AzureRM gerenciam soluções implantadas usando o modelo de implantação do **Azure Resource Manager**. Esses recursos incluem os espaços de trabalho do estúdio (clássico) e os novos serviços Web do Studio (clássico). 

O PowerShell clássico pode ser instalado juntamente com AZ ou AzureRM para cobrir os tipos de recursos "New" e "Classic". No entanto, não é recomendável ter o Az e o AzureRM instalados ao mesmo tempo. Para decidir entre o Az e o AzureRM, a Microsoft recomenda o Az para todas as implantações futuras.  Saiba mais sobre AZ versus AzureRM e o caminho de migração na [introdução ao Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Para começar a trabalhar com o Az, siga as [instruções de instalação do Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell clássico

O [módulo clássico do PowerShell](https://aka.ms/amlps) do Studio (clássico) permite que você gerencie recursos implantados usando o **modelo de implantação clássico**. Esses recursos incluem ativos de usuário do estúdio (clássico), serviços Web "clássicos" e pontos de extremidade de serviço Web "clássicos".

No entanto, a Microsoft recomenda que você use o modelo de implantação do Gerenciador de recursos para todos os recursos futuros para simplificar a implantação e o gerenciamento de recursos. Se você quiser saber mais sobre os modelos de implantação, confira o artigo [Azure Resource Manager versus implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Para começar a usar o PowerShell clássico, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell.

O PowerShell clássico pode ser instalado juntamente com AZ ou AzureRM para cobrir os tipos de recursos "New" e "Classic".

## <a name="support-table"></a> Tabela de compatibilidade do PowerShell


| | **Az** |  **Implantação clássica do PowerShell** |
| --- | --- | --- |
| Criar/excluir espaços de trabalho | [Modelos do Gerenciador de Recursos](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gerenciar planos de compromisso do espaço de trabalho | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gerenciar os usuários do workspace |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gerenciar serviços Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("novos" serviços Web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(serviços Web "clássicos") |
| Gerenciar pontos de extremidade de serviço Web/chaves |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gerenciar conjuntos de usuários/modelos treinados| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gerenciar experimentos do usuário |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gerenciar módulos personalizados | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação completa deste módulo do PowerShell:
* [Implantação clássica do PowerShell](https://aka.ms/amlps)
* [Az do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
