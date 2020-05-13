---
title: Requisitos de rede
description: Requisitos de rede e práticas recomendadas de rede para uma experiência ideal
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fe684d15e2ce9b8d302db4eb6bd31dd1416abf21
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196542"
---
# <a name="network-requirements"></a>Requisitos de rede

Uma conexão de rede estável e de baixa latência com uma data center do Azure é essencial para uma boa experiência do usuário na renderização remota do Azure. Condições de rede insuficientes podem resultar em conexões descartadas, em instabilidade, tremulação ou hologramas de ' salto ' e em um retardo perceptível ao atualizar o grafo de cena do lado do servidor.

## <a name="guidelines-for-network-connectivity"></a>Diretrizes para conectividade de rede

Os requisitos de rede exatos dependem do seu caso de uso específico, como o número e a frequência de modificações no grafo de cena remota, bem como a complexidade da exibição renderizada, mas há várias diretrizes para garantir que sua experiência seja a mais boa possível:

* Sua conectividade com a Internet precisa dar suporte a pelo menos **40 Mbps downstream** e **5 Mbps upstream** de forma consistente para uma única sessão de usuário da renderização remota do Azure, supondo que não haja tráfego de concorrência na rede. Recomendamos taxas mais altas para melhores experiências. Com mais usuários na mesma rede, esses requisitos são dimensionados de forma correspondente.
* Usar a **banda Wi-Fi de 5 GHz** geralmente produzirá resultados melhores do que a banda Wi-fi de 2,4 GHz, embora ambas devam funcionar.
* Se houver outras redes Wi-Fi próximas, evite usar canais Wi-Fi em uso por essas outras redes. Você pode usar ferramentas de verificação de rede como [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para verificar se os canais que sua rede Wi-Fi usa, estão livres de tráfego de concorrência.
* Evite, estritamente, o **uso de repetidores Wi-Fi** ou encaminhamento de LAN over-Powerline.
* **Evite o tráfego intensivo de largura de banda concorrente** , como vídeo ou streaming de jogos, na mesma rede Wi-Fi.
* Ter **boa intensidade de sinal de Wi-Fi** é essencial. Se possível, permaneça perto do seu ponto de acesso Wi-Fi e Evite obstáculos entre o dispositivo cliente e os pontos de acesso.
* Certifique-se de sempre se conectar ao **Data Center do Azure mais próximo** para sua [região](regions.md). Quanto mais próximo a data center, menor a latência de rede, que tem um grande efeito na estabilidade do holograma.

> [!NOTE]
> A largura de banda downstream é consumida principalmente pelo fluxo de vídeo, que, por sua vez, é dividido entre as informações de cor e de profundidade (60 Hz, estéreo).

## <a name="network-performance-tests"></a>Testes de desempenho de rede

Se você quiser ter um entendimento inicial de se a qualidade de sua conectividade de rede é suficiente para executar a renderização remota do Azure, há ferramentas online existentes que você pode usar. É altamente recomendável executar essas ferramentas online de um laptop razoavelmente poderoso conectado ao mesmo Wi-Fi que o dispositivo no qual você está planejando executar seu aplicativo cliente de renderização remota do Azure. Os resultados obtidos da execução dos testes em um celular ou HoloLens2 geralmente são menos úteis, pois eles comprovaram mostrar uma variação significativa em dispositivos de ponto de extremidade de baixa potência. O local no qual você coloca o laptop deve estar aproximadamente no mesmo local em que você espera usar o dispositivo que executa o aplicativo cliente de renderização remota do Azure.

Aqui estão algumas etapas simples para um teste rápido de sua conectividade de rede:

1. **Execute uma ferramenta de teste de rede como www.speedtest.net para obter dados sobre a latência geral e a largura de banda de upstream/downstream de sua conexão de rede.**
Escolha um servidor mais próximo de você e execute o teste. Embora o servidor não seja o data center do Azure ao qual a renderização remota do Azure se conectará, os dados resultantes ainda serão úteis para entender o desempenho da conexão com a Internet e do Wi-Fi.
   * **Requisito mínimo** de renderização remota do Azure: aproximadamente 40 Mbps downstream e 5 Mbps upstream.
   * **Recomendado** para renderização remota do Azure: de aproximadamente 100 Mbps downstream e 10 Mbps upstream.
É recomendável executar o teste várias vezes e assumir os piores resultados.
1. **Use uma ferramenta como www.azurespeed.com que mede a latência para os data centers do Azure**. Selecione o data center do Azure com suporte pelo processamento remoto do Azure que está mais próximo de você (consulte [regiões com suporte](regions.md)) e execute um **teste de latência**. Se houver variação nos números que você vê, dê tempo aos resultados para estabilizar.
   * **Requisito mínimo** para renderização remota do Azure: a latência deve ser de forma consistente menor que 100 ms.
   * **Recomendado** para renderização remota do Azure: a latência deve ser consistentemente menor que 70 ms.

Embora a baixa latência não seja uma garantia de que a renderização remota do Azure funcionará bem em sua rede, normalmente vimos que ela está funcionando corretamente em situações em que esses testes passaram com êxito.
Se você estiver encontrando artefatos como hologramas instável, de tremulação ou de salto ao executar a renderização remota do Azure, consulte o [Guia de solução de problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
