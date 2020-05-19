---
title: Processo de retenção de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Saiba como se inscrever para acesso antecipado a novos contêineres e APIs de serviços cognitivas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599508"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Processo de retenção para serviços cognitivas do Azure

> [!NOTE]
> Depois que uma oferta de serviço conclui uma visualização restrita, ela entra em uma visualização pública "não restrita" que não requer um aplicativo para acesso. Após o processo de visualização, ele é lançado como GA (disponibilidade geral).

À medida que novas ofertas de serviços cognitivas do Azure são introduzidas, elas passam por uma visualização restrita, onde os clientes precisam solicitar acesso por meio de um aplicativo. Esse processo de retenção ajuda a identificar oportunidades de melhorias em ofertas de serviço antes que elas fiquem amplamente disponíveis. 

Este artigo orientará você pelo processo de aplicativo para ofertas de serviços cognitivas restritos.

## <a name="eligibility-and-approval-process"></a>Processo de qualificação e aprovação

O processo de retenção está em vigor para ajudar a medir o interesse e entender melhor as necessidades do cliente. A equipe da Microsoft aceita [aplicativos](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) de clientes comerciais da Microsoft com uma assinatura válida do Azure e um cenário de negócios válido. Os clientes provavelmente terão seus aplicativos negados quando:

 - Eles não estão associados a nenhuma organização
 - Eles não têm uma assinatura válida do Azure
 - O aplicativo foi enviado por email pessoal ( @hotmail.com , @gmail.com , @yahoo.com )
 - Não havia nenhuma justificativa ou cenário de negócios adequado fornecido

Devido à demanda de diferentes segmentos de clientes, estamos tentando agilizar o processo de aprovação. No entanto, não podemos confirmar uma linha do tempo. Após a tomada de uma decisão, a equipe da Microsoft entrará em contato com você e sua equipe de gerenciamento de contas para as próximas etapas. Agradecemos sua compreensão e paciência.

Se o aplicativo for aprovado, a equipe da Microsoft enviará um email com detalhes, documentação e orientações. Detalhes de preços de serviços cognitivas disponíveis [aqui](https://azure.microsoft.com/pricing/details/cognitive-services/).


Atualmente, os serviços abaixo são oferecidos por meio do processo de retenção:

## <a name="gated-web-apis"></a>APIs Web restritas

|Serviço  |
|---------|
|Detector de anomalias v2     | 

## <a name="gated-online-containers"></a>Contêineres online restritos

| Serviço                             | Contêiner (es)                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Pesquisa Visual Computacional][cv-containers]    | Ler                                                                          |
| [Detecção Facial][fa-containers]               | Detecção Facial                                                                          |
| [Reconhecimento de Formulários][fr-containers]    | Reconhecimento de Formulários                                                               |
| [API do Serviço de Fala][sp-containers] | Conversão de fala em texto (personalizada e padrão) e conversão de texto em fala (personalizada e padrão) |
| [Tradução de Texto][tt-containers]    | Tradução de Texto                                                               |

> [!IMPORTANT]
> Se um serviço ou oferta em contêiner não estiver listado, ele não será restringido ou não estará disponível.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Inscrever-se para serviços restritos](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
