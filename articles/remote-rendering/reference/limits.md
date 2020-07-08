---
title: Limitações
description: Limitações de código para recursos do SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 17f98f452764abdc8458cdc38661d464ecb0a60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808536"
---
# <a name="limitations"></a>Limitações

Vários recursos têm limitações de tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Azure Frontend

* Total de instâncias de AzureFrontend por processo: 16.
* Total de instâncias de AzureSession por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos permitidos de um único tipo (Entity, CutPlaneComponent, etc.): 16.777.215.
* Total de planos de corte ativos permitidos: 8.

## <a name="geometry"></a>Geometry

* Total de materiais permitidos em um ativo: 65.535.
* Dimensão máxima de uma única textura: 16.384 x 16.384. Texturas de origem maiores serão reduzidas pelo processo de conversão.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número permitido de polígonos para todos os modelos carregados depende do tamanho da VM, conforme passado para [a API REST de gerenciamento de sessão](../how-tos/session-rest-api.md#create-a-session):

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| nenhum limite |

Consulte o capítulo [tamanho da VM](../reference/vm-sizes.md) para obter informações mais detalhadas sobre essa limitação.

## <a name="platform-limitations"></a>Limitações da plataforma

**Windows 10 Desktop**

* A UWP/x86 é a única plataforma UWP com suporte. Não há suporte para a UWP/x64.
* Win32/x64 é a única plataforma Win32 com suporte. Não há suporte para Win32/x86.

**Hololens 2**

* Não há suporte para [renderização de câmeras de foto/vídeo](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in).
