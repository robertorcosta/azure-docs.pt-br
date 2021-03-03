---
title: Visão geral do dispositivo de áudio do Azure Percept
description: Saiba mais sobre o áudio do Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661936"
---
# <a name="introduction-to-azure-percept-audio"></a>Introdução ao áudio Percept do Azure

O áudio do Azure Percept é um dispositivo acessório que adiciona recursos de ia de fala ao Percept do Azure DK. Ele contém um acelerador de ia de fala pré-configurado e uma matriz linear de quatro microfones, permitindo que você aplique comandos personalizados, a palavra-chave e a fala de campo distante aos dispositivos de escuta locais. O áudio do Azure Percept permite que os fabricantes de dispositivos estendam os recursos de visão do Azure Percept DK para dispositivos habilitados para voz inteligente novos. Ele é integrado de pronto para uso com o Azure Percept DK, o Azure Percept Studio e outros serviços de gerenciamento do Azure Edge. Ele está disponível para compra na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Dispositivo de áudio do Azure Percept.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Componentes de áudio do Azure Percept

O áudio do Azure Percept contém os seguintes componentes principais:

- Dispositivo de áudio do Azure Percept (SoM) pronto para produção com matriz linear de quatro microfone
- Placa do desenvolvedor (inclui botões 2x, três LEDs, USB micro e 3,5 mm de áudio)
- Cabos necessários: cabo flexível, micro tipo USB-B para USB-A
- Cartão de boas-vindas
- Placa de montagem mecânica com montagem integrada da série 80/20 1010


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

### <a name="build-a-no-code-prototype"></a>Criar um protótipo sem código

Crie uma [solução de fala sem código](./tutorial-no-code-speech.md) usando modelos do assistente de voz do Percept do Azure para hospedagem, saúde, inventário e cenários automotivados.

## <a name="additional-technical-information"></a>Informações técnicas adicionais

- [Folha de dados do áudio do Azure Percept](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Próximas etapas

Solicite um dispositivo de áudio do Azure Percept na [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270).