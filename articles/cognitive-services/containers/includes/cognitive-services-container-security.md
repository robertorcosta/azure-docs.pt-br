---
title: Segurança do contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como proteger seu contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272901"
---
## <a name="azure-cognitive-services-container-security"></a>Segurança de contêineres do Azure Cognitive Services

A segurança deve ser o foco principal sempre que você estiver desenvolvendo aplicações. A importância da segurança é uma métrica para o sucesso. Quando você está arquitetando uma solução de software que inclui contêineres de Serviços Cognitivos, é vital entender as limitações e recursos disponíveis para você. Para obter mais informações sobre segurança de rede, consulte [Configure Redes Virtuais de Serviços Cognitivos Azure][az-security].

> [!IMPORTANT]
> Por *padrão, não* há segurança na API do contêiner Cognitive Services. A razão para isso é que, na maioria das vezes, o contêiner será executado como parte de uma cápsula que é protegida do lado de fora por uma ponte de rede. No entanto, é possível habilitar a autenticação que funciona de forma idêntica à autenticação usada ao acessar os [Serviços Cognitivos baseados em nuvem.][request-authentication]

O diagrama abaixo ilustra a abordagem padrão e **não segura:**

![Segurança do contêiner](../media/container-security.svg)

Como uma abordagem alternativa e *segura,* os consumidores de contêineres de Serviços Cognitivos poderiam aumentar um contêiner com um componente frontal, mantendo o ponto final do contêiner privado. Vamos considerar um cenário onde usamos [Istio][istio] como um portal de entrada. A Istio suporta HTTPS/TLS e autenticação de certificado de cliente. Nesse cenário, o frontend istio expõe o acesso ao contêiner, apresentando o certificado do cliente que é listado antecipadamente com a Istio.

[Nginx][nginx] é outra escolha popular na mesma categoria. Tanto istio quanto nginx atuam como uma malha de serviço e oferecem recursos adicionais, incluindo coisas como balanceamento de carga, roteamento e controle de taxa.

### <a name="container-networking"></a>Rede de contêineres

Os contêineres dos Serviços Cognitivos são obrigados a enviar informações de medição para fins de faturamento. A única exceção, são *os contêineres offline,* pois seguem uma metodologia de faturamento diferente. A falha em permitir a lista de vários canais de rede em que os contêineres do Cognitive Services dependem impedirá que o contêiner funcionasse.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Permitir listar domínios e portas de serviços cognitivos

O host deve permitir a porta de lista **443** e os seguintes domínios:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Desativar a inspeção profunda de pacotes

> [A inspeção profunda de pacotes](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) é um tipo de processamento de dados que inspeciona detalhadamente os dados que estão sendo enviados por uma rede de computadores, e geralmente toma medidas bloqueando, redirecionando ou registrando-os de acordo.

Desativar o DPI nos canais seguros que os contêineres cognitive services criam para servidores Microsoft. Caso contrário, o recipiente funcionará corretamente.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
