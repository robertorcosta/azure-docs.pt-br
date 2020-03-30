---
title: Controle de Segurança Azure - Segurança de Rede
description: Segurança da rede de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251865"
---
# <a name="security-control-network-security"></a>Controle de Segurança: Segurança de rede

As recomendações de segurança da rede se concentram em especificar quais protocolos de rede, portas TCP/UDP e serviços conectados à rede são permitidos ou não têm acesso aos serviços do Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos usando grupos de segurança de rede ou firewall do Azure em sua rede virtual

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1,1 | 9.2, 9.4, 14.1-14.3 | Cliente |

Certifique-se de que todas as implantações de rede virtual tenham um Grupo de Segurança de Rede aplicado com controles de acesso de rede específicos para as portas e fontes confiáveis do seu aplicativo. Use os serviços do Azure com o Private Link ativado, implante o serviço dentro do seu Vnet ou conecte-se privadamente usando pontos finais privados. Para requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico.

Alternativamente, se você tiver um caso de uso específico, os requisitos podem ser atendidos implementando o Firewall Do Azure.

Informações gerais sobre o Link Privado:

https://docs.microsoft.com/azure/private-link/private-link-overview

Como criar uma Rede Virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar e configurar o Firewall Do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registrar a configuração e o tráfego de Vnets, Subnets e NICs

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.2 | 9.3, 12.2 | Cliente |

Use o Azure Security Center e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. Habilite os registros de fluxo do NSG e envie logs em uma conta de armazenamento para auditoria de tráfego.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Entenda a segurança de rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos web críticos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1,3 | 9,5 | Cliente |

Implante o WAF (Azure Web Application Firewall, firewall de aplicativos da Web) em frente a aplicativos web críticos para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, hub de eventos ou espaço de trabalho do Log Analytics.

Como implantar o Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Habilite a proteção padrão DDoS em suas Redes Virtuais Azure para se proteger contra ataques DDoS. Use o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

Implante o Firewall Azure em cada um dos limites de rede &quot;da organização&quot; com o Threat Intelligence ativado e configurado para alertar e negar o tráfego de rede malicioso.

Use o acesso da Rede Just In Time do Azure para configurar OSCs para limitar a exposição de pontos finais a endereços IP aprovados por um período limitado.

Use o Azure Security Center Adaptive Network Hardening para recomendar configurações de NSG que limitam portas e IPs de origem com base em informações reais de tráfego e ameaças.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar o Firewall Do Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Entenda o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Entenda o endurecimento de rede adaptativa do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Entenda o controle de acesso da rede do Azure Just In Time:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Gravar pacotes de rede e registros de fluxo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.5 | 12.5, 15.8 | Cliente |

Gravar logins de fluxo NSG em uma conta de armazenamento para gerar registros de fluxo. Se necessário para investigar atividades anômalas, habilite a captura de pacotes do Network Watcher.

Como ativar registros de fluxo do NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de detecção/prevenção de intrusões baseados em rede (IDS/IPS)

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.6 | 12.6, 12.7 | Cliente |

Implante o Firewall Azure em cada um dos limites de rede &quot;da organização&quot; com o Threat Intelligence ativado e configurado para alertar e negar o tráfego de rede malicioso.

Como implantar o Firewall Do Azure:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com o Firewall Do Azure:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos web

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.7 | 12.9, 12.10 | Cliente |

Implantar o Gateway de aplicativos do Azure para aplicativos web com HTTPS/SSL ativado para certificados confiáveis.

Como implantar o Gateway de aplicativos:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Como configurar o Application Gateway para usar https:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Entenda o balanceamento de carga da camada 7 com gateways de aplicativos web do Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Use tags de serviço de rede virtual para definir controles de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

Entenda e use etiquetas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.9 | 11.1 | Cliente |

Defina e implemente configurações de segurança padrão para recursos de rede com a Diretiva Azure.

Você também pode usar o Azure Blueprints para simplificar implantações do Azure em larga escala, embalando artefatos-chave do ambiente, como modelos do Azure Resource Manager, controles RBAC e políticas, em uma definição de projeto único. Você pode aplicar o projeto a novas assinaturas e ajustar o controle e o gerenciamento por meio de versionamentos.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política do Azure para rede:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Como criar um Projeto Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração de tráfego de documentos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1,1 | 11.2 | Cliente |

Use tags para NSGs e outros recursos relacionados à segurança da rede e fluxo de tráfego. Para regras individuais do &quot;NSG, use o campo Descrição&quot; para especificar a necessidade e/ou duração dos negócios (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Como criar e usar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com uma configuração de segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use ferramentas automatizadas para monitorar configurações de recursos de rede e detectar alterações

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Use a diretiva do Azure para validar (e/ou remediar) a configuração para recursos de rede.

Como configurar e gerenciar a política do Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política do Azure para rede:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Próximas etapas

- Veja o próximo controle de segurança: [Registro e Monitoramento](security-control-logging-monitoring.md)
