---
title: Integração e implantação do Projeto Acústico Unidade
titlesuffix: Azure Cognitive Services
description: Este artigo descreve como integrar o plug-in Project Acoustics Unity em seu projeto Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243009"
---
# <a name="project-acoustics-unity-integration"></a>Integração do Projeto Unidade Acústica
Este artigo descreve como integrar o plug-in Project Acoustics Unity em seu projeto Unity.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows
* [Pacote Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importe o plug-in
1. Importe a acústica UnityPackage para o seu projeto. 
 Em Unity, vá para O Pacote personalizado**do pacote** > de importação**de** **ativos** > .

    ![O menu Pacote de Importação da Unidade](media/import-package.png)  

1. Escolha **ProjectAcoustics.unitypackage**.

1. Selecione o botão **Importar** para integrar o pacote do Unity ao projeto.

    ![A caixa de diálogo Importar Pacote do Unity](media/import-dialog.png)  

Se você estiver importando o plug-in para um projeto existente, seu projeto pode já ter um arquivo *mcs.rsp* na raiz do projeto. Este arquivo especifica opções para o compilador C#. Mesclar o conteúdo desse arquivo com o arquivo mcs.rsp que vem com o plug-in Project Acoustics.

## <a name="enable-the-plug-in"></a>Habilite o plug-in
A porção de cozimento do kit de ferramentas de acústica requer o .NET 4. *x* roteirizar versão em tempo de execução. A importação do pacote atualiza as configurações do jogador unity. Reinicialização do Unity para essa configuração tenha efeito.

![O painel Unity Player Settings](media/player-settings.png)

![O painel Unity Player Settings com .NET 4.5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar dsp de áudio
Project Acoustics inclui DSP em tempo de execução de áudio que se integra à estrutura espacializadora do motor de áudio Unity. Inclui espacialização baseada em HRTF e panorâmica. Para habilitar o Project Acoustics DSP, vá para **Editar** > **áudio** **de configurações** > do projeto para abrir as configurações de áudio unity. Selecione **Project Acoustics** como o **Plugin Espacializador** para o seu projeto. Certifique-se de que **o tamanho do buffer DSP** está definido como o melhor *desempenho*.

![O menu Configurações do projeto do Unity](media/project-settings.png)  

![O painel de configurações do Unity Spatializer com o espacializador Project Acoustics selecionado](media/choose-spatializer.png)

Em seguida, abra o Mixer de áudio **(Window** > **Audio Mixer).** Certifique-se de que você tem pelo menos um misturador, com um grupo. Se você não tiver um, **+** selecione o botão à direita dos **Mixers**. Clique com o botão direito do mouse na parte inferior da faixa de canais na seção de efeitos e adicione o efeito **Microsoft Acoustics Mixer**. Apenas um mixer de Acústica do Projeto é suportado por vez.

![O Mixer de Áudio Unity recebe o Projeto Acústico mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Habilite a acústica em fontes de som
Criar uma fonte de áudio: Selecione a caixa de seleção **Spatialize** na parte inferior do painel de inspetor AudioSource. Certifique-se de que **a mistura espacial** está definida como *3D*completa .  

![O painel Unity Audio Source](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Habilite o design acústico
Anexar o *script AcousticsAdjust* a uma fonte de som em sua cena para ativar parâmetros adicionais de design de origem: Selecione **Adicionar componente** e escolha **Scripts** > **Acoustics Adjust**.

![O script AcousticsAdjust do Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Próximas etapas
* [Asse sua cena com o Projeto Acústica para Unidade](unity-baking.md).
* [Crie uma conta do Azure Batch](create-azure-account.md) para assar sua cena na nuvem.
* Explore o [processo de design do Project Acoustics Unity](unity-workflow.md).
