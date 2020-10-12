---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282714"
---
O tratamento de áudio compactado é implementado usando o [`GStreamer`](https://gstreamer.freedesktop.org) . Para motivos de licenciamento `GStreamer` , os binários não são compilados e vinculados com o SDK de fala. Os desenvolvedores precisam instalar várias dependências e plug-ins, consulte [instalando no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` os binários precisam estar no caminho do sistema, para que o SDK de fala possa carregar os binários durante o tempo de execução. Se o SDK de fala for capaz de encontrar `libgstreamer-1.0-0.dll` durante o tempo de execução, significa que os binários estão no caminho do sistema.

