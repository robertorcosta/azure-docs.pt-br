---
title: Visão geral do Player de Mídia do Azure
description: Saiba mais sobre o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87011696"
---
# <a name="azure-media-player-overview"></a>Visão geral do Player de Mídia do Azure #

O Player de Mídia do Azure é um player de vídeo da Web criado para reproduzir conteúdo de mídia dos [Serviços de Mídia do Microsoft Azure](https://azure.microsoft.com/services/media-services/) em uma grande variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável.  Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de Mídia do Azure usa Flash e Silverlight como tecnologias de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface unificada do JavaScript para acessar APIs.  Isso permite que o conteúdo fornecido pelos Serviços de Mídia do Azure seja executado em vários dispositivos e navegadores sem esforço adicional.

Os Serviços de Mídia do Microsoft Azure permitem que o conteúdo seja fornecido com os formatos de streaming DASH, HLS e Smooth Streaming para reprodução de conteúdo. O Player de Mídia do Azure leva em conta esses vários formatos e executa automaticamente o melhor link com base nos recursos da plataforma/navegador. Os Serviços de Mídia do Microsoft Azure também permitem a criptografia dinâmica de ativos com criptografia comum (PlayReady ou Widevine) ou criptografia de envelope de 128 bits AES. O Player de Mídia do Azure permite a descriptografia do conteúdo criptografado com PlayReady e AES de 128 bits, quando configurado adequadamente.  Confira a seção [Conteúdo protegido](azure-media-player-protected-content.md) para saber como configurar isso.

Para solicitar novos recursos ou fornecer ideias ou comentários, envie sua mensagem para o [UserVoice do Player de Mídia do Azure](https://aka.ms/ampuservoice). Se tiver problemas ou perguntas específicas ou encontrar bugs, entre em contato conosco em ampinfo@microsoft.com.

[Inscreva-se](https://aka.ms/ampsignup) para nunca perder um lançamento e manter-se atualizado com a versão mais recente que o Player de Mídia do Azure tem a oferecer.

> [!NOTE]
> Observe que Player de Mídia do Azure só dá suporte a fluxos de mídia dos Serviços de Mídia do Azure.

## <a name="license"></a>Licença ##

O Player de Mídia do Azure é licenciado e está sujeito aos termos descritos nos Termos de licença para software Microsoft para o Player de Mídia do Azure. Confira o [arquivo de licença](/legal/azure-media-player/azure-media-player-license) para ver os termos completos. Confira a [Política de Privacidade](https://www.microsoft.com/en-us/privacystatement/default.aspx) para obter mais informações.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)
