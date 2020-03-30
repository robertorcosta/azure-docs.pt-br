---
title: 'Trabalhando remotamente: Considerações do Network Virtual Appliance (NVA) para o trabalho remoto | Azure VPN Gateway'
description: Este artigo ajuda você a entender as coisas que você deve levar em consideração trabalhando com NVAs (Network Virtual Appliances) no Azure durante a pandemia COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337104"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Trabalhando remotamente: Considerações do Network Virtual Appliance (NVA) para o trabalho remoto

>[!NOTE]
>Este artigo descreve como você pode aproveitar os dispositivos virtuais da Rede, o Azure, a rede Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e mitigar os problemas de rede que você está enfrentando devido à crise do COVID-19.
>

Alguns clientes do Azure utilizam NVAs (Network Virtual Appliances, dispositivos virtuais de rede de terceiros) do Azure Marketplace para fornecer serviços críticos, como VPN ponto a ponto para seus funcionários que estão trabalhando em casa durante a epidemia do COVID-19. Este artigo descreve algumas orientações de alto nível a serem consideradas ao aproveitar NVAs no Azure para fornecer soluções de acesso remoto.

## <a name="nva-performance-considerations"></a>Considerações de desempenho da NVA

Todos os principais fornecedores de NVA no Azure Marketplace devem ter recomendações sobre o tamanho da VM e o número de instâncias a serem usados ao implantar suas soluções.  Embora quase todos os fornecedores de NVA permitirão que você escolha qualquer tamanho que esteja disponível para você em uma determinada região, é muito importante que você siga as recomendações dos fornecedores para tamanhos de exemplo do Azure VM, já que essas recomendações são os tamanhos de VM que o fornecedor fez com o desempenho testando com em Azure.  

### <a name="consider-the-following"></a>Considere o seguinte

- **Capacidade e número de usuários simultâneos** - Esse número é particularmente importante para os usuários de VPN ponto a ponto, pois cada usuário conectado criará um túnel criptografado (IPSec ou SSL VPN).  
- **Throughput agregado** - Qual é a largura de banda agregada que você precisará para acomodar o número de usuários que você precisa para o qual você precisará para fornecer acesso remoto.
- **O tamanho da VM que você vai precisar** - Você deve sempre usar tamanhos de VM recomendados pelo fornecedor NVA.  Para vpn ponto a ponto, se você tiver muitas conexões simultâneas de usuários, você deve estar usando tamanhos maiores de VM, como [VMs da série Dv2 e DSv2.](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Série Dv2 e Dsv2") Essas VMs tendem a ter mais vCPUs e podem lidar com sessões de VPN mais simultâneas.  Além de ter mais núcleos virtuais, tamanhos maiores de VM no Azure têm mais capacidade de largura de banda agregada do que tamanhos menores de VM.
    > **Importante:** Cada fornecedor utiliza recursos de forma diferente.  Se não estiver claro quais tamanhos de instância você deve usar para acomodar sua carga estimada do usuário, você deve entrar em contato diretamente com o fornecedor de software e pedir-lhes uma recomendação.
- **Número de instâncias** - Se você espera ter um grande número de usuários e conexões, há limites para o que o dimensionamento dos tamanhos de instâncias de NVA pode alcançar.  Considere a implantação de várias instâncias de VM.
- **IPSec VPN vs SSL VPN** - Em geral, as implementações do IPSec VPN têm um desempenho melhor do que as implementações do SSL VPN.  
- **Licenciamento** - Certifique-se de que as licenças de software que você adquiriu para a solução NVA cobrirão o crescimento repentino que você pode experimentar durante a epidemia covid-19.  Muitos programas de licenciamento NVA limitam o número de conexões ou largura de banda que a solução é capaz.
- **Rede acelerada** - Considere uma solução NVA que tenha suporte para rede acelerada.  Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho contorna o host do caminho de dados, reduzindo a latência, o nervosismo e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A rede acelerada é suportada na maioria dos tamanhos de instância otimizados para computação com dois ou mais vCPUs.

## <a name="monitoring-resources"></a>Monitorando recursos

Cada solução NVA possui suas próprias ferramentas e recursos para monitorar o desempenho de sua NVA.  Consulte a documentação de seus fornecedores para ter certeza de que você entende as limitações de desempenho e pode detectar quando seu NVA está próximo ou atingindo a capacidade.  Além disso, você pode olhar para o Azure Monitor Network Insights e ver informações básicas de desempenho sobre seus Dispositivos Virtuais de Rede, tais como:

- Utilização da CPU
- Entrada na rede
- Saída da rede
- Fluxos de Entrada
- Fluxos de saída

## <a name="next-steps"></a>Próximas etapas

A maioria dos principais parceiros da NVA postou orientações em torno do dimensionamento para crescimento repentino e inesperado durante o COVID-19. Aqui estão alguns links úteis para os recursos dos parceiros.

[Barracuda habilitar o trabalho em casa enquanto protege seus dados durante o COVID-19](https://www.barracuda.com/covid-19/work-from-home "Habilite o trabalho em casa enquanto protege seus dados durante o COVID-19")

[Cisco AnyConnect Implementação e referência de desempenho/dimensionamento para preparação COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect Implementação e referência de desempenho/dimensionamento para preparação COVID-19")

[Centro de Suporte de Resposta Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centro de Suporte de Resposta Citrix COVID-19")

[Orientação do F5 para abordar o aumento dramático dos trabalhadores remotos](https://www.f5.com/business-continuity "Orientação do F5 para abordar o aumento dramático dos trabalhadores remotos")

[Fortinet COVID-19 Atualizações para Clientes e Parceiros](https://www.fortinet.com/covid-19.html "Atualizações do COVID-19 para clientes e parceiros")

[Palo Alto Networks COVID-19 Centro de Resposta](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Centro de Resposta")
