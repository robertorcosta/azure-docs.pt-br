---
title: Exemplos do Azure PowerShell – Redefinir Serviços de Nuvem do Azure (suporte estendido)
description: Scripts de exemplo para redefinir uma implantação do Serviço de Nuvem do Azure (suporte estendido)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881478"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Redefinir um Serviço de Nuvem do Azure (suporte estendido) 
Esses exemplos abordam várias maneiras de redefinir uma implantação existente do Serviço de Nuvem do Azure (suporte estendido).

## <a name="reimage-role-instances-of-cloud-service"></a>Refazer imagem das instâncias de função do Serviço de Nuvem
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Este comando refaz a imagem de duas instâncias de função, **ContosoFrontEnd\_IN\_0** e **ContosoBackEnd\_IN\_1**, do Serviço de Nuvem denominado ContosoCS que pertence ao grupo de recursos denominado ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Refazer a imagem de todas as funções do Serviço de Nuvem
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Refazer a imagem de uma instância de função de um Serviço de Nuvem
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Reiniciar uma instância de função de um Serviço de Nuvem
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os Serviços de Nuvem do Azure (suporte estendido), confira [Visão geral dos Serviços de Nuvem do Azure (suporte estendido)](overview.md).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)