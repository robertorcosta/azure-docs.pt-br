---
title: Visão geral do dispositivo de áudio do Azure Percept
description: Saiba mais sobre o áudio do Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 9ff0cb8e2417ed08ed4c2061674cc6932b511aed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595899"
---
# <a name="introduction-to-azure-percept-audio"></a>Introdução ao áudio Percept do Azure

O áudio do Azure Percept é um dispositivo acessório que adiciona recursos de ia de fala ao Percept do Azure DK. Ele contém um processador de áudio pré-configurado e uma matriz linear de quatro microfone, permitindo que você aplique comandos de voz, a palavra-chave e a fala de campo distante a dispositivos de escuta locais usando os serviços cognitivas do Azure. O áudio do Azure Percept permite que os fabricantes de dispositivos estendam o Azure Percept DK além dos recursos de visão para novos dispositivos inteligentes ativados para voz. Ele é integrado de pronto para uso com o Azure Percept DK, o Azure Percept Studio e outros serviços de gerenciamento do Azure Edge. Ele está disponível para compra na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Comprar agora](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

<!---
:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Azure Percept Audio device.":::
--->
</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Componentes de áudio do Azure Percept

O áudio do Azure Percept contém os seguintes componentes principais:

- Dispositivo de áudio do Azure Percept (SoM) pronto para produção com matriz linear de quatro microfone e processamento de áudio feito por um codec XMOS
- Placa de desenvolvedor (intermediária) (inclui botões 2x, 3-LEDs, micro USB e 3,5 mm de áudio)
- Cabos necessários: cabo FPC, micro tipo USB-B para USB-A
- Cartão de boas-vindas
- Placa de montagem mecânica com montagem integrada da série 80/20 1010

## <a name="compute-capabilities"></a>Recursos de computação 

O áudio do Azure Percept passa a entrada de áudio por meio da pilha de fala que é executada na CPU da placa da operadora do Percept do Azure DK em uma forma de nuvem híbrida. Portanto, o áudio do Azure Percept requer uma placa de operadora com um sistema operacional que dê suporte à pilha de fala para executar. 

O processamento é feito da seguinte maneira: 

- Áudio do Azure Percept: captura e converte o áudio e o envia para a tomada de áudio e DK.

- Azure Percept DK: a pilha de fala executa a forma de feixe e o cancelamento de eco e processa o áudio de entrada para otimizar para fala. Em seguida, ele executa a palavra-chave que está deparando.

- Nuvem: processa comandos e frases de linguagem natural, verificação de palavra-chave e readaptação. 

- Offline: se o dispositivo estiver offline, ele detectará a palavra-chave e capturará a telemetria do status da conexão com a Internet. Uma taxa de aceitação falsa maior para a palavra-chave pode ser observada, pois a verificação de palavra-chave na nuvem não pode ser executada. 

## <a name="getting-started"></a>Introdução

- [Monte seu Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Concluir a experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Conecte seu dispositivo de áudio do Azure Percept ao seu devkit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Criar um protótipo sem código

Crie uma [solução de fala sem código](./tutorial-no-code-speech.md) no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) usando modelos do assistente de voz do Percept do Azure para hospedagem, saúde, inventário e cenários automotivados.

### <a name="manage-your-no-code-speech-solution"></a>Gerenciar sua solução de fala sem código

- [Configurar seu assistente de voz no Hub IOT](./how-to-manage-voice-assistant.md)
- [Configurar o assistente de voz no Azure Percept Studio](./how-to-configure-voice-assistant.md)
- [Solução de problemas de áudio do Azure Percept](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Informações técnicas adicionais

- [Folha de dados do áudio do Azure Percept](./azure-percept-audio-datasheet.md)
- [Comportamento de botão e LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Comprar um dispositivo de áudio do Azure Percept da Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
