---
title: Perguntas frequentes sobre o Gateway de aplicativos do Azure
description: Encontre respostas para perguntas frequentes sobre o Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 242fc115c5b2324c770de480fb19985e8b874893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371260"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre o Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A seguir, perguntas comuns sobre o Azure Application Gateway.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicativo?

O Azure Application Gateway fornece um controlador de entrega de aplicativos (ADC) como um serviço. Ele oferece vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Este serviço é altamente disponível, escalável e totalmente gerenciado pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de Aplicativo suporta?

O Application Gateway oferece suporte a autodimensionamento, descarregamento de SSL e SSL de ponta a ponta, um FIREWALL de aplicativos da Web (WAF), afinidade de sessão baseada em cookies, roteamento baseado em caminho de URL, hospedagem multisite e outros recursos. Para obter uma lista completa dos recursos com suporte, confira [Introdução ao Gateway de Aplicativo](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como o Gateway de aplicativo e o Balancer de Carga Do Azure diferem?

O Application Gateway é um balanceador de carga de camada 7, o que significa que ele funciona apenas com tráfego web (HTTP, HTTPS, WebSocket e HTTP/2). Ele suporta recursos como terminação SSL, afinidade de sessão baseada em cookies e round robin para tráfego de balanceamento de carga. Balanceador de carga equilibra o tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo fornece suporte HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como o Gateway de aplicativo dá suporte a HTTP/2?

Consulte [o suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quais recursos são suportados como parte de um pool de backend?

Consulte [os recursos de backend suportados](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Em quais regiões o Application Gateway está disponível?

O Gateway de Aplicativo está disponível em todas as regiões do Azure global. Também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e [no Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Esta implantação é dedicada à minha assinatura ou é compartilhada entre clientes?

O Gateway de Aplicativo é uma implementação dedicada em sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>O Gateway de aplicativo suporta redirecionamento HTTP-para-HTTPS?

Há suporte para redirecionamento. Consulte [a visão geral do redirecionamento do Gateway de aplicativos](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Veja a [ordem de processamento do ouvinte.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontro o IP e o DNS do Gateway de aplicativo?

Se você estiver usando um endereço IP público como ponto final, você encontrará as informações de IP e DNS no recurso público de endereço IP. Ou encontrá-lo no portal, na página de visão geral do gateway do aplicativo. Se você estiver usando endereços IP internos, encontre as informações na página de visão geral.

Para o V2 SKU, abra o recurso IP público e selecione **Configuração**. O **campo dns name label (opcional)** está disponível para configurar o nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as configurações para tempo de tempo de Keep-Alive e tempo de tempo de tcp ocioso?

*O tempo hámenos do Keep-Alive* rege quanto tempo o Gateway de aplicativo esperará que um cliente envie outra solicitação HTTP em uma conexão persistente antes de reúna-a ou fechá-la. *O tempo livre de ociosidade tcp* rege quanto tempo uma conexão TCP é mantida aberta em caso de nenhuma atividade. 

O *tempo de manter-vivo* no gateway de aplicativo v1 SKU é de 120 segundos e no V2 SKU é de 75 segundos. O *tempo de espera ocioso TCP* é um padrão de 4 minutos no IP virtual frontend (VIP) do V1 e v2 SKU do Application Gateway. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS muda ao longo da vida útil do gateway de aplicativo?

No Application Gateway V1 SKU, o VIP pode mudar se você parar e iniciar o gateway do aplicativo. Mas o nome DNS associado ao gateway de aplicativo não muda ao longo da vida útil do gateway. Como o nome DNS não muda, você deve usar um alias CNAME e apontá-lo para o endereço DNS do gateway do aplicativo. No Application Gateway V2 SKU, você pode definir o endereço IP como estático, para que o nome IP e DNS não mude ao longo da vida útil do gateway do aplicativo. 

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicativo suporta IP estático?

Sim, o Application Gateway v2 SKU suporta endereços IP públicos estáticos. O v1 SKU suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de Aplicativo suporta vários IPs públicos no gateway?

Um gateway de aplicativo suporta apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quão grande devo criar minha sub-rede para o Application Gateway?

Consulte as considerações sobre o tamanho da [sub-rede do Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais de um recurso de Gateway de Aplicativo em uma única sub-rede?

Sim. Além de várias instâncias de uma determinada implantação do Application Gateway, você pode provisionar outro recurso exclusivo do Application Gateway para uma sub-rede existente que contém um recurso diferente do Application Gateway.

Uma única sub-rede não pode suportar o Standard_v2 e o Standard Application Gateway juntos.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>O Application Gateway v2 suporta rotas definidas pelo usuário (UDR)?

Sim, mas apenas cenários específicos. Para obter mais informações, consulte [visão geral da configuração do Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicativo dá suporte a cabeçalhos x-forwarded-for?

Sim. Consulte [Modificações em uma solicitação](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo leva para implantar um gateway de aplicativo? O gateway do meu aplicativo funcionará enquanto ele está sendo atualizado?

As novas implantações de SKU do Application Gateway v1 podem levar até 20 minutos para provisionar. As alterações no tamanho ou na contagem de instâncias não são perturbadoras, e o gateway permanece ativo durante esse tempo.

A maioria das implantações que usam o V2 SKU leva cerca de 6 minutos para ser provisionada. No entanto, pode levar mais tempo dependendo do tipo de implantação. Por exemplo, implantações em várias Zonas de Disponibilidade com muitas instâncias podem levar mais de 6 minutos. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso usar o Exchange Server como backend com o Application Gateway?

Não. O Application Gateway não suporta protocolos de e-mail como SMTP, IMAP e POP3. 

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Application Gateway suporta alta disponibilidade e escalabilidade?

O Application Gateway v1 SKU suporta cenários de alta disponibilidade quando você implantou duas ou mais instâncias. O Azure distribui essas instâncias em domínios de atualização e falhas para garantir que as instâncias não falhem ao mesmo tempo. O v1 SKU suporta escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

A SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se você escolher a redundância da região, as instâncias mais recentes também serão espalhadas por zonas de disponibilidade para oferecer resiliência de falha zonal.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como faço para alcançar um cenário de DR em data centers usando o Application Gateway?

Use o Traffic Manager para distribuir tráfego em vários gateways de aplicativos em diferentes data centers.

### <a name="does-application-gateway-support-autoscaling"></a>O Gateway de aplicativos suporta autodimensionamento?

Sim, o SKU Application Gateway v2 suporta escalonamento automático. Para obter mais informações, consulte [Autoscaling e Zone-redundante Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>A escala manual ou automática ou a escala diminui causa tempo de inatividade?

Não. As instâncias são distribuídas entre domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de Aplicativo suporta a drenagem de conexão?

Sim. Você pode configurar a drenagem de conexão para alterar membros dentro de uma piscina de backend sem interrupção. Para obter mais informações, consulte [a seção de drenagem de conexões do Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho da instância de médio para grande sem interrupção?

Sim.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado em uma rede virtual?

Sim. O Gateway de aplicativos é sempre implantado em uma sub-rede virtual. Esta sub-rede pode conter apenas gateways de aplicativo. Para obter mais informações, consulte [os requisitos de rede virtual e sub-rede](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>O Application Gateway pode se comunicar com instâncias fora de sua rede virtual ou fora de sua assinatura?

Desde que você tenha conectividade IP, o Application Gateway pode se comunicar com instâncias fora da rede virtual em que está. O Application Gateway também pode se comunicar com instâncias fora da assinatura em que está. Se você planeja usar IPs internos como membros do pool backend, use [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou [Gateway Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implantar coisa na sub-rede do gateway de aplicativo?

Não. Mas você pode implantar outros gateways de aplicativo na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança de rede são suportados na sub-rede do gateway de aplicativo?

Consulte [grupos de segurança de rede na sub-rede Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede do gateway de aplicativo suporta rotas definidas pelo usuário?

Consulte [as rotas definidas pelo usuário suportadas na sub-rede Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de Aplicativo? Posso aumentar esses limites?

Consulte [os limites do Gateway de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Posso usar simultaneamente o Gateway de aplicativo para tráfego externo e interno?

Sim. O Application Gateway suporta um IP interno e um IP externo por gateway de aplicativo.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Application Gateway suporta peering de rede virtual?

Sim. O peering de rede virtual ajuda a equilibrar o tráfego de carga em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores locais quando eles estão conectados por túneis ExpressRoute ou VPN?

Sim, contanto que o tráfego seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Um pool de back-end pode servir a muitos aplicativos em diferentes portas?

A arquitetura de microserviços é suportada. Para sondar em diferentes portas, você precisa configurar várias configurações HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Os testes personalizados suportam curingas ou regex em dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como as regras de roteamento são processadas no Gateway de aplicativo?

Consulte [Ordem das regras de processamento](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para testes personalizados, o que significa o campo Host?

O campo Host especifica o nome para enviar o teste para quando você configurou vários locais no Gateway de aplicativo. Caso contrário, use '127.0.0.1'. Este valor é diferente do nome do host da máquina virtual. Seu formato \<\>é\<\>protocolo\<\>\<://\>host : caminho da porta .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir o acesso ao Application Gateway a apenas alguns endereços IP de origem?

Sim. Consulte [restringir o acesso a IPs de origem específicas](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Posso usar a mesma porta para ouvintes de frente para o público e para ouvintes privados?

Não.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe alguma orientação para migrar do V1 SKU para o V2 SKU?

Sim. Para obter detalhes, [migrate Azure Application Gateway e Web Application Firewall de v1 para v2](migrate-v1-v2.md).

### <a name="does-application-gateway-support-ipv6"></a>O Gateway de aplicativo suporta IPv6?

O Gateway de aplicativo v2 não suporta atualmente IPv6. Ele pode operar em um VNet de pilha dupla usando apenas IPv4, mas a sub-rede de gateway deve ser somente IPv4. O Gateway de aplicativo v1 não suporta VNets de pilha dupla. 

## <a name="configuration---ssl"></a>Configuração - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Quais certificados o Application Gateway suporta?

O Application Gateway suporta certificados auto-assinados, certificados de autoridade de certificado (CA), certificados de Validação Estendida (EV) e certificados curinga.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quais suítes de cifras o Application Gateway suporta?

O Application Gateway suporta as seguintes suítes de cifras. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Para obter informações sobre como personalizar as opções ssl, consulte [Configurar versões de política SSL e suítes de cifra sinuosas no Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Application Gateway suporta a recriptografia do tráfego para o backend?

Sim. O Application Gateway suporta descarregamento SSL e SSL de ponta a ponta, que recriptografam o tráfego para o backend.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar versões de protocolo SSL?

Sim. Você pode configurar o Application Gateway para negar TLS1.0, TLS1.1 e TLS1.2. Por padrão, o SSL 2.0 e o 3.0 já estão desativados e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar conjuntos de criptografia e ordem de política?

Sim. No Application Gateway, você pode [configurar suítes de cifras](application-gateway-ssl-policy-overview.md). Para definir uma política personalizada, habilite pelo menos uma das seguintes suítes cifradas. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

O Application Gateway usa sha256 para o gerenciamento de back-end.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Quantos certificados SSL o Application Gateway suporta?

O Application Gateway suporta até 100 certificados SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quantos certificados de autenticação para recriptografia backend o Application Gateway suporta?

O Application Gateway suporta até 100 certificados de autenticação.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O Gateway de aplicativos integra nativamente o Azure Key Vault?

Sim, o Application Gateway v2 SKU suporta Key Vault. Para obter mais informações, consulte [o término do SSL com os certificados Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como configurar ouvintes HTTPS para sites .com e .net? 

Para roteamento baseado em vários domínios (baseado em host), você pode criar ouvintes multisites, configurar ouvintes que usam HTTPS como protocolo e associar os ouvintes às regras de roteamento. Para obter mais informações, consulte [Hospedar vários sites usando o Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usar caracteres especiais na minha senha de arquivo .pfx?

Não, use apenas caracteres alfanuméricos na senha do arquivo .pfx.

## <a name="configuration---web-application-firewall-waf"></a>Configuração - firewall de aplicativos web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>O WAF SKU oferece todos os recursos disponíveis no SKU padrão?

Sim. O WAF suporta todos os recursos do SKU padrão.

### <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

Monitore o WAF através do registro de diagnóstico. Para obter mais informações, consulte [registro de diagnóstico e métricas para Gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Não. O modo de detecção só registra o tráfego que aciona uma regra WAF.

### <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim. Para obter mais informações, consulte [Personalizar grupos e regras waf](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quais regras estão disponíveis atualmente para waf?

Atualmente, o WAF suporta CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)e [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Essas regras fornecem segurança de linha de base contra a maioria das 10 vulnerabilidades top-10 que o OWASP (Open Web Application Security Project, projeto de segurança de aplicativos da Web) identifica: 

* Proteção contra injeção de SQL
* Proteção de scripts entre sites
* Proteção contra ataques comuns na Web, como injeção de comando, contrabando de solicitação HTTP, divisão de resposta HTTP e ataque remoto de inclusão de arquivos
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação
* Prevenção contra bots, rastreadores e scanners
* Detecção de configurações erradas comuns de aplicativos (isto é, Apache, IIS e assim por diante)

Para obter mais informações, consulte [as vulnerabilidades top-10 do OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>O WAF suporta proteção DDoS?

Sim. Você pode ativar a proteção DDoS na rede virtual onde o gateway de aplicativo é implantado. Essa configuração garante que o serviço de proteção Azure DDoS também proteja o IP virtual (VIP) do gateway de aplicativo.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe alguma orientação para migrar do V1 SKU para o V2 SKU?

Sim. Para obter detalhes, [migrate Azure Application Gateway e Web Application Firewall de v1 para v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Configuração - controlador de entrada para AKS

### <a name="what-is-an-ingress-controller"></a>O que é um Controlador Ingress?

Kubernetes permite `deployment` a `service` criação e o recurso para expor um grupo de pods internamente no cluster. Para expor o mesmo serviço [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) externamente, é definido um recurso que fornece balanceamento de carga, rescisão ssl e hospedagem virtual baseada em nomes.
Para satisfazer `Ingress` esse recurso, é necessário um Controlador de Ingress que ouça quaisquer alterações nos `Ingress` recursos e configure as políticas de balanceador de carga.

O Application Gateway Ingress Controller permite que [o Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) seja usado como entrada de um Serviço [Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) também conhecido como cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Uma única instância do controlador de entrada pode gerenciar vários Gateways de aplicativos?

Atualmente, uma instância do Ingress Controller só pode ser associada a um Gateway de aplicativo.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipos de logs o Application Gateway fornece?

O Application Gateway fornece três logs: 

* **ApplicationGatewayAccessLog**: O log de acesso contém cada solicitação enviada ao frontend do gateway do aplicativo. Os dados incluem ip do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora. O registro de acesso é coletado a cada 300 segundos. Ele contém um registro por gateway de aplicativo.
* **ApplicationGatewayPerformanceLog**: O registro de desempenho captura informações de desempenho para cada gateway de aplicativo. As informações incluem o throughput in bytes, o total de solicitações atendidas, a contagem de solicitações falhadas e a contagem de instâncias de backend saudáveis e insalubres.
* **AplicativoGatewayFirewallLog**: Para gateways de aplicativos configurados com waf, o registro de firewall contém solicitações que são registradas através do modo de detecção ou do modo de prevenção.

Para obter mais informações, consulte [a saúde do Backend, os registros de diagnóstico e as métricas do Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se meus membros do pool de back-end são saudáveis?

Verifique a saúde usando o `Get-AzApplicationGatewayBackendHealth` cmdlet PowerShell ou o portal. Para obter mais informações, consulte [os diagnósticos do Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Qual é a política de retenção dos registros de diagnóstico?

Os registros de diagnóstico fluem para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base em sua preferência. Os registros de diagnóstico também podem ser enviados para um hub de eventos ou registros do Monitor Do Azure. Para obter mais informações, consulte [os diagnósticos do Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter os logs de auditoria para o Gateway de aplicativo?

No portal, na lâmina de menu de um gateway de aplicativo, selecione **Registro de atividades** para acessar o registro de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Configurar alertas com o Gateway de aplicativo?

Sim. No Application Gateway, os alertas são configurados em métricas. Para obter mais informações, consulte [as métricas do Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e receba [notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como faço para analisar as estatísticas de tráfego do Application Gateway?

Você pode visualizar e analisar registros de acesso de várias maneiras. Use registros do Monitor do Azure, Excel, POWER BI e assim por diante.

Você também pode usar um modelo do Gerenciador de recursos que instala e executa o popular analisador de log do [GoAccess](https://goaccess.io/) para logs de acesso do Application Gateway. O GoAccess fornece estatísticas de tráfego HTTP valiosas, como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores e códigos de status HTTP. Para obter mais informações, no GitHub, consulte o [arquivo Readme na pasta de modelo Seleção de recursos](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia fazer com que a saúde do backend retornasse a um status desconhecido?

Normalmente, você vê um status desconhecido quando o acesso ao backend é bloqueado por um grupo de segurança de rede (NSG), DNS personalizado ou udr definido pelo usuário na sub-rede do gateway do aplicativo. Para obter mais informações, consulte [saúde do Backend, registro de diagnósticos e métricas para Gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Existe algum caso em que os registros de fluxo do NSG não mostrem tráfego permitido?

Sim. Se a configuração corresponder ao cenário seguinte, você não verá tráfego permitido em seus registros de fluxo do NSG:
- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicativo
- Você habilitou os registros de fluxo NSG naquele NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como usar o Gateway de aplicativo V2 com apenas endereço IP frontend privado?

O Gateway de aplicativo V2 atualmente não suporta apenas o modo IP privado. Ele suporta as seguintes combinações
* IP privado e IP público
* Somente IP público

Mas se você quiser usar o Application Gateway V2 apenas com IP privado, você pode acompanhar o processo abaixo:
1. Crie um Gateway de aplicativo com endereço IP frontend público e privado
2. Não crie nenhum ouvinte para o endereço IP do frontend público. O Application Gateway não ouvirá nenhum tráfego no endereço IP público se nenhum ouvinte for criado para ele.
3. Criar e anexar um [Grupo de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede do Gateway de aplicativo com a seguinte configuração na ordem de prioridade:
    
    a. Permitir o tráfego da marca De Origem como tag de serviço **GatewayManager** e destino como **qualquer** e destino porta como **65200-65535**. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por autenticação de certificado. Entidades externas, incluindo os administradores de usuários do Gateway, não podem iniciar alterações nesses pontos finais sem certificados apropriados em vigor
    
    b. Permitir o tráfego da Fonte como tag de serviço **azureLoadBalancer** e porta de destino como **Qualquer**
    
    c. Negar todo o tráfego de entrada da Fonte como tag de serviço da **Internet** e porta de destino como **Qualquer**. Dê a esta regra a *menor prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada do VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade de internet de saída não pode ser bloqueada. Caso contrário, você enfrentará problemas com registro, métricas e assim por diante.

Configuração do SAMPLE NSG ![para acesso somente IP privado: Configuração do Gateway de aplicativo V2 NSG apenas para acesso IP privado](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade do Application Gateway suporta o atributo SameSite?
Sim, a [atualização v80](https://chromiumdash.appspot.com/schedule) [do navegador Chromium](https://www.chromium.org/Home) introduziu um mandato em cookies HTTP sem o atributo SameSite para ser tratado como SameSite=Lax. Isso significa que o cookie de afinidade do Application Gateway não será enviado pelo navegador em um contexto de terceiros. Para suportar esse cenário, o Application Gateway injeta outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *ApplicationGatewayAffinity* existente.  Esses cookies são semelhantes, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados: *SameSite=None; Seguro*. Esses atributos mantêm sessões pegajosas mesmo para solicitações de origem cruzada. Consulte a [seção de afinidade baseada em cookies](configuration-overview.md#cookie-based-affinity) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Application Gateway, consulte [O que é gateway de aplicativos do Azure?](overview.md)
