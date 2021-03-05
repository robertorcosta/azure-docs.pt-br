---
title: Visão geral do dispositivo de áudio do Azure Percept
description: Saiba mais sobre o áudio do Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8f84fb6bf37a3d3b61f4cad2c89745447aa88a36
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179384"
---
# <a name="introduction-to-azure-percept-audio"></a>Introdução ao áudio Percept do Azure

O áudio do Azure Percept é um dispositivo acessório que adiciona recursos de ia de fala ao Percept do Azure DK. Ele contém um processador de áudio pré-configurado e uma matriz linear de quatro microfone, permitindo que você aplique comandos de voz, a palavra-chave e a fala de campo distante a dispositivos de escuta locais usando os serviços cognitivas do Azure. O áudio do Azure Percept permite que os fabricantes de dispositivos estendam o Azure Percept DK além dos recursos de visão para novos dispositivos inteligentes ativados para voz. Ele é integrado de pronto para uso com o Azure Percept DK, o Azure Percept Studio e outros serviços de gerenciamento do Azure Edge. Ele está disponível para compra na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Dispositivo de áudio do Azure Percept.":::

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

- Áudio do Azure Percept: executa a formação de feixe e o cancelamento de eco e processa o áudio de entrada para otimizar para fala e enviar para o DK.  

- Azure Percept DK: a pilha de fala executa a palavra-chave que está deparando.  

- Nuvem: processa comandos e frases de linguagem natural, verificação de palavra-chave e readaptação. 

- Offline: se o dispositivo estiver offline, ele detectará a palavra-chave e capturará a telemetria do status da conexão com a Internet. Uma taxa de aceitação falsa maior para a palavra-chave pode ser observada, pois a verificação de palavra-chave na nuvem não pode ser executada. 

<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

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

Solicite um dispositivo de áudio do Azure Percept na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
