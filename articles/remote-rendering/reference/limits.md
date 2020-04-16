---
title: Limitações
description: Limitações de código para recursos SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417684"
---
# <a name="limitations"></a>Limitações

Uma série de recursos têm tamanho, contagem ou outras limitações.

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


## <a name="platform-limitations"></a>Limitações da plataforma

**Desktop windows 10**

* A implantação "PC Standalone" do Unity não é suportada. Use UWP em vez disso.
* UWP/x86 é a única plataforma UWP suportada. UWP/x64 não é suportado.

**Hololens 2**

* A [renderização do](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) recurso de câmera PV não é suportada.
