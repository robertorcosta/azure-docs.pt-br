---
ms.openlocfilehash: eb6551a9ec9accd4a7ee270ec47794fdaed91a8a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88684133"
---
Quando você configura os recursos do Azure, um vídeo curto do tráfego em uma rodovia é copiado para a VM Linux no Azure que você está usando como o dispositivo do IoT Edge. Este guia de início rápido usa o arquivo de vídeo para simular uma transmissão ao vivo.

Abra um aplicativo como o [VLC Media Player](https://www.videolan.org/vlc/). Selecione `Ctrl+N` e cole um link para [o vídeo de exemplo de cruzamento de rodovias](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) para iniciar a reprodução. Você vê uma gravação de muitos veículos que se movimentam no tráfego de uma rodovia.

Neste início rápido, você usará a Análise Dinâmica de Vídeo no IoT Edge para detectar objetos, como veículos e pessoas. Você publicará os eventos de inferência associados no Hub do IoT Edge.
