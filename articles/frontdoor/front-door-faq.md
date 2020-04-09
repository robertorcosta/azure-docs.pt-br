---
title: Porta da Frente do Azure - Perguntas frequentes
description: Esta página fornece respostas para perguntas frequentes sobre o Azure Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 0fe5d245d629c731a47ca5441afd2a3388a22de4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878010"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Perguntas frequentes para a porta da frente do Azure

Este artigo responde a perguntas comuns sobre os recursos e funcionalidades do Azure Front Door. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Suporte da Microsoft:** Para criar uma nova solicitação de suporte, no portal Azure, na guia **Ajuda,** selecione o botão **Ajuda + suporte** e selecione Nova solicitação de **suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

O Azure Front Door é uma ADN (Application Delivery Network, rede de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Ele fornece aceleração dinâmica do local (DSA) juntamente com o balanceamento global de carga com failover quase em tempo real. É um serviço altamente disponível e escalável, que é totalmente gerenciado pelo Azure.

### <a name="what-features-does-azure-front-door-support"></a>Quais características o Azure Front Door suporta?

O Azure Front Door suporta aceleração dinâmica de sites (DSA), descarregamento TLS/SSL e TLS de ponta a ponta, Firewall de aplicativos da Web, afinidade de sessão baseada em cookies, roteamento baseado em caminho de URL, certificados gratuitos e gerenciamento de vários domínios, entre outros. Para obter uma lista completa de recursos suportados, consulte [Visão geral da porta frontal do Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual é a diferença entre o Azure Front Door e o Azure Application Gateway?

Embora ambos os Gateways de Porta Frontal e gateway de aplicativo sejam balanceadores de carga de camada 7 (HTTP/HTTPS), a principal diferença é que o Front Door é um serviço global, enquanto o Application Gateway é um serviço regional. Enquanto a Porta frontal pode carregar o equilíbrio entre suas diferentes unidades de escala/clusters/unidades de carimbo entre regiões, o Application Gateway permite que você carregue o equilíbrio entre suas VMs/contêineres etc. que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando devemos implantar um gateway de aplicativo atrás da porta da frente?

Os principais cenários por que se deve usar o Gateway de aplicativo atrás da Porta da Frente são:

- O Front Door pode executar o balanceamento de carga baseado em caminho apenas em nível global, mas se alguém quiser carregar o tráfego de equilíbrio ainda mais dentro de sua rede virtual (VNET), então eles devem usar o Application Gateway.
- Uma vez que a porta da frente não funciona em um nível de VM/contêiner, então ele não pode fazer a drenagem de conexão. No entanto, o Application Gateway permite que você faça a drenagem de conexão. 
- Com um Gateway de aplicativo atrás do AFD, pode-se alcançar 100% de descarregamento TLS/SSL e direcionar apenas solicitações HTTP em sua rede virtual (VNET).
- Porta da frente e gateway de aplicativo ambos suportam afinidade de sessão. Embora o Front Door possa direcionar o tráfego subseqüente de uma sessão de usuário para o mesmo cluster ou backend em uma determinada região, o Application Gateway pode direcionar o tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos implantar o Azure Load Balancer atrás da porta da frente?

O Azure Front Door precisa de um VIP público ou um nome DNS disponível publicamente para direcionar o tráfego. Implantar um Balanceador de Carga Azure atrás da porta da frente é um caso de uso comum.

### <a name="what-protocols-does-azure-front-door-support"></a>Que protocolos o Azure Front Door suporta?

O Azure Front Door suporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Como o Azure Front Door suporta HTTP/2?

O suporte ao protocolo HTTP/2 está disponível apenas para clientes que se conectam ao Azure Front Door. A comunicação para backends no pool de backend é sobre HTTP/1.1. O suporte HTTP/2 é ativado por padrão.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quais recursos têm suporte atualmente como parte do pool de back-end?

Os pools de backend podem ser compostos por armazenamento, web app, instâncias do Kubernetes ou qualquer outro nome de host personalizado que tenha conectividade pública. O Azure Front Door exige que os backends sejam definidos por meio de um IP público ou de um nome de host DNS publicamente solucionável. Os membros de pools de back-end podem estar em regiões, regiões ou até mesmo fora do Azure, desde que tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em quais regiões o serviço está disponível?

O Azure Front Door é um serviço global e não está vinculado a nenhuma região específica do Azure. O único local que você precisa especificar ao criar uma Porta da Frente é o local do grupo de recursos, que está basicamente especificando onde os metadados para o grupo de recursos serão armazenados. O recurso Front Door em si é criado como um recurso global e a configuração é implantada globalmente em todos os POPs (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quais são os locais POP para Azure Front Door?

O Azure Front Door tem a mesma lista de locais POP (Point of Presence) que o Azure CDN da Microsoft. Para obter a lista completa de nossos POPs, consulte os [locais do Azure CDN POP da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>O Azure Front Door é uma implantação dedicada ao meu aplicativo ou é compartilhado entre os clientes?

O Azure Front Door é um serviço multi-inquilino distribuído globalmente. Assim, a infra-estrutura do Front Door é compartilhada entre todos os seus clientes. No entanto, ao criar um perfil da Porta da Frente, você define a configuração específica necessária para o seu aplicativo e nenhuma alteração feita na porta da frente impacta outras configurações da Porta da Frente.

### <a name="is-http-https-redirection-supported"></a>O redirecionamento HTTP-> HTTPS é suportado?

Sim. Na verdade, o Azure Front Door suporta o redirecionamento de strings host, path e consulta, bem como parte do redirecionamento de URL. Saiba mais sobre [o redirecionamento de URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Em que ordem as regras de roteamento são processadas?

As rotas para a porta da frente não são ordenadas e uma rota específica é selecionada com base na melhor combinação. Saiba mais sobre [como o Front Door corresponde às solicitações de uma regra de roteamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Como eu tranco o acesso ao meu backend apenas a porta da frente do Azure?

Para bloquear seu aplicativo para aceitar o tráfego apenas a partir de sua porta da frente específica, você precisará configurar ACLs IP para o seu backend e, em seguida, restringir o tráfego em seu backend ao valor específico do cabeçalho 'X-Azure-FDID' enviado pela Porta da Frente. Estas etapas são detalhadas conforme abaixo:

- Configure o IP ACLing para que seus backends aceitem o tráfego do espaço de endereço IP backend do Azure Front Door e apenas os serviços de infra-estrutura do Azure. Consulte os detalhes de IP abaixo para acling seu backend:
 
    - Consulte a seção *AzureFrontDoor.Backend* em [Faixas IP do Azure e tags de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para o intervalo de endereços IP iPv4 da porta da frente ou você também pode usar a tag de serviço *AzureFrontDoor.Backend* em seus [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de rede ou com [o Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags).
    - O espaço IP de backend **IPv6** da Porta da Frente, enquanto estiver coberto na tag de serviço, não está listado no arquivo JSON das faixas de IP do Azure. Se você está procurando por um intervalo de endereços IPv6 explícito, ele está atualmente limitado a`2a01:111:2050::/44`
    - Os serviços básicos de [infra-estrutura](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) do `168.63.129.16` Azure através de endereços IP de host virtualizados: e`169.254.169.254`

    > [!WARNING]
    > O espaço IP backend da Front Door pode mudar mais tarde, no entanto, vamos garantir que antes disso acontecer, que teríamos integrado com [faixas IP do Azure e tags de serviço](https://www.microsoft.com/download/details.aspx?id=56519). Recomendamos que você assine as [faixas de IP do Azure e as tags de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações.

-    Execute uma operação GET na porta da `2020-01-01` frente com a versão da API ou superior. Na chamada da API, procure por `frontdoorID` campo. Filtrar o cabeçalho de entrada '**X-Azure-FDID**' enviado pela Porta `frontdoorID`da Frente para o seu backend com o valor do campo . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP de qualquer elenco pode mudar ao longo da vida da minha Porta da Frente?

O IP de frontend anycast para a porta da frente normalmente não deve mudar e pode permanecer estático durante toda a vida útil da Porta da Frente. No entanto, não há **garantias** para o mesmo. Por gentileza, não tome nenhuma dependência direta do IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>O Azure Front Door suporta IPs estáticos ou dedicados?

Não, o Azure Front Door atualmente não suporta IPs de frontend estática ou dedicado. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>A Porta Frontal do Azure suporta cabeçalhos x-encaminhados?

Sim, a porta frontal do Azure suporta os cabeçalhos X-Forwarded-For, X-Forwarded-Host e X-Forwarded-Proto. Para X-Forwarded-For se o cabeçalho já estivesse presente, então a Porta da Frente anexa o IP do soquete do cliente a ele. Caso outra coisa, ele adiciona o cabeçalho com o IP do soquete do cliente como o valor. Para X-Forwarded-Host e X-Forwarded-Proto, o valor é substituído.

Saiba mais sobre os [cabeçalhos HTTP suportados pela Porta da Frente.](front-door-http-headers-protocol.md)  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo leva para implantar uma porta da frente do Azure? Minha porta da frente ainda funciona quando atualizada?

Uma nova criação do Front Door ou qualquer atualização para uma porta frontal existente leva cerca de 3 a 5 minutos para a implantação global. Isso significa que em cerca de 3 a 5 minutos, sua configuração de Porta Frontal será implantada em todos os nossos POPs globalmente.

Nota - As atualizações personalizadas do certificado TLS/SSL levam cerca de 30 minutos para serem implantadas globalmente.

Quaisquer atualizações para rotas ou pools de backend, etc. são perfeitas e causarão zero tempo de inatividade (se a nova configuração estiver correta). As atualizações de certificados também são atômicas e não causarão nenhuma paralisação, a menos que mudem de 'AFD Managed' para 'Use seu próprio cert' ou vice-versa.


## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>O Azure Front Door pode equilibrar ou direcionar o tráfego dentro de uma rede virtual?

O Azure Front Door (AFD) requer um IP público ou um nome DNS publicamente solucionável para direcionar o tráfego. Portanto, a resposta é que nenhum AFD não pode direcionar diretamente dentro de uma rede virtual, mas usar um Gateway de aplicativo ou Balancer de Carga Azure no meio resolverá esse cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quais são os vários intervalos e limites para a Porta da Frente do Azure?

Saiba mais sobre todos os [intervalos e limites documentados para a porta da frente do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Como o Azure Front Door suporta alta disponibilidade e escalabilidade?

O Azure Front Door é uma plataforma multi-locatário distribuída globalmente com enormes volumes de capacidade para atender às necessidades de escalabilidade do seu aplicativo. Entregue a partir da borda da rede global da Microsoft, o Front Door oferece um recurso global de balanceamento de carga que permite que você falhe em todo o seu aplicativo ou mesmo microsserviços individuais em regiões ou nuvens diferentes.

## <a name="tls-configuration"></a>Configuração TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Quais versões TLS são suportadas pelo Azure Front Door?

Todos os perfis do Front Door criados após setembro de 2019 usam o TLS 1.2 como o mínimo padrão.

Front Door suporta versões TLS 1.0, 1.1 e 1.2. O TLS 1.3 ainda não tem suporte.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Quais certificados são suportados na Porta da Frente do Azure?

Para habilitar o protocolo HTTPS para fornecer conteúdo com segurança em um domínio personalizado da Porta da Frente, você pode optar por usar um certificado gerenciado pelo Azure Front Door ou usar seu próprio certificado.
A opção de porta da frente gerencia da porta da frente fornece um certificado TLS/SSL padrão via Digicert e armazenado no Cofre chave da porta da frente. Se você optar por usar seu próprio certificado, então você pode embarcar em um certificado de um CA suportado e pode ser um TLS padrão, certificado de validação estendida ou até mesmo um certificado curinga. Os certificados auto-assinados não são suportados. [Saiba como ativar https para um domínio personalizado](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>A Porta da Frente suporta a rotação automática de certificados?

Para a opção de certificado gerenciado front door, os certificados são auto-girados pela Porta da Frente. Se você estiver usando um certificado gerenciado da Porta da Frente e ver se a data de validade do certificado está a menos de 60 dias de distância, faça um pedido de suporte.
</br>Para seu próprio certificado TLS/SSL personalizado, a rotação automática não é suportada. Semelhante à forma como ele foi configurado pela primeira vez para um determinado domínio personalizado, você precisará apontar a Porta da Frente para a versão certa do certificado em seu Cofre de Chaves e garantir que o principal de serviço para porta da frente ainda tenha acesso ao Cofre de Chaves. Esta operação de implantação de certificado atualizado pela Porta da Frente é atômica e não causa nenhum impacto na produção, desde que o nome do assunto ou SAN para o certificado não mude.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quais são as suítes de cifras atuais suportadas pelo Azure Front Door?

A seguir estão as suítes de cifras atuais suportadas pelo Azure Front Door:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política TLS para controlar versões do Protocolo TLS?

Você pode configurar uma versão TLS mínima no Azure Front Door nas configurações HTTPS de domínio personalizado via portal Azure ou a [API Azure REST](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Atualmente, você pode escolher entre 1.0 e 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a Porta da Frente para suportar apenas suítes de cifras específicas?

Não, a configuração da Porta da Frente para suítes específicas de cifras não é suportada. No entanto, você pode obter seu próprio certificado TLS/SSL personalizado da sua Autoridade de Certificado (digamos Verisign, Entrust ou Digicert) e ter suítes de cifras específicas marcadas no certificado quando você o tiver gerado. 

### <a name="does-front-door-support-ocsp-stapling"></a>A Porta da Frente suporta grampeamento OCSP?

Sim, o grampeamento OCSP é suportado por padrão pela Porta da Frente e nenhuma configuração é necessária.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>O Azure Front Door também suporta a recriptografia do tráfego para o backend?

Sim, o Azure Front Door suporta descarregamento TLS/SSL e tLS de ponta a ponta, que recriptografa o tráfego para o backend. Na verdade, uma vez que as conexões para o backend acontecem sobre seu IP público, é recomendável que você configure sua Porta frontal para usar HTTPS como protocolo de encaminhamento.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>O Front Door suporta certificados auto-assinados no backend para conexão HTTPS?

Não, os certificados auto-assinados não são suportados no Front Door e a restrição se aplica a ambos:

1. **Backends**: Você não pode usar certificados auto-assinados quando estiver encaminhando o tráfego como testes de saúde HTTPS ou HTTPS ou preenchendo o cache da origem para regras de roteamento com o cache ativado.
2. **Frontend**: Você não pode usar certificados auto-assinados ao usar seu próprio certificado TLS/SSL personalizado para habilitar HTTPS em seu domínio personalizado.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Por que o tráfego HTTPS para o meu backend está falhando?

Para ter conexões HTTPS bem-sucedidas no seu backend, seja para testes de saúde ou para solicitações de encaminhamento, pode haver duas razões pelas quais o tráfego HTTPS pode falhar:

1. **Incompatibilidade de nome do assunto do certificado**: Para conexões HTTPS, o Front Door espera que seu backend apresente certificado de um CA válido com nome de assunto correspondente ao nome de host do backend. Como exemplo, se o nome de `myapp-centralus.contosonews.net` host do backend estiver definido e o certificado `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` que seu backend apresenta durante o aperto de mão TLS nem tem nem no nome do objeto, a Porta da Frente recusará a conexão e resultará em um erro. 
    1. **Solução**: Embora não seja recomendado do ponto de vista da conformidade, você pode contornar esse erro desabilitando a verificação do nome do assunto do certificado para a porta da frente. Isso está presente em Configurações no portal Azure e em BackendPoolsSettings na API.
2. **Certificado de hospedagem backend da CA inválida**: Somente certificados de [CAs válidos](/azure/frontdoor/front-door-troubleshoot-allowed-ca) podem ser usados no backend com front door. Não são permitidos certificados de CAs internos ou certificados auto-assinados.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Que tipos de métricas e registros estão disponíveis com o Azure Front Door?

Para obter informações sobre logs e outros recursos de diagnóstico, consulte [métricas de monitoramento e registros para porta da frente](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção nos logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento de clientes, e os clientes podem definir a política de retenção com base em suas preferências. Os logs de diagnóstico também podem ser enviados para o Hub de Eventos ou os logs do Azure Monitor. Para obter mais informações, consulte [Azure Front Door Diagnostics](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Como faço para obter registros de auditoria para a porta da frente do Azure?

Os registros de auditoria estão disponíveis para a Porta da Frente do Azure. No portal, clique em Entrar em **atividade** na lâmina do menu da porta da frente para acessar o registro de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Posso colocar alertas com a porta da frente do Azure?

Sim, a Porta da Frente do Azure suporta alertas. Os alertas são configurados nas métricas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
