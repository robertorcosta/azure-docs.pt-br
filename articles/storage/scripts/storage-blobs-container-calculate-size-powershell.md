---
title: Calcular o tamanho de um contêiner de blob com o PowerShell
titleSuffix: Azure Storage
description: Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho de cada um dos seus blobs.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de275bcca1644750532809b35ae85d954d3cac6d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076898"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Calcular o tamanho de um contêiner de blob com o PowerShell

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do PowerShell fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança. Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, o contêiner e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para calcular o tamanho do contêiner de Armazenamento de Blobs. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento em um grupo de recursos ou na assinatura. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Lista os blobs em um contêiner. |

## <a name="next-steps"></a>Próximas etapas

Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
