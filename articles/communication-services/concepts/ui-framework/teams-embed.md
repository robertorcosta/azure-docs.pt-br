---
title: Inserção do Teams na Estrutura da Interface do Usuário
titleSuffix: An Azure Communication Services quickstart
description: Neste documento, você saberá como a funcionalidade de Inserção do Teams na Estrutura da Interface do Usuário dos Serviços de Comunicação do Azure pode ser usada para criar experiências de chamadas de turnkey.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 99263695aa0842daf59f23cda115dcb5b27b7add
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803885"
---
# <a name="teams-embed"></a>Inserção do Teams

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


A Inserção do Teams é uma funcionalidade dos Serviços de Comunicação do Azure voltada para executar interações de chamadas comuns entre empresas e o consumidor e entre empresas. O núcleo do sistema de Inserção do Teams é a [chamada de vídeo e voz](../voice-video-calling/calling-sdk-features.md). No entanto, o sistema de Inserção do Teams é baseado em primitivos de chamadas do Azure criados para fornecer uma experiência de usuário completa com base nas reuniões do Microsoft Teams.

As bibliotecas de clientes da Inserção do Teams são um software proprietário e disponibilizam essas funcionalidades em um formato turnkey e de composição. Soltar a Inserção do Teams no painel da tela do aplicativo e na biblioteca de clientes criará uma experiência de usuário completa. Como essa experiência de usuário é muito semelhante às reuniões do Microsoft Teams, será possível obter as seguintes vantagens:

- Redução do tempo de desenvolvimento e da complexidade da engenharia
- Familiaridade do usuário final com o Teams
- Capacidade de usar novamente o [Conteúdo do treinamento do usuário final do Teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

A Inserção do Teams fornece a maioria dos recursos compatíveis com as reuniões do Teams, incluindo:

- Uma experiência antes da reunião em que um usuário configura dispositivos próprios de áudio e vídeo
- Uma experiência de configuração de dispositivos de áudio e vídeo durante a reunião
- [Telas de fundo do vídeo](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): permite que os participantes desfoquem ou substituam as telas de fundo
- [Várias opções da galeria de vídeos](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): galeria grande, modo conferência, foco, marcação e destaque
- [Compartilhamento de conteúdo](https://support.microsoft.comoffice/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8#ID0EABAAA=Mobile): permite que participantes compartilhem a tela

Para obter mais informações sobre essa interface do usuário em comparação com outros SDKs de Comunicação do Azure, confira a [Introdução ao conceito de SDK de interface do usuário](ui-sdk-overview.md). 
