---
title: 'Script do PowerShell: criar um novo compartilhamento de dados do Azure | Microsoft Docs'
description: Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "70307256"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Usar o PowerShell para criar um compartilhamento de dados no Azure

Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.

## <a name="sample-script"></a>Exemplo de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Cria um compartilhamento de dados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).
