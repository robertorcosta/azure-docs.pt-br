---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422201"
---
O manuseio de áudio comprimido é implementado usando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e vinculados ao Speech SDK. Os desenvolvedores precisam instalar várias dependências e plugins, ver [Instalação no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Binários gstreamer precisam estar no caminho do sistema, para que o SDK de fala possa carregar binários gstreamer durante o tempo de execução. Se o SDK de fala é capaz de encontrar libgstreamer-1.0-0.dll durante o tempo de execução, significa que os binários gstreamer estão no caminho do sistema.

