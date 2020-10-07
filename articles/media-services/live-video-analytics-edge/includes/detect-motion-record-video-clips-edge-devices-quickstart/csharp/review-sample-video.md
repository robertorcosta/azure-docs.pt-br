---
ms.openlocfilehash: e0c313ea5e5062f725d0a65fefeac444214cf863
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682052"
---
Quando você configura os recursos do Azure para este início rápido, um vídeo curto de um estacionamento é copiado para a VM Linux no Azure usada como o dispositivo do IoT Edge. Esse arquivo de vídeo será usado para simular uma transmissão ao vivo para este tutorial.

Abra um aplicativo como o [VLC media player](https://www.videolan.org/vlc/), selecione Ctrl+N e cole [este link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) para o vídeo de estacionamento começar a ser reproduzido. Na marca de aproximadamente 5 segundos, um carro branco percorre o estacionamento.

Conclua as etapas abaixo para usar a Análise Dinâmica de Vídeo no IoT Edge para detectar o movimento do carro e gravar um clipe de vídeo começando na marca de cerca de 5 segundos.
