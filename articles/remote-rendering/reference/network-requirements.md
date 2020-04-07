---
title: Requisitos de rede
description: Requisitos de rede e melhores práticas de rede para uma experiência ideal
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 73bbfad4c0535fa00b1aa53764eb52acb83124f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680369"
---
# <a name="network-requirements"></a>Requisitos de rede

Uma conexão de rede estável e de baixa latência a um data center do Azure é fundamental para uma boa experiência do usuário na Renderização Remota do Azure. As más condições da rede podem resultar em conexões descartadas, hologramas instáveis, nervosos ou "pulando" e um atraso perceptível ao atualizar o gráfico de cena do lado do servidor.

## <a name="guidelines-for-network-connectivity"></a>Diretrizes para conectividade de rede

Os requisitos exatos da rede dependem do seu caso de uso específico, como o número e a frequência de modificações no gráfico de cena remota, bem como a complexidade da visualização renderizada, mas há uma série de diretrizes para garantir que sua experiência seja a melhor possível:

* Sua conectividade com a internet precisa suportar pelo menos **50 Mbps a jusante** e **10 Mbps a montante** consistentemente para uma única sessão de usuário do Azure Remote Rendering, assumindo que não há tráfego concorrente na rede. Recomendamos taxas mais altas para melhores experiências. Com mais usuários na mesma rede, esses requisitos são dimensionados correspondentemente.
* O uso da **banda Wi-Fi de 5 GHz** geralmente produzirá melhores resultados do que a banda Wi-Fi de 2,4 GHz, embora ambos deva funcionar.
* Se houver outras redes Wi-Fi nas proximidades, evite usar canais Wi-Fi em uso por essas outras redes. Você pode usar ferramentas de varredura de rede como [o WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para verificar se os canais que sua rede Wi-Fi usa, estão livres de tráfego concorrente.
* Evite estritamente **o uso de repetidores Wi-Fi** ou encaminhamento de linha de entrada sobre potência.
* **Evite o tráfego intenso de largura de banda concorrente** – como o streaming de vídeo ou de jogos – na mesma rede Wi-Fi.
* Ter **uma boa força de sinal Wi-Fi** é essencial. Se possível, fique perto do seu ponto de acesso Wi-Fi e evite obstáculos entre o dispositivo cliente e os pontos de acesso.
* Certifique-se de que você sempre se conecta ao **data center Azure mais próximo** para sua [região](regions.md). Quanto mais perto o data center, menor a latência da rede, o que tem um enorme efeito na estabilidade do holograma.

## <a name="network-performance-tests"></a>Testes de desempenho em rede

Se você quiser obter uma compreensão inicial sobre se a qualidade da sua conectividade de rede é suficiente para executar a renderização remota do Azure, existem ferramentas on-line existentes que você pode usar. Recomendamos fortemente executar essas ferramentas on-line a partir de um laptop razoavelmente poderoso conectado ao mesmo Wi-Fi que o dispositivo que você está planejando executar o seu aplicativo cliente de renderização remota Azure. Os resultados obtidos com a execução dos testes em um telefone celular ou HoloLens2 são geralmente menos úteis, pois eles provaram mostrar variação significativa em dispositivos de ponto final de baixa potência. O local em que você coloca o laptop deve ser aproximadamente no mesmo local em que você espera usar o dispositivo que executa o aplicativo cliente de renderização remota Do Azure.

Aqui estão alguns passos simples para um teste rápido da conectividade de sua rede:

1. **Execute uma ferramenta de teste de rede como www.speedtest.net para obter dados sobre a latência geral e a largura de banda upstream/downstream da sua conexão de rede.**
Escolha um servidor mais próximo de você e execute o teste. Embora o servidor não seja o data center do Azure ao qual o Azure Remote Rendering se conectará, os dados resultantes ainda são úteis para entender o desempenho da sua conexão com a internet e Wi-Fi.
   * **Requisito mínimo** para renderização remota Azure: aproximadamente 40 Mbps a jusante e 5 Mbps a montante.
   * **Recomendado** para renderização remota Azure: aproximadamente 100 Mbps a jusante e 10 Mbps upstream.
Recomendamos fazer o teste várias vezes e tirar os piores resultados.
1. **Use uma ferramenta como www.azurespeed.com que mede a latência aos data centers do Azure.** Selecione o data center do Azure suportado pela renderização remota do Azure mais próxima de você (veja [regiões suportadas)e](regions.md)execute um **teste de latência**. Se houver variação nos números que você vê, dê aos resultados algum tempo para estabilizar.
   * **Requisito mínimo** para renderização remota Azure: A latência deve ser consistentemente inferior a 100 ms.
   * **Recomendação** para renderização remota Azure: A latência deve ser consistentemente inferior a 70 ms.

Embora a baixa latência não seja uma garantia de que a renderização remota do Azure funcionará bem em sua rede, geralmente vimos que ela funciona bem em situações em que esses testes passaram com sucesso.
Se você estiver encontrando artefatos como hologramas instáveis, nervosos ou saltitantes ao executar renderização remota do Azure, consulte o [guia de solução de problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

* [Quickstart: Render um modelo com Unity](../quickstarts/render-model.md)
