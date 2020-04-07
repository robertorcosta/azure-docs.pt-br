---
title: limites
description: Limitações de código para recursos SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680330"
---
# <a name="limits"></a>limites

Uma série de recursos têm limitações de tamanho ou contagem devido a detalhes internos do sistema em execução.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias do AzureFrontend por processo: 16.
* Total de instâncias do AzureSession por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos permitidos de um único tipo (Entity, CutPlaneComponent, etc.): 16.777.215.
* Total de planos de corte ativo permitidos: 8.

## <a name="materials"></a>Materiais

* Total de materiais permitidos em um ativo: 65.535.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número permitido de polígonos para todos os modelos carregados depende do tamanho da VM como passado para [a API de gerenciamento de sessão REST](../how-tos/session-rest-api.md#create-a-session):

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| nenhum limite |



