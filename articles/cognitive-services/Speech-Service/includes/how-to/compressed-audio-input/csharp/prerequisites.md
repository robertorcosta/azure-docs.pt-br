---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637278"
---
O manuseio de áudio comprimido é implementado usando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e vinculados ao Speech SDK. Os desenvolvedores precisam instalar várias dependências e plugins, ver [Instalação no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Binários gstreamer precisam estar no caminho do sistema, para que o SDK de fala possa carregar binários gstreamer durante o tempo de execução. Se o SDK de fala é capaz de encontrar libgstreamer-1.0-0.dll durante o tempo de execução, significa que os binários gstreamer estão no caminho do sistema.

