---
title: Linha de base de segurança do Azure para gerenciamento de API
description: Linha de base de segurança do Azure para gerenciamento de API
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796519"
---
# <a name="azure-security-baseline-for-api-management"></a>Linha de base de segurança do Azure para gerenciamento de API

A linha de base de segurança do Azure para gerenciamento de API contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base desse serviço é extraída da [versão 1,0 do benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure com nossas diretrizes de práticas recomendadas.

Para obter mais informações, consulte [visão geral das linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [controle de segurança: segurança de rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua rede virtual

**Diretrizes**: o gerenciamento de API do Azure pode ser implantado dentro de uma vnet (rede virtual) do Azure, para que possa acessar os serviços de back-end na rede. O portal do desenvolvedor e o gateway de gerenciamento de API podem ser configurados para serem acessados da Internet (externo) ou somente na vnet (interna).
- Externo: o portal de desenvolvedor e o gateway de Gerenciamento de API podem ser acessados pela Internet pública por meio de um balanceador de carga externo. O gateway pode acessar recursos na rede virtual.
- Interno: o portal de desenvolvedor e o gateway de Gerenciamento de API só podem ser acessados de dentro da rede virtual por meio de um balanceador de carga interno. O gateway pode acessar recursos na rede virtual.

O tráfego de entrada e de saída na sub-rede na qual o gerenciamento de API é implantado pode ser controlado usando o grupo de segurança de rede.

* [Como usar o Gerenciamento de API do Azure com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Usar o serviço de Gerenciamento de API do Azure com rede virtual interna](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [Integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de Vnets, sub-redes e NICs

**Orientação**: o tráfego de entrada e de saída na sub-rede na qual o gerenciamento de API é implantado pode ser controlado usando NSGs (grupos de segurança de rede). Implante um NSG em sua sub-rede de gerenciamento de API e habilite logs de fluxo do NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Cuidado: ao configurar um NSG na sub-rede de gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

* [Entender as configurações do NSG para o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Diretrizes**: para proteger as APIs Web/http críticas, configure o gerenciamento de API em uma rede virtual (vnet) no modo interno e configure um Gateway de aplicativo Azure. O gateway de aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços.

A combinação do gerenciamento de API provisionado em uma vnet interna com o front-end do gateway de aplicativo habilita os seguintes cenários:
- Use um único recurso de gerenciamento de API para expor todas as APIs para consumidores internos e clientes externos.
- Use um único recurso de gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Forneça uma maneira de alternar o acesso ao gerenciamento de API da Internet pública e ativar e desativar.

Observação: esse recurso está disponível nas camadas Premium e desenvolvedor do gerenciamento de API.

* [Como integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Entender Aplicativo Azure gateway](https://docs.microsoft.com/azure/application-gateway/)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: configurar o gerenciamento de API em uma rede virtual (vnet) no modo interno e configurar um Gateway de aplicativo Azure. O gateway de aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços.

A combinação do gerenciamento de API provisionado em uma vnet interna com o front-end do gateway de aplicativo habilita os seguintes cenários:
- Use um único recurso de gerenciamento de API para expor todas as APIs para consumidores internos e clientes externos.
- Use um único recurso de gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Forneça uma maneira de alternar o acesso ao gerenciamento de API da Internet pública e ativar e desativar.

Observação: esse recurso está disponível nas camadas Premium e desenvolvedor do gerenciamento de API.

Habilite a proteção contra DDoS do Azure Standard na vnet associada à sua implantação de gerenciamento de API para proteger contra ataques de DDoS (negação de serviço distribuído).

Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

* [Como integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Entender Aplicativo Azure gateway](https://docs.microsoft.com/azure/application-gateway/)

* [Como configurar a proteção contra DDoS do Azure Standard](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Entender a inteligência de ameaças integrada da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: o tráfego de entrada e de saída na sub-rede na qual o gerenciamento de API é implantado pode ser controlado usando NSG (grupos de segurança de rede). Implante um NSG em sua sub-rede de gerenciamento de API e habilite logs de fluxo do NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

Cuidado: ao configurar um NSG na sub-rede de gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

* [Entender as configurações do NSG para o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Como habilitar logs de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como habilitar e usar Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: configurar o gerenciamento de API em uma rede virtual (vnet) no modo interno e configurar um Gateway de aplicativo Azure. O gateway de aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços. O WAF do gateway de aplicativo fornece proteção contra vulnerabilidades e explorações de segurança comuns e pode ser executado nos dois modos a seguir:
- Modo de detecção: Monitora e registra todos os alertas de ameaça. Você pode ativar o diagnóstico de log para o gateway de aplicativo na seção diagnóstico. Você deve verificar se o log WAF está selecionado e ativado. O firewall do aplicativo Web no modo de detecção não bloqueia solicitações de entrada quando está operando no modelo de Detecção.
- Modo de prevenção: Bloqueia invasões e ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

A combinação do gerenciamento de API provisionado em uma vnet interna com o front-end do gateway de aplicativo habilita os seguintes cenários:
- Use um único recurso de gerenciamento de API para expor todas as APIs para consumidores internos e clientes externos.
- Use um único recurso de gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Forneça uma maneira de alternar o acesso ao gerenciamento de API da Internet pública e ativar e desativar.

Observação: esse recurso está disponível nas camadas Premium e desenvolvedor do gerenciamento de API.

* [Como integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Entender Aplicativo Azure WAF do gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: para gerenciar o tráfego que flui para as APIs Web/http, implante o gerenciamento de API em uma vnet (rede virtual) associada a ambiente do serviço de aplicativo no modo externo ou interno.

No modo interno, configure um gateway de Aplicativo Azure na frente do gerenciamento de API. O gateway de aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços. O WAF do gateway de aplicativo fornece proteção contra vulnerabilidades e explorações de segurança comuns.

A combinação do gerenciamento de API provisionado em uma vnet interna com o front-end do gateway de aplicativo habilita os seguintes cenários:
- Use um único recurso de gerenciamento de API para expor todas as APIs para consumidores internos e clientes externos.
- Use um único recurso de gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Forneça uma maneira de alternar o acesso ao gerenciamento de API da Internet pública e ativar e desativar.

Observação: esse recurso está disponível nas camadas Premium e desenvolvedor do gerenciamento de API.

* [Como expor APIs privadas para consumidores externos](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Como usar o gerenciamento de API em uma vnet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Firewall do aplicativo Web Azure no Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Entender Aplicativo Azure gateway](https://docs.microsoft.com/azure/application-gateway/overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimizar a complexidade e a sobrecarga administrativa das regras de segurança de rede

**Diretrizes**: Use as marcas de serviço de rede virtual (vnet) para definir os controles de acesso à rede em NSGs (grupos de segurança de rede) usados em suas sub-redes de gerenciamento de API. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

Cuidado: ao configurar um NSG na sub-rede de gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

* [Compreendendo e usando marcas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Portas necessárias para o gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para as configurações de rede relacionadas às implantações de gerenciamento de API do Azure. Use Azure Policy aliases nos namespaces "Microsoft. ApiManagement" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas implantações de gerenciamento de API do Azure e recursos relacionados. Você também pode fazer uso de definições de política internas para redes virtuais do Azure, como:
- A Proteção contra DDoS Standard deve ser habilitada

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, RBAC (controle de acesso baseado em função) e políticas em uma única definição de Blueprint. Você pode aplicar facilmente o plano gráfico a novas assinaturas, ambientes e controle e gerenciamento finos por meio da versão.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar um Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para NSGs (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras NSG individuais, você pode usar o campo "Descrição" para especificar a necessidade de negócios e/ou duração (etc.) para qualquer regra que permita o tráfego de/para uma rede.

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Como criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Como criar um NSG com uma configuração de segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações nos recursos de rede associados às implantações de gerenciamento de API do Azure. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte [controle de segurança: registro em log e monitoramento](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para o gerenciamento de API do Azure.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Diretrizes**: dentro do Azure monitor, use log Analytics espaços de trabalho para consultar e executar análises, enviar logs para o armazenamento do Azure para armazenamento de longo prazo/arquivamento ou análise offline ou exportar logs para outra solução de análise no Azure e em outro lugar usando os hubs de eventos do Azure. O gerenciamento de API do Azure gera logs e métricas para Azure Monitor por padrão. O detalhamento do registro em log pode ser configurado em uma base de todo o serviço e por API.

Além do Azure Monitor, o gerenciamento de API do Azure pode ser integrado a um ou vários serviços do Aplicativo Azure insights. As configurações de log para Application Insights podem ser configuradas tanto por serviço quanto por API.

Opcionalmente, habilite e dados integrados para o Azure Sentinel ou um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros.

* [Como definir configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como começar a usar Azure Monitor e integração de SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Como criar um pipeline personalizado de log e análise](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [Como integrar com o Aplicativo Azure insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: para o log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie logs de atividade para um log Analytics espaço de trabalho para relatório e análise, para o armazenamento do Azure para a proteção de longo prazo para os hubs de eventos do Azure para exportação em outras soluções de análise no Azure e em outro Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para o serviço de gerenciamento de API do Azure.

Para o log de auditoria do plano de dados, os logs de diagnóstico fornecem informações avançadas sobre operações e erros que são importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou.

* [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como habilitar as configurações de diagnóstico para o gerenciamento de API do Azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

* [Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Como arquivar logs em uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: o gerenciamento de API do Azure emite continuamente logs e métricas para Azure monitor, dando a você uma visibilidade quase em tempo real do estado e da integridade de suas APIs. Com os espaços de trabalho Azure Monitor e Log Analytics, você pode revisar, consultar, Visualizar, rotear, arquivar, configurar alertas e executar ações sobre métricas e logs provenientes do gerenciamento de API e de recursos relacionados. Analise e monitore os logs em busca de comportamentos anormais e examine os resultados regularmente.

Opcionalmente, integre o gerenciamento de API com o Aplicativo Azure insights e use-o como monitoramento primário ou secundário, rastreamento, relatório e ferramenta de alerta.

* [Como monitorar e examinar os logs do gerenciamento de API do Azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [Como executar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Entender Log Analytics espaço de trabalho](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Como integrar com o Aplicativo Azure insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para as instâncias de gerenciamento de API do Azure e envie os logs para um espaço de trabalho log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados coletados. Você pode criar alertas com base em suas consultas de Log Analytics espaço de trabalho.

Crie alertas de métrica para informá-lo quando algo inesperado estiver acontecendo. Por exemplo, obtenha notificações quando sua instância de gerenciamento de API do Azure exceder sua capacidade de pico esperada durante um determinado período de tempo ou se houvesse um determinado número de solicitações ou erros de gateway não autorizados em um período de tempo predefinido.

Opcionalmente, integre o gerenciamento de API com o Aplicativo Azure insights e use-o como monitoramento primário ou secundário, rastreamento, relatório e ferramenta de alerta.

Opcionalmente, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros.

* [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como habilitar as configurações de diagnóstico para o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [Como configurar uma regra de alerta para a solicitação não autorizada](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [Como exibir métricas de capacidade de uma instância de gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [Como integrar com o Aplicativo Azure insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: não aplicável; O gerenciamento de API do Azure não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

**Orientação**: não aplicável; O gerenciamento de API do Azure não processa nem produz logs relacionados ao DNS acessíveis pelo usuário.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [controle de segurança: identidade e controle de acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: manter um inventário de contas administrativas

**Orientação**: manter um inventário de contas que têm acesso administrativo ao plano de controle de gerenciamento de API do Azure (Portal do Azure).

Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e podem ser consultadas. O gerenciamento de API depende dessas funções e do controle de acesso baseado em função para habilitar o gerenciamento de acesso refinado para entidades e serviços de gerenciamento de API.

Além disso, o gerenciamento de API contém um grupo interno de administradores no sistema de usuário do gerenciamento de API. Os grupos no gerenciamento de API controlam a visibilidade das APIs no portal do desenvolvedor e os membros do grupo Administradores podem ver todas as APIs.

Siga as recomendações da central de segurança do Azure para o gerenciamento e a manutenção de contas administrativas.

* [Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [Como obter uma lista de usuários em uma instância do gerenciamento de API do Azure](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como obter uma lista de usuários atribuídos a uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [Como obter uma definição de função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Entender as recomendações de identidade e acesso da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: o gerenciamento de API do Azure não tem o conceito de senhas/chave padrão.

As assinaturas de gerenciamento de API do Azure, que são um meio de proteger o acesso às APIs, no entanto, são fornecidas com um par de chaves de assinatura geradas. Os clientes podem regenerar essas chaves de assinatura a qualquer momento.

* [Noções básicas sobre assinaturas de gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode usar recomendações da central de segurança do Azure ou de políticas internas do Azure, como:
- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

* [Como usar a central de segurança do Azure para monitorar a identidade e o acesso (visualização)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Como usar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usar SSO (logon único) com Azure Active Directory

**Diretrizes**: o gerenciamento de API do Azure pode ser configurado para aproveitar Azure Active Directory como um provedor de identidade para autenticar usuários no portal do desenvolvedor a fim de se beneficiar dos recursos de SSO oferecidos pelo Azure AD. Uma vez configurados, novos usuários do portal do desenvolvedor podem optar por seguir o processo de inscrição pronto para uso, primeiro Autenticando por meio do Azure AD e, em seguida, concluindo o processo de inscrição no portal depois de autenticado.

* [Autorizar contas de desenvolvedor usando o Azure Active Directory no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

Como alternativa, o processo de entrada/inscrição pode ser personalizado por meio da delegação. A delegação permite que você use seu site existente para lidar com a inscrição/inscrição e assinatura de desenvolvedores em produtos, em vez de usar a funcionalidade interna no portal do desenvolvedor. Ele permite que seu site seja proprietário dos dados do usuário e execute a validação dessas etapas de maneira personalizada.

* [Como delegar o registro de usuário e a assinatura do produto](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para acesso baseado em Azure Active Directory

**Orientação**: habilitar a MFA (autenticação multifator) do Azure Active Directory (AD) e seguir as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorar a identidade e o acesso na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

* [Saiba mais sobre estações de trabalho com acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como habilitar a MFA no Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: Use o PIM (Azure Active Directory Privileged Identity Management) para geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Além disso, use as detecções de risco do Azure AD para exibir alertas e relatórios sobre comportamento de usuário arriscado.

* [Como implantar Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Entender as detecções de riscos do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso ao portal do Azure apenas de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar locais nomeados no Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usar Azure Active Directory

**Orientação**: sempre que possível, use o Azure ad como o sistema de autenticação e autorização central. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também Salts, hashes e armazena com segurança as credenciais do usuário.

Configure seu portal do desenvolvedor de gerenciamento de API do Azure para autenticar contas de desenvolvedor usando Azure Active Directory.

Configure sua instância do gerenciamento de API do Azure para proteger suas APIs usando o protocolo OAuth 2,0 com Azure Active Directory (AD).

* [Como autorizar contas de desenvolvedor usando Azure Active Directory no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Como proteger uma API usando o OAuth 2,0 com gerenciamento de API e Azure Active Directory](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Como criar e configurar uma instância do AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Os clientes podem utilizar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos continuem tendo o acesso apropriado.

Os clientes podem manter o inventário das contas de usuário do gerenciamento de API e reconciliar o acesso conforme necessário. No gerenciamento de API, os desenvolvedores são os consumidores das APIs que são expostas com o gerenciamento de API. Por padrão, as contas de desenvolvedor criadas recentemente têm o estado Ativa e são associadas ao grupo Desenvolvedores. As contas de desenvolvedor que estão com estado ativa podem ser utilizadas para acessar todas as APIs nas quais estão inscritas.

Os administradores podem criar grupos personalizados ou aproveitar grupos externos em locatários Azure Active Directory associados. Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API.

* [Como gerenciar contas de usuário no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [Como obter a lista de usuários de gerenciamento de API](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [Como usar as revisões de acesso de identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Orientação**: configure sua instância de gerenciamento de API do Azure para autenticar contas de desenvolvedor usando Azure Active Directory como um provedor de identidade no gerenciamento de API do Azure.

Configure sua instância do gerenciamento de API do Azure para proteger suas APIs usando o protocolo OAuth 2,0 com Azure Active Directory (AD).

Configure a política de validação de JWT para solicitações de API de entrada para ajudar a impor a existência e a validade de um token válido.

Crie configurações de diagnóstico para contas de usuário do Azure AD e envie os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics. Configure os alertas desejados no Log Analytics. Além disso, você pode integrar o espaço de trabalho Log Analytics ao Azure Sentinel ou a um SIEM de terceiros.

Configure o monitoramento avançado com o gerenciamento de API `log-to-eventhub` usando a política, Capture quaisquer informações adicionais de contexto necessárias para análise de segurança e envie para o Azure Sentinel ou Siem de terceiros.

* [Como autorizar contas de desenvolvedor usando Azure Active Directory no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Como proteger uma API usando o OAuth 2,0 com gerenciamento de API e Azure Active Directory](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Políticas de restrição de acesso do Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [Como integrar os logs do Azure AD ao Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Como embutir o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Monitoramento avançado de APIs](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Diretrizes**: para o desvio do comportamento de logon da conta no plano de controle (o portal do Azure), use os recursos proteção de identidade do Azure Active Directory (AD) e detecção de risco para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

* [Como exibir entradas arriscadas do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e habilitar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como carregar o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível no momento; O Sistema de Proteção de Dados do Cliente não tem suporte no momento para o gerenciamento de API do Azure.

* [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [controle de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Isole os sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. As instâncias de gerenciamento de API do Azure devem ser separadas por mapeamentos de rede virtual (VNet) e marcadas adequadamente.

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Como usar o Gerenciamento de API do Azure com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: não disponível no momento; os recursos de identificação de dados, classificação e prevenção de perda não estão disponíveis no momento para o gerenciamento de API do Azure.

A Microsoft gerencia a infraestrutura subjacente para o gerenciamento de API do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: criptografar todas as informações confidenciais em trânsito

**Orientação**: as chamadas do plano de gerenciamento são feitas por meio de Azure Resource Manager sobre TLS. Um JWT (token Web JSON) válido é necessário. As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação com suporte (por exemplo, certificado de cliente ou JWT).

* [Entender a proteção de dados no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [Gerenciar configurações de TLS no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [Proteger APIs no gerenciamento de API do Azure com Azure Active Directory](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Proteger APIs no gerenciamento de API do Azure com Azure Active Directory B2C](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: não disponível no momento; os recursos de identificação de dados, classificação e prevenção de perda não estão disponíveis no momento para o gerenciamento de API do Azure. Marque os serviços de gerenciamento de API do Azure que podem estar processando informações confidenciais como tal e implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usar o RBAC do Azure para controlar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função para controlar o acesso ao gerenciamento de API do Azure. O Gerenciamento de API do Azure depende do RBAC (Controle de Acesso Baseado em Função) do Azure para habilitar o gerenciamento de acesso refinado de serviços e entidades de Gerenciamento de API (por exemplo, APIs e políticas).

* [Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usar a prevenção de perda de dados baseada em host para impor o controle de acesso

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o gerenciamento de API do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Orientação**: dados confidenciais, como certificados, chaves e valores nomeados secretos, são criptografados com chaves gerenciadas por serviço, por chave de instância de serviço. Todas as chaves de criptografia são por instância de serviço e são gerenciadas pelo serviço.

* [Entender a proteção/criptografia de dados em repouso com o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrar em log e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em aplicativos de Azure Functions de produção, bem como outros recursos críticos ou relacionados.

* [Como criar alertas para eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Como usar o Azure Monitor e o log de atividades do Azure no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [controle de segurança: gerenciamento de vulnerabilidade](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: não disponível no momento; a avaliação de vulnerabilidade na central de segurança do Azure não está disponível no momento para o gerenciamento de API do Azure.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para reduzir as vulnerabilidades relacionadas à configuração de serviço.

* [Noções básicas sobre os controles de segurança disponíveis para o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usar um processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas

**Orientação**: não disponível no momento; a avaliação de vulnerabilidade na central de segurança do Azure não está disponível no momento para o gerenciamento de API do Azure.

Plataforma subjacente verificada e corrigida pela Microsoft. Cliente para examinar os controles de segurança disponíveis para reduzir as vulnerabilidades relacionadas à configuração de serviço.

* [Noções básicas sobre os controles de segurança disponíveis para o gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitoramento da central de segurança do Azure**: não disponível no momento

**Responsabilidade**: cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [controle de segurança: inventário e gerenciamento de ativos](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas. Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

* [Como criar consultas com o grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Entender o RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e utilizar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar os recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: manter um inventário de recursos do Azure aprovados e títulos de software

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com o grafo do Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usar somente aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo de recurso específico com Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementar a lista de aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure por meio de scripts

**Orientação**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Controle de acesso baseado em função no gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [controle de segurança: configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para seu serviço de gerenciamento de API do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de seus serviços de gerenciamento de API do Azure.

* [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

**Diretrizes**: defina e implemente configurações de segurança padrão para seus serviços de gerenciamento de API do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições personalizadas de política do Azure, use o Azure DevOps ou Azure Repos para armazenar e gerenciar com segurança sua configuração de serviço de gerenciamento de API do Azure.

* [Como armazenar arquivos no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [Entender o kit de recursos DevOps do gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: defina e implemente configurações de segurança padrão para seus serviços de gerenciamento de API do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Diretrizes**: Use o DevOps Resource Kit de gerenciamento de API do Azure para executar o gerenciamento de configuração para o gerenciamento de API do Azure.

Além disso, defina e implemente configurações de segurança padrão para seus serviços de gerenciamento de API do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração de instâncias de gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Entender o kit de recursos DevOps do gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [Como configurar e gerenciar Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Entender Azure Policy efeitos](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use Key Vault para gerenciar certificados e defini-los para rotação automática. Se você usar Azure Key Vault para gerenciar o certificado SSL de domínio personalizado, verifique se o certificado foi inserido no Key Vault como um certificado, não como um segredo.

* [Como definir nomes de domínio personalizados com diretrizes para Key Vault rotação de chaves](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

**Orientação**: Use identidade de serviço gerenciada gerado pelo Azure Active Directory (AD) para permitir que sua instância de gerenciamento de API acesse com facilidade e segurança outros recursos protegidos pelo Azure AD, como Azure Key Vault.

* [Como criar uma identidade gerenciada para uma instância de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [Política para autenticar com identidade gerenciada](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

**Diretrizes**: implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte [controle de segurança: defesa contra malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, gerenciamento de API do Azure), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pré-examinar arquivos a serem carregados em recursos não computados do Azure

**Orientação**: não aplicável; Essa recomendação destina-se a recursos que não são de computação criados para armazenar dados.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, gerenciamento de API do Azure), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Orientação**: não aplicável; Essa recomendação destina-se a recursos que não são de computação criados para armazenar dados.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, gerenciamento de API do Azure), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [controle de segurança: recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: ao publicar e gerenciar suas APIs por meio do gerenciamento de API do Azure, você está aproveitando os recursos de tolerância a falhas e infraestrutura que, de outra forma, criaria, implementaria e gerenciaria manualmente. O gerenciamento de API dá suporte à implantação em várias regiões, o que torna o plano de dados impenetrável a falhas regionais sem adicionar nenhuma sobrecarga operacional.

Os recursos de backup e restauração de serviço do gerenciamento de API fornecem os blocos de construção necessários para implementar uma estratégia de recuperação de desastre. As operações de backup e restauração podem ser executadas manualmente ou automatizadas.

* [Como implantar o plano de dados de gerenciamento de API em várias regiões](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Como chamar a operação de backup de gerenciamento de API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [Como chamar a operação de restauração de gerenciamento de API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: as operações de backup e restauração fornecidas pelo gerenciamento de API do Azure executam backup e restauração completos do sistema.

Identidades gerenciadas podem ser usadas para obter certificados de Azure Key Vault para nomes de domínio personalizados de gerenciamento de API. Faça backup de todos os certificados armazenados no Azure Key Vault.

* [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Como fazer backup de certificados Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: validar backups executando uma restauração de teste do serviço e certificados de backups.

* [Como chamar a operação de restauração de gerenciamento de API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [Como restaurar Azure Key Vault certificados](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: o gerenciamento de API do Azure grava backups em contas de armazenamento do Azure de Propriedade do cliente. Siga as recomendações de segurança de armazenamento do Azure para proteger seu backup.

* [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Recomendação de segurança para o armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

Habilite a exclusão reversível no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

* [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controle de segurança: resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: criar um guia de resposta a incidentes

**Diretrizes**: Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Aproveite o guia de tratamento de incidentes de segurança de computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: criar um procedimento de classificação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) usando marcas e cria um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

* [Alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Usar marcações para organizar seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identificar pontos fracos e lacunas e revisar o plano conforme necessário.

* [Guia de publicação do NIST para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma entidade ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato da segurança da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoramento da central de segurança do Azure**: Sim

**Responsabilidade**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizar a resposta a alertas de segurança

**Diretrizes**: utilize o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

* [Como configurar a automação de fluxo de trabalho e os aplicativos lógicos](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [controle de segurança: testes de penetração e exercícios de equipe vermelhos](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e assegure a correção de todas as descobertas de segurança críticas dentro de 60 dias

**Orientação**: * [siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem gerenciada da Microsoft, serviços e aplicativos, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [benchmark de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
