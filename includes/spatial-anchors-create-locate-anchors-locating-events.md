---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "71180032"
---
Depois que o observador for criado, o evento `AnchorLocated` será acionado para cada âncora solicitada. Esse evento é acionado quando uma âncora é localizada ou se a âncora não puder ser localizada. Se essa situação ocorrer, o motivo será indicado no status. Após todas as âncoras de um observador serem processadas, sejam elas encontradas ou não, o evento `LocateAnchorsCompleted` será acionado. Há um limite de 35 identificadores por observador. 
