---
title: Visão geral da proteção contra DDoS do Azure Standard
description: Saiba mais sobre o serviço de Proteção contra DDoS do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: fc47e1f4fbdb48e6e0abc1f2a7e32127b0325f47
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82130966"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral de Proteção contra DDoS do Azure Standard

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.

A Proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, oferece defesa contra ataques de DDoS. A proteção contra DDoS do Azure fornece as seguintes camadas de serviço:

- **Básica**: habilitada automaticamente como parte da plataforma Azure. O monitoramento de tráfego sempre ativa e a mitigação em tempo real de ataques comuns em nível de rede, fornecem as mesmas defesas utilizadas pelo serviços online da Microsoft.Toda a escala da rede global do Azure pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões.A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.
- **Standard**: fornece funcionalidades de mitigação adicionais à camada de serviço Básica ajustadas especificamente para os recursos de Rede Virtual do Azure. A Proteção contra DDoS Standard é simples de habilitar e não exige nenhuma alteração no aplicativo. Políticas de proteção são ajustadas por meio do monitoramento de tráfego dedicado e algoritmos de aprendizado de máquina. As políticas são aplicadas a endereços IP públicos associados a recursos implantados em redes virtuais, como as instâncias do Azure Load Balancer, do Azure Application Gateway e do Azure Service Fabric, mas essa proteção não se aplica a Ambientes do Serviço de Aplicativo.A telemetria em tempo real está disponível por meio de exibições do Azure Monitor durante um ataque e para fins de histórico. A análise avançada de mitigação de ataque está disponível por meio das configurações de diagnóstico. A proteção da camada de aplicativo pode ser adicionada por meio do [Firewall do aplicativo Web do Gateway de Aplicativo do Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou instalando um firewall de terceiros do Azure Marketplace. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md) IPv4 e IPv6 do Azure.

|Recurso                                         |Proteção contra DDoS Básica                 |Proteção contra DDoS Standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Monitoramento de tráfego ativo & detecção de AlwaysOn |Sim                                   |Sim                                           |
|Mitigações de ataque automáticas                    |Sim                                   |Sim                                           |
|Garantia de disponibilidade                          |Região do Azure                          |Aplicativo                                   |
|Políticas de mitigação                             |Ajustado para o volume da região de tráfego do Azure |Ajustado para o volume de tráfego do aplicativo          |
|Métricas & alertas                                |Não                                    |Métricas de ataque em tempo real & logs de recursos por meio de Azure Monitor                                 |
|Relatórios de mitigação                              |Não                                    |Lançar relatórios de mitigação de ataque                |
|Logs de fluxo de mitigação                            |Não                                    |Fluxo de log do NRT para integração do SIEM           |
|Personalização da política de mitigação                 |Não                                    |Envolver especialistas em DDoS                           |
|Suporte                                         |Melhor esforço                           |Acesso a especialistas em DDoS durante um ataque ativo|
|Contrato de Nível de Serviço                                             |Região do Azure                          |Garantia de aplicativo & proteção de custo       |
|Preços                                         |Grátis                                  |Uso mensal de & com base                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitigados pela Proteção contra DDoS Standard

A Proteção contra DDoS Standard pode mitigar os tipos de ataques a seguir:

- **Ataques volumétricos**: a meta do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Eles incluem inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A proteção contra DDoS Standard atenua esses ataques potenciais de vários gigabytes, exportando-os e Depurando-os, com a escala de rede global do Azure, automaticamente.
- **Ataques de protocolo**: esses ataques renderizam um destino inacessível explorando uma vulnerabilidade na pilha de protocolos das camadas 3 e 4. Eles incluem ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. A Proteção contra DDoS Standard mitiga esses ataques diferenciando entre o tráfego mal-intencionado e o legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de recursos (aplicativo)**: esses ataques são direcionados a pacotes de aplicativo Web para interromper a transmissão de dados entre os hosts. Esses ataques incluem violações de protocolo HTTP, injeção de SQL, scripts intersites e outros ataques de camada 7. Use um firewall do aplicativo Web, como o [Firewall do aplicativo Web do gateway de aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure, bem como o padrão de proteção contra DDoS para fornecer defesa contra esses ataques. Também há ofertas de firewall do aplicativo Web de terceiros disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A Proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinado com o Firewall do aplicativo Web do gateway de aplicativo ou um firewall de aplicativo Web de terceiros implantado em uma rede virtual com um IP público, a proteção contra DDoS Standard pode fornecer a capacidade de mitigação completa da camada 3 para a camada 7.

## <a name="ddos-protection-standard-features"></a>Recursos da Proteção contra DDoS do Azure Standard

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Os recursos da Proteção contra DDoS do Azure Standard:

- **Integração de plataforma nativa:** nativamente integrado ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
- **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
- **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
- **Ajuste adaptativo:** A criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo.
- **Proteção multicamada:** fornece proteção contra DDoS de pilha completa, quando usado com um firewall do aplicativo Web.
- **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Análise de ataque:** obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, além de um resumo completo após o término dele. Transmita logs do fluxo de mitigação por streaming para um sistema de SIEM (gerenciamento de evento e informações de segurança) offline para monitoramento quase em tempo real durante um ataque.
- **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
- **Alerta de ataque:** Os alertas podem ser configurados no início e na interrupção de um ataque e na duração do ataque, usando métricas de ataque internas. Os alertas integram-se ao seu software operacional, como Microsoft Azure logs de monitor, Splunk, armazenamento do Azure, email e o portal do Azure.
- **Garantia de custo:** Créditos de serviço de transferência de dados e de expansão de aplicativos para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação da Proteção contra DDoS do Azure Standard

A Proteção contra DDoS Standard monitora a utilização de tráfego real e compara-a constantemente com os limites definidos na Política de DDoS. Quando esse limite de tráfego for excedido, a mitigação de DDoS será iniciada automaticamente. Quando o tráfego retorna para baixo do limite, a mitigação é removida.

![Atenuação](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de Proteção contra DDoS e diversas verificações são executadas, incluindo as seguintes:

- Assegure que os pacotes estejam em conformidade com as especificações de Internet e não sejam malformados.
- Interagir com o cliente para determinar se o tráfego é potencialmente um pacote adulterado (por exemplo: Aut. SYN ou Cookie SYN ou removendo um pacote para que a fonte o retransmita).
- Estabelecer limites de taxa para pacotes se nenhum outro método de imposição puder ser executado.

A Proteção contra DDoS bloqueia o tráfego e encaminha o tráfego restante para seu destino pretendido. Em poucos minutos de detecção de ataque, você será notificado pelo uso de métricas no Azure Monitor. Configurando o registro em log na telemetria da Proteção contra DDoS Standard, você pode gravar os logs de opções disponíveis para análise futura. Os dados de métrica no Azure Monitor para a Proteção contra DDoS Standard são mantidos por 30 dias.

A Microsoft firmou parceria com [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface em que você pode gerar tráfego contra endereços IP públicos com a Proteção contra DDoS habilitada para fins de simulação. A simulação do BreakPoint Cloud permitirá que você:

- Valide como a Proteção contra DDoS Standard do Microsoft Azure protege seus recursos do Azure contra ataques de DDoS
- Otimize o processo de resposta a incidentes durante ataques de DDoS
- Documente a conformidade de DDoS
- Treine suas equipes de segurança de rede

## <a name="next-steps"></a>Próximas etapas

- [Configurar a proteção contra DDoS Standard](manage-ddos-protection.md)
