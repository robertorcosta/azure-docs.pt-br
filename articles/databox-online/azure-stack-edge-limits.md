---
title: Limites de borda Azure Stack | Microsoft Docs
description: Saiba mais sobre os limites e os tamanhos recomendados ao implantar e operar Azure Stack borda, incluindo limites de serviço, limites de dispositivo e limites de armazenamento.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922703"
---
# <a name="azure-stack-edge-limits"></a>Limites do Azure Stack Edge

Considere esses limites ao implantar e operar sua solução de borda de Microsoft Azure Stack. 

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço do Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack limites de dispositivo

A tabela a seguir descreve os limites para o dispositivo Azure Stack Edge. 

| Descrição | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões |
|Não. de compartilhamentos por dispositivo |24 |
|Não. de compartilhamentos por contêiner |1 |
|Tamanho máximo do arquivo gravado em um compartilhamento| 5 TB |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Azure Stack borda](azure-stack-edge-deploy-prep.md)
