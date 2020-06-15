---
title: Limitações
description: Limitações de código para recursos do SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759040"
---
# <a name="limitations"></a>Limitações

Vários recursos têm limitações de tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias de AzureFrontend por processo: 16.
* Total de instâncias de AzureSession por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos permitidos de um único tipo (Entity, CutPlaneComponent, etc.): 16.777.215.
* Total de planos de corte ativos permitidos: 8.

## <a name="materials"></a>Materiais

* Total de materiais permitidos em um ativo: 65.535.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número permitido de polígonos para todos os modelos carregados depende do tamanho da VM, conforme passado para [a API REST de gerenciamento de sessão](../how-tos/session-rest-api.md#create-a-session):

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| nenhum limite |


## <a name="platform-limitations"></a>Limitações da plataforma

**Windows 10 Desktop**

* A UWP/x86 é a única plataforma UWP com suporte. Não há suporte para a UWP/x64.

**Hololens 2**

* Não há suporte para [renderização de câmeras de foto/vídeo](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in).
