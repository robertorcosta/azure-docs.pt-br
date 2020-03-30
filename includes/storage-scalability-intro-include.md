---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477142"
---
Esta referência detalha as metas de escalabilidade e desempenho para o Armazenamento Azure. As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo.

Certifique-se de testar seu serviço para determinar se seu desempenho atende aos seus requisitos. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

Quando seu aplicativo atinge o limite de processamento de uma partição para sua carga de trabalho, o Armazenamento do Azure começa a retornar respostas com o código de erro 503 (Servidor Ocupado) ou 500 (Tempo Limite da Operação). Se 503 erros estiverem ocorrendo, considere modificar seu aplicativo para usar uma política de backoff exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.
