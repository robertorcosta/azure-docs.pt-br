---
title: Linha de base de segurança do Azure para WAN virtual
description: A linha de base de segurança de WAN virtual fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328662"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Linha de base de segurança do Azure para WAN virtual

Essa linha de base de segurança aplica diretrizes da [versão 2,0 do benchmark de segurança do Azure](../security/benchmarks/overview.md) para Microsoft Azure Wan virtual. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à WAN virtual. Os **controles** não aplicáveis à WAN virtual foram excluídos.

Para ver como a WAN virtual é totalmente mapeada para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de WAN virtual completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Diretrizes** : a WAN virtual do Azure não oferece suporte à implantação diretamente em uma rede virtual. No entanto, você ainda pode aplicar regras de grupo de segurança de rede em recursos do spoke, usar as proteções do firewall do Azure ou criar tabelas de rotas personalizadas para impedir ou permitir o tráfego privado.

- [Cenários de roteamento personalizado](scenario-any-to-any.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas 

**Orientação** : Use o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os datacenters do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública, oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. 

Para VPN ponto a site e VPN site a site, conecte dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e ExpressRoute. Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [ExpressRoute na WAN virtual](virtual-wan-expressroute-portal.md)

- [Visão geral de VPN site a site](virtual-wan-site-to-site-portal.md)

- [Visão geral de VPN ponto a site](virtual-wan-point-to-site-portal.md)

- [Link privado](howto-private-link.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Compartilhado

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer acesso de rede privada aos serviços do Azure

**Orientação** : Use o link privado do Azure para habilitar o acesso privado à WAN virtual do Azure de suas redes virtuais sem cruzar a Internet. O acesso privado é outra medida de defesa aprofundada, além da segurança de autenticação e tráfego oferecida pelos serviços do Azure.

- [Entender o link privado do Azure](../private-link/private-link-overview.md)

- [Link privado da WAN virtual](howto-private-link.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Diretrizes** : Proteja seus recursos de WAN virtual do Azure contra ataques de redes externas, incluindo ataques de DDoS (negação de serviço distribuído), ataques específicos de aplicativo e tráfego de Internet não solicitado e potencialmente mal-intencionado. 

Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. Escolha a proteção contra DDoS do Azure para seus ativos contra ataques em suas redes virtuais do Azure. Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados aos seus recursos relacionados à rede.

- [Documentação do firewall do Azure](/azure/firewall)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](/azure/virtual-network/manage-ddos-protection) 

- [Recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md#recs-network)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Orientação** : a melhor maneira de implantar sistemas de prevenção ou detecção de intrusão na WAN virtual do Azure é usar uma solução de virtualização de rede nas redes spoke conectadas ao Hub virtual.  Mais informações estão disponíveis nos cenários de roteamento nos links referenciados. 

- [Cenário – Rotear o tráfego por meio de um NVA](scenario-route-through-nva.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes** : Use as marcas de serviço de rede virtual do Azure para definir os controles de acesso à rede nos grupos de segurança de rede do Azure ou no firewall do Azure configurados para seus recursos de WAN 

Use as marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo: {VirtualWAN: rede virtual}) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: serviço de nomes de domínio seguro (DNS)

**Diretrizes** : a WAN virtual do Azure oferece servidores DNS personalizados para gateways de VPN ponto a site. 

Siga as práticas recomendadas para a segurança do DNS para mitigar ataques comuns, como pendente DNS, amplificações de DNS ataques, envenenamento e falsificação de DNS e assim por diante.

Quando o DNS do Azure é usado como seu serviço DNS autoritativo, verifique se as zonas e os registros DNS estão protegidos contra modificação acidental ou mal-intencionada usando o controle de acesso baseado em função do Azure (RBAC do Azure) e os bloqueios de recursos.

- [Visão geral do DNS do Azure](../dns/dns-overview.md) 

- [Guia de implantação do DNS (sistema de nomes de domínio) seguro](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Impedir entradas DNS pendente e evitar subdomínio tomada](../security/fundamentals/subdomain-takeover.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar Azure Active Directory como o sistema de identidade e autenticação central

**Diretrizes** : a VPN ponto a site da WAN virtual do Azure é integrada ao Azure Active Directory (Azure AD), que é o serviço de gerenciamento de identidade e acesso padrão do Azure. Você deve padronizar o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, as máquinas virtuais do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.
- Os recursos da sua organização, como aplicativos no Azure ou seus recursos de rede corporativa.

Proteja o Azure AD com alta prioridade na prática de segurança de nuvem de sua organização. Avalie a sua postura de identidade e de segurança com o recurso de Pontuação de segurança da central de segurança do Azure para avaliar a precisão da sua configuração de acordo com as práticas recomendadas. Implemente as recomendações de práticas recomendadas da Microsoft para aprimoramentos na sua postura de segurança.

O AD do Azure também dá suporte a identidades externas, que permitem aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa. Examine as informações sobre como usar o Azure AD em cenários de VPN ponto a site nos links referenciados.

- [Criar um locatário Azure Active Directory (AD) para conexões de protocolo P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar a autenticação de Azure Active Directory para VPN de usuário](virtual-wan-point-to-site-azure-ad.md)

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usar provedores de identidade externos para o aplicativo](/azure/active-directory/b2b/identity-providers)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado em Azure Active Directory

**Diretrizes** : a VPN ponto a site da WAN virtual do Azure é integrada ao Azure Active Directory (Azure AD), que dá suporte a controles de autenticação fortes com autenticação multifator e métodos fortes sem senha.

- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de identidade e acesso da central de segurança do Azure para obter as práticas recomendadas para a configuração de autenticação multifator. A autenticação multifator pode ser imposta em todos, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.

- Autenticação com senha – três opções de autenticação com senha estão disponíveis. Isso inclui o Windows Hello para empresas, Microsoft Authenticator aplicativo e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, verifique se o nível mais alto do método de autenticação forte é usado, seguido por uma distribuição da política de autenticação forte apropriada para outros usuários.

- [Como habilitar a MFA no VPN P2S para WAN virtual](openvpn-azure-ad-mfa.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso a recursos do Azure com base em condições

**Diretrizes** : a VPN ponto a site da WAN virtual do Azure dá suporte ao acesso condicional do Azure ad para um controle de acesso mais granular com base em condições definidas pelo usuário. Por exemplo, os logons de usuário de determinados intervalos de IP precisam usar a autenticação multifator para fazer logon. A política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso.

- [Políticas de acesso condicional comum](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Acesso condicional de VPN P2S de WAN virtual](openvpn-azure-ad-mfa.md#conditional)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar exposição de credencial não intencional

**Diretriz** : implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Para o GitHub, você pode usar o recurso de verificação de segredo nativo para identificar as credenciais ou outra forma de segredos dentro do código.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Verificação de segredo do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, consulte o [benchmark de segurança do Azure: acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes** : a WAN virtual do Azure usa controles de acesso baseado em função do Azure (RBAC do Azure) para isolar o acesso a sistemas críticos de negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais elas estão.

Além disso, restrinja o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory, ferramentas de segurança e ferramentas de gerenciamento de sistema com agentes instalados em sistemas de negócios críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes** : estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Use estações de trabalho de usuário altamente protegidas ou bastiões do Azure para tarefas administrativas. Use o Azure Active Directory (Azure AD), a ATP (proteção avançada contra ameaças) do Microsoft defender ou Microsoft Intune para implantar uma estação de trabalho de usuário segura e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas centralmente para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](../active-directory/devices/concept-azure-managed-workstation.md)

- [Implantar uma estação de trabalho com acesso privilegiado](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Orientação** : a criptografia é essencial para o tráfego em redes externas e públicas.

- Use controles de acesso, os dados em trânsito devem ser protegidos contra ataques "fora da banda" (por exemplo, captura de tráfego) usando a criptografia para garantir que os invasores não possam ler ou modificar os dados com facilidade.

- Certifique-se de que o tráfego HTTP, que os clientes que se conectam aos recursos do Azure, possam negociar TLS v 1.2 ou superior.-

- Para gerenciamento remoto, use SSH (para Linux) ou RDP/TLS (para Windows) em vez de protocolo não criptografado. As versões de SSL/TLS/SSH obsoletas, os protocolos e as codificações fracas devem ser desabilitados.-

- Na infraestrutura subjacente, o Azure fornece dados em criptografia de trânsito por padrão para o tráfego de dados entre os datacenters do Azure.

Em geral, fornecemos criptografia no backbone seguro da Microsoft e oportunidades para criptografar o tráfego em sua VPN site a site, VPN site a site por meio do Azure ExpressRoute e VPN de usuário ponto a site.

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informações sobre segurança TLS](/security/engineering/solving-tls1-problem)

- [Criptografia dupla para dados do Azure em trânsito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: garantir que a equipe de segurança tenha acesso ao inventário de ativos e metadados

**Orientação** : aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

A WAN virtual do Azure também dá suporte a implantações de recursos baseadas em Azure Resource Manager e consultas do grafo de recursos do Azure. 

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar apenas os serviços aprovados do Azure

**Diretrizes** : Use Azure Policy para restringir os serviços que podem ser provisionados em seu ambiente. Consulte e descubra recursos com o grafo de recursos do Azure em suas assinaturas e use Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar o tipo de recurso específico com Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação** : Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

## <a name="logging-and-threat-detection"></a>Registro em log e detecção de ameaças

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para gerenciamento de identidade e acesso do Azure

**Diretrizes** : o Azure Active Directory (Azure AD) fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure ad ou integrados com Azure monitor, Azure Sentinel, Siem ou ferramentas de monitoramento para casos de uso de análise e monitoramento mais sofisticados. Eles são:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entrada arriscada-uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Use a central de segurança do Azure para criar alertas sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha, incluindo contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, use o módulo de proteção contra ameaças da central de segurança para coletar mais alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (SQL DB e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade em anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividade de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar a proteção de identidade do Azure](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Diretrizes** : Use as ferramentas de captura de pacote de VPN para registrar os pacotes de rede que trafegam entre os recursos de WAN virtual do Azure. Isso pode ajudar no processo de depuração relacionado à sua rede híbrida. Embora não seja possível implantar um grupo de segurança de rede na WAN virtual, você pode implantá-lo nos recursos de rede virtual do spoke.

Habilite os logs de fluxo do grupo de segurança de rede em seus grupos de segurança de rede para a auditoria de tráfego.

Habilite o recurso Análise de Tráfego do Azure para processar logs de fluxo que são retidos na conta de armazenamento e, em seguida, envie-os para um espaço de trabalho do Log Analytics. Análise de Tráfego fornece informações adicionais sobre o fluxo de tráfego para suas redes do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md) 

A WAN virtual não produz nem processa logs de consulta DNS que precisam ser habilitados.

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Orientação** : logs de atividades do Azure, habilitados automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos de WAN virtual do Azure, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro durante a solução de problemas ou para monitorar como um usuário em sua organização modificou um recurso. No entanto, a WAN virtual não produz logs de recursos do Azure.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação** : centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados. Verifique se você está integrando os logs de atividades do Azure em seus sistemas de registro central. 

Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Compartilhado

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – processo de atualização de resposta a incidentes para o Azure

**Orientação** : Verifique se sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está experimentando-os regularmente para garantir a preparação. Verifique se a oferta de serviço está incluída no processo de resposta a incidentes, conforme aplicável.

- [Implementar a segurança em todo o ambiente corporativo](https://aka.ms/AzSec4) 
- [Guia de referência de resposta a incidentes](https://aka.ms/IRRG)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – notificação de incidente de instalação

**Orientação** : configurar informações de contato de incidente de segurança na central de segurança do Azure. Essas informações de contato são usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure com base em suas necessidades de resposta a incidentes.

As informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam totalmente resolvidos.

- [Como definir o contato da segurança da central de segurança do Azure](../security-center/security-center-provide-security-contact-details.md) 

- [Como definir o contato da segurança da central de segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – crie incidentes com base em alertas de alta qualidade

**Diretrizes** : a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detecção e análise – investigue um incidente

**Diretriz** : crie um guia de resposta a incidentes para sua organização. Conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretrizes** : a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (como produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenção, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretrizes** : Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos do Azure" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Cliente

## <a name="posture-and-vulnerability-management"></a>Postura e gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: postura e gerenciamento de vulnerabilidade](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: conduzir simulação de ataque regular

**Diretrizes** : de acordo com seus requisitos, realize testes de penetração ou atividades de equipe vermelhas em seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas.

Siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração do Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud o agrupamento vermelho](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="endpoint-security"></a>Segurança do ponto de extremidade

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança do ponto de extremidade](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usar a detecção e resposta de ponto de extremidade (EDR)

**Orientação** : os clientes não têm permissão explícita para configurar a detecção de ponto de extremidade e as configurações de resposta. No entanto, as máquinas virtuais usadas na oferta de WAN virtual do Azure usam esses recursos. Saiba mais sobre esses recursos gerais nos links referenciados. 

- [Visão geral da proteção avançada contra ameaças do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço Microsoft defender ATP para Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço Microsoft defender ATP para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitoramento da Central de Segurança do Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, consulte o [benchmark de segurança do Azure: governança e estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de gerenciamento de ativos e de proteção de dados 

**Orientação** : Certifique-se de documentar e comunicar uma estratégia clara para monitoramento contínuo e proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados críticos para os negócios. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- Padrão de classificação de dados de acordo com os riscos de negócios
- Visibilidade da organização de segurança em riscos e inventário de ativos 
- Aprovação da organização de segurança dos serviços do Azure para uso 
- Segurança de ativos por meio de seu ciclo de vida
- Estratégia de controle de acesso necessária de acordo com a classificação de dados organizacionais
- Uso de recursos de proteção de dados nativos e de terceiros do Azure
- Requisitos de criptografia de dados para casos de uso em trânsito e em repouso
- Padrões de criptografia apropriados

Examine as informações adicionais disponíveis nos links referenciados.

- [Recomendação da arquitetura de segurança do Azure – armazenamento, dados e criptografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Conceitos básicos de segurança do Azure-segurança de dados do Azure, criptografia e armazenamento](../security/fundamentals/encryption-overview.md)

- [Estrutura de adoção de nuvem – práticas recomendadas de segurança de dados do Azure e criptografia](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir estratégia de segmentação corporativa 

**Orientação** : estabeleça uma estratégia empresarial para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles. Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitar a operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Certifique-se de que a estratégia de segmentação seja implementada consistentemente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, bem como permissões de aplicativo ou modelos de acesso, além de controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir estratégia de gerenciamento de postura de segurança

**Diretrizes** : meça e reduza continuamente os riscos para seus ativos individuais e para o ambiente em que eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador e assim por diante.

- [Benchmark de segurança do Azure – gerenciamento de postura e vulnerabilidade](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções de organização, responsabilidades e responsabilidades

**Orientação** : Certifique-se de documentar e comunicar uma estratégia clara para funções e responsabilidades em sua organização de segurança. Priorize seus esforços com base em responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instruir equipes técnicas sobre tecnologia para proteger a nuvem.

- [Prática recomendada de segurança do Azure 1 – pessoas: treinar equipes na jornada de segurança de nuvem](https://aka.ms/AzSec1)

- [Prática recomendada de segurança do Azure 2-pessoas: educar equipes sobre tecnologia de segurança de nuvem](https://aka.ms/AzSec2)

- [Prática recomendada de segurança do Azure 3-processo: atribuir responsabilidade por decisões de segurança na nuvem](https://aka.ms/AzSec3)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir a estratégia de segurança de rede

**Diretrizes** : estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização. Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- Gerenciamento de rede centralizado e responsabilidade de segurança
- Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa
- Estratégia de correção em diferentes cenários de ameaça e ataque
- Borda da Internet e a estratégia de entrada e saída
- Estratégia de interconectividade local e de nuvem híbrida
- Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Examine as informações adicionais disponíveis nos links referenciados.

- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Benchmark de segurança do Azure-segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir a identidade e a estratégia de acesso privilegiado

**Orientação** : estabeleça a identidade do Azure e abordagens de acesso privilegiado como parte da estratégia geral de controle de acesso de segurança da sua organização. Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- Um sistema de identidade e autenticação centralizado e sua interconectividade com outros sistemas de identidade internos e externos
- Métodos de autenticação fortes em diferentes casos de uso e condições
- Proteção de usuários altamente privilegiados
- Monitoramento e manipulação de atividades de usuário de anomalias  
- Identidade do usuário e processo de reavaliação e reconciliação de acesso

Examine as informações adicionais disponíveis nos links referenciados.

- [Benchmark de segurança do Azure-gerenciamento de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de segurança do Azure-acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Prática recomendada de segurança do Azure 11-arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../security/fundamentals/identity-management-overview.md)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir a estratégia de resposta contra ameaças e registro

**Orientação** : estabeleça uma estratégia de resposta a ameaças e registro para detectar e corrigir ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de analistas com alertas de alta qualidade e experiências integradas para que eles possam se concentrar em ameaças em vez de integração e etapas manuais. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- A função e as responsabilidades da organização de operações de segurança (SecOps)
- Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor
- Captura de log e retenção para dar suporte à detecção de ameaças, resposta a incidentes e necessidades de conformidade
- Visibilidade centralizada e informações de correlação sobre ameaças, usando SIEM, recursos nativos do Azure e outras fontes – plano de comunicação e notificação com seus clientes, fornecedores e partes públicas de interesse
- Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como registro em log e detecção de ameaças, análise forense e remediação de ataque e erradicação
- Processos para lidar com incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Examine as informações adicionais disponíveis nos links referenciados.
- [Benchmark de segurança do Azure-registro em log e detecção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark de segurança do Azure-resposta de incidente](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Prática recomendada de segurança do Azure 4-Process. Atualizar processos de resposta a incidentes para a nuvem](https://aka.ms/AzSec4)

- [Guia de decisão da estrutura de adoção, registro em log e relatórios do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitoramento da Central de Segurança do Azure** : Não disponível no momento

**Responsabilidade** : Cliente

## <a name="next-steps"></a>Próximas etapas

- Consulte a [visão geral do benchmark de segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
