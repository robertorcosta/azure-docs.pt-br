---
title: Problemas conhecidos do plug-in do Project Acoustics
titlesuffix: Azure Cognitive Services
description: Você pode experimentar os seguintes problemas conhecidos no Project Acoustics.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243036"
---
# <a name="project-acoustics-known-issues"></a>Projeto Acústico questões conhecidas
Este artigo descreve problemas que você pode experimentar quando usa o Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena não são automaticamente transferidos para a nova cena. Mas eles ainda existem no arquivo de ativos antigos. Procure o arquivo *[SceneName]_AcousticParameters.asset* no diretório *Editor* ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Implantar para Android bug de algumas versões do Unity

Algumas versões do Unity têm um [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) na forma como implantam plug-ins de áudio para Android. Certifique-se de que você não está usando uma versão afetada por este bug.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Erro "Não foi possível encontrar o erro de arquivo de metadados System.Security.dll"

Certifique-se de que a **versão de execução de scripts** nas configurações do **Jogador** seja *.NET 4.x Equivalente*e reinicie unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemas de autenticação ao se conectar ao Azure

Verifique se:
- Você usou as credenciais corretas para sua conta no Azure.
- Sua conta suporta o tipo de nó que você pediu no cozimento.
- O relógio do sistema está configurado corretamente.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>A guia Bake ainda mostra "excluindo" depois que você cancela
O Project Acoustics limpa todos os recursos do Azure para um trabalho após a conclusão ou cancelamento bem-sucedidos. Esse processo pode levar até 5 minutos.

## <a name="next-steps"></a>Próximas etapas
* Experimente o conteúdo de amostra [Unity](unity-quickstart.md) ou [Unreal.](unreal-quickstart.md)
