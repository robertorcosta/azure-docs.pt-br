---
title: Contêineres de Serviços Cognitivos frequentemente perguntas (FAQ)
titleSuffix: Azure Cognitive Services
description: Perguntas e respostas freqüentes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961886"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Contêineres do Azure Cognitive Services frequentemente fizeram perguntas (FAQ)

## <a name="general-questions"></a>Perguntas gerais

**P: O que está disponível?**

**R:** [O suporte a contêineres no Azure Cognitive Services](../cognitive-services-container-support.md) permite que os desenvolvedores usem as mesmas APIs inteligentes disponíveis no Azure, mas com os [benefícios](../cognitive-services-container-support.md#features-and-benefits) da containerização. O suporte a contêineres está atualmente disponível na pré-visualização de um subconjunto de Serviços Cognitivos Azure, incluindo partes de:

> [!div class="checklist"]
> * [Detector de Anomalias][ad-containers]
> * [Pesquisa Visual Computacional][cv-containers]
> * [Face][fa-containers]
> * [Reconhecimento de Formulários][fr-containers]
> * [Reconhecimento Vocal (LUIS)][lu-containers]
> * [API do Serviço de Fala][sp-containers]
> * [Análise de texto][ta-containers]

**P: Existe alguma diferença entre a nuvem de Serviços Cognitivos e os contêineres?**

**A:** Os contêineres de Serviços Cognitivos são uma alternativa à nuvem de Serviços Cognitivos. Os contêineres oferecem os mesmos recursos que os serviços de nuvem correspondentes. Os clientes podem implantar os contêineres no local ou no Azure. A tecnologia principal de IA, os níveis de preços, as chaves de API e a assinatura da API são os mesmos entre o contêiner e os serviços de nuvem correspondentes. Aqui estão os [recursos e benefícios](../cognitive-services-container-support.md#features-and-benefits) para escolher contêineres em vez de seu equivalente de serviço em nuvem.

**P: Os contêineres estarão disponíveis para todos os Serviços Cognitivos e quais são o próximo conjunto de contêineres que devemos esperar?**

**A:** Gostaríamos de disponibilizar mais Serviços Cognitivos como ofertas de contêineres. Entre em contato com o gerente de contas da Microsoft local para obter atualizações sobre novas versões de contêineres e outros anúncios de Serviços Cognitivos.

**P: Qual será o Contrato de Nível de Serviço (SLA) para contêineres de Serviços Cognitivos?**

**A:** Os contêineres de Serviços Cognitivos não possuem um SLA.

As configurações de recursos dos contêineres da Cognitive Services são controladas pelos clientes, portanto, a Microsoft não oferecerá um SLA para disponibilidade geral (GA). Os clientes são livres para implantar contêineres no local, assim eles definem os ambientes de hospedagem.

> [!IMPORTANT]
> Para saber mais sobre contratos de nível de serviço de serviços cognitivos, [visite nossa página de SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**P: Esses contêineres estão disponíveis em nuvens soberanas?**

**A:** Nem todos estão familiarizados com o termo "nuvem soberana", então vamos começar com a definição:

> A "nuvem soberana" consiste nas nuvens [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Germany](../../germany/germany-welcome.md)e [Azure China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

Infelizmente, os contêineres dos Serviços Cognitivos *não* são suportados nativamente nas nuvens soberanas. Os contêineres podem ser executados nessas nuvens, mas serão retirados da nuvem pública e precisarão enviar dados de uso para o ponto final público.

### <a name="versioning"></a>Controle de versão

**P: Como os contêineres são atualizados para a versão mais recente?**

**A:** Os clientes podem escolher quando atualizar os contêineres que implantaram. Os contêineres serão marcados com `latest` tags [Docker](https://docs.docker.com/engine/reference/commandline/tag/) padrão, como para indicar a versão mais recente. Nós encorajamos os clientes a puxar a versão mais recente dos contêineres à medida que eles são lançados, checkout [Azure Container Registry webhooks](../../container-registry/container-registry-webhook.md) para obter detalhes sobre como ser notificado quando uma imagem é atualizada.
 
**P: Quais versões serão suportadas?**

**A:** A versão atual e última grande do contêiner será suportada. No entanto, encorajamos os clientes a se manterem atualizados para obter a mais recente tecnologia.
 
**P: Como as atualizações são versidas?**

**A:** As principais alterações na versão indicam que há uma mudança de ruptura na assinatura da API. Prevemos que isso geralmente coincidirá com as principais alterações de versão na oferta correspondente de nuvem do Serviço Cognitivo. Alterações menores na versão indicam correções de bugs, atualizações de modelos ou novos recursos que não fazem uma alteração de ruptura na assinatura da API.

## <a name="technical-questions"></a>Perguntas técnicas

**P: Como devo executar os contêineres de Serviços Cognitivos em dispositivos IoT?**

Se você não tem uma conexão de internet confiável ou quer economizar no custo de largura de banda. Ou se tiver requisitos de baixa latência ou estiver lidando com dados confidenciais que precisam ser analisados no local, [o Azure IoT Edge com os contêineres do Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) lhe dá consistência com a nuvem.

**P: Como forneço feedback de produtos e recomendações de recursos?**

**A:** Os clientes são encorajados a [expressar suas preocupações](https://cognitive.uservoice.com/) publicamente, e outros que votaram em outros que fizeram o mesmo onde possíveis questões se sobrepõem. A ferramenta de voz do usuário pode ser usada tanto para feedback do produto quanto para recomendações de recursos.

**P: Quem eu contato para obter suporte?**

**A:** Os canais de suporte ao cliente são os mesmos da oferta em nuvem dos Serviços Cognitivos. Todos os contêineres do Cognitive Services incluem recursos de registro que nos ajudarão e os clientes de suporte da comunidade. Para obter suporte adicional, consulte as seguintes opções.

### <a name="customer-support-plan"></a>Plano de suporte ao cliente

Os clientes devem consultar seu [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) para ver com quem entrar em contato para obter suporte.

### <a name="azure-knowledge-center"></a>Centro de conhecimento Azure

O cliente pode explorar o [centro de conhecimento do Azure](https://azure.microsoft.com/resources/knowledge-center/) para responder a perguntas e questões de suporte.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) é um site de perguntas e respostas para programadores profissionais e entusiastas.

Explore as seguintes tags para obter possíveis perguntas e respostas que estejam alinhadas com suas necessidades.

* [Serviços Cognitivos do Azure](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitivo](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: Como funciona o faturamento?**

**A:** Os clientes são cobrados com base no consumo, semelhante à nuvem de Serviços Cognitivos. Os contêineres precisam ser configurados para enviar dados de medição para o Azure, e as transações serão cobradas em conformidade. Os recursos utilizados nos serviços hospedados e locais adicionarão à cota única com preços hierárquicos, contando com ambos os usos. Para obter mais detalhes, consulte a página de preços da oferta correspondente.

* [Detector de Anomalias][ad-containers-billing]
* [Pesquisa Visual Computacional][cv-containers-billing]
* [Face][fa-containers-billing]
* [Reconhecimento de Formulários][fr-containers-billing]
* [Reconhecimento Vocal (LUIS)][lu-containers-billing]
* [API do Serviço de Fala][sp-containers-billing]
* [Análise de texto][ta-containers-billing]

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente para a Microsoft.
 
**P: Qual é a garantia de suporte atual para contêineres?**

**A:** Não há garantia para visualizações. A garantia padrão da Microsoft para software corporativo será aplicada quando os contêineres forem formalmente anunciados como disponibilidade geral (GA).
 
**P: O que acontece com os contêineres dos Serviços Cognitivos quando a conectividade com a internet é perdida?**

**A:** Os contêineres de Serviços Cognitivos não têm *licença* para funcionar sem serem conectados ao Azure para medição. Os clientes precisam habilitar os contêineres para se comunicarem com o serviço de medição o tempo todo.

**P: Por quanto tempo o contêiner pode operar sem estar conectado ao Azure?**

**A:** Os contêineres de Serviços Cognitivos não têm *licença* para funcionar sem serem conectados ao Azure para medição. Os clientes precisam habilitar os contêineres para se comunicarem com o serviço de medição o tempo todo.
 
**P: O que é necessário para executar esses contêineres?**

**A:** Os contêineres cognitive services são contêineres baseados em x64 que podem executar qualquer nó Linux compatível, VM e dispositivo edge que suporte os contêineres X64 Linux Docker. Todos eles requerem processadores de CPU. As configurações mínimas e recomendadas para cada oferta de contêineres estão disponíveis abaixo:

* [Detector de Anomalias][ad-containers-recommendations]
* [Pesquisa Visual Computacional][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [Reconhecimento de Formulários][fr-containers-recommendations]
* [Reconhecimento Vocal (LUIS)][lu-containers-recommendations]
* [API do Serviço de Fala][sp-containers-recommendations]
* [Análise de texto][ta-containers-recommendations]
 
**P: Esses contêineres são suportados atualmente no Windows?**

**A:** Os contêineres do Cognitive Services são contêineres Linux, porém há algum suporte para contêineres Linux no Windows. Para obter mais informações sobre contêineres Linux no Windows, consulte [a documentação do Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**P: Como descubro os contêineres?**

**A:** Os contêineres de Serviços Cognitivos estão disponíveis em vários locais, como o portal Azure, o hub Docker e os registros de contêineres do Azure. Para os locais de contêineres mais recentes, consulte [repositórios e imagens de contêineres](../cognitive-services-container-support.md#container-repositories-and-images).

**P: Como os contêineres de Serviços Cognitivos se comparam às ofertas da AWS e do Google?**

**A:** A Microsoft é a primeira provedora de nuvem a mover seus modelos de IA pré-treinados em contêineres com faturamento simples por transação como se os clientes usassem um serviço em nuvem. A Microsoft acredita que uma nuvem híbrida dá aos clientes mais opções.

**P: Que certificações de conformidade os contêineres têm?**

**A:** Os contêineres de serviços cognitivos não possuem nenhuma certificação de conformidade

**P: Em que regiões estão disponíveis os contêineres de Serviços Cognitivos?**

**A:** Os contêineres podem ser executados em qualquer lugar de qualquer região, no entanto, eles precisam de uma chave e para chamar de volta para Azure para medição. Todas as regiões suportadas para o Serviço de Nuvem são suportadas para a chamada de medição de contêineres.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
