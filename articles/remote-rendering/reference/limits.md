---
title: Limitações
description: Limitações de código para recursos do SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417684"
---
# <a name="limitations"></a>Limitações

Vários recursos têm tamanho, contagem ou outras limitações.

## <a name="azure-frontend"></a>Front-end do Azure

* Total de instâncias AzureFrontend por processo: 16.
* Total de instâncias de AzureSession por AzureFrontend: 16.

## <a name="objects"></a>Objetos

* Total de objetos permitidos de um único tipo (entidade, CutPlaneComponent, etc.): 16.777.215.
* Total de planos de recorte ativos permitidos: 8.

## <a name="materials"></a>Materiais

* Total de materiais permitidos em um ativo: 65.535.

## <a name="overall-number-of-polygons"></a>Número total de polígonos

O número permitido de polígonos para todos os modelos carregados depende do tamanho da VM, conforme passado para [a API REST de gerenciamento de sessão](../how-tos/session-rest-api.md#create-a-session):

| Tamanho da VM | Número máximo de polígonos |
|:--------|:------------------|
|padrão| 20 milhões |
|premium| nenhum limite |


## <a name="platform-limitations"></a>Limitações da plataforma

**Windows 10 desktop**

* Não há suporte para a implantação de "PC autônomo" do Unity. Em vez disso, use UWP.
* O UWP/x86 é a única plataforma UWP com suporte. Não há suporte para UWP/x64.

**Hololens 2**

* Não há suporte para o recurso [render da câmera PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) .
