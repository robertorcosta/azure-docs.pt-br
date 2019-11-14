---
title: O que é o Gateway de Aplicativo do Azure
description: Saiba como você pode usar um gateway de aplicativo do Azure para gerenciar o tráfego da Web para seu aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: a51276cd54a88c5e566afef0d06673f14b5862cf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572026"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicativo do Azure?

O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. Os balanceadores de carga tradicionais operam na camada de transporte (camada OSI 4 – TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e na porta para um endereço IP de destino e uma porta.

![Gateway de Aplicativo conceitual](media/overview/figure1-720.png)

Com o Gateway de Aplicativo, é possível tomar decisões de roteamento com base em outros atributos de uma solicitação HTTP, como o caminho de URI ou os cabeçalhos de host. Por exemplo, você pode encaminhar o tráfego com base na URL de entrada. Portanto, se `/images` estiver na URL de entrada, você poderá encaminhar o tráfego para um conjunto específico de servidores (conhecido como um pool) configurado para as imagens. Se `/video` estiver na URL, esse tráfego será encaminhado para outro pool otimizado para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Esse tipo de roteamento é conhecido como balanceamento de carga da camada de aplicativo (camada OSI 7). O Gateway de Aplicativo do Azure pode fazer o roteamento baseado em URL e muito mais.

Os seguintes recursos estão incluídos no Gateway de Aplicativo do Azure:

## <a name="secure-sockets-layer-ssltls-termination"></a>Terminação do protocolo SSL/TLS (Secure Sockets Layer)

O gateway de aplicativo dá suporte a terminação SSL/TLS no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Esse recurso permite que os servidores Web fiquem livres da sobrecarga da criptografia e descriptografia dispendiosa. Mas, às vezes, a comunicação não criptografada com os servidores não é uma opção aceitável. Isso pode ocorrer devido a requisitos de segurança, de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para tais aplicativos, o Gateway de Aplicativo dá suporte à criptografia SSL/TLS de ponta a ponta.

## <a name="autoscaling"></a>Dimensionamento automático

Implantações do Gateway de Aplicativo ou do WAF sob o SKU Standard_v2 ou WAF_v2 dão suporte ao dimensionamento automático e podem ser aumentadas ou reduzidas com base na mudança dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Para saber mais sobre os recursos Standard_v2 e WAF_v2 do Gateway de Aplicativo, confira [Dimensionamento automático do SKU v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundância de zona

Um Gateway de Aplicativo ou implantações WAF no SKU Standard_v2 ou WAF_v2 pode abranger várias Zonas de Disponibilidade, que oferecem uma melhor resiliência a falhas e que acabam com a necessidade de provisionar Gateways de Aplicativo separados em cada zona.

## <a name="static-vip"></a>VIP estático

O gateway de aplicativo VIP no SKU Standard_v2 ou WAF_v2 é compatível exclusivamente com o tipo VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado mesmo durante o tempo de vida do Gateway de Aplicativo.

## <a name="web-application-firewall"></a>Firewall do aplicativo Web

O WAF (Firewall do Aplicativo Web) é um serviço que fornece proteção centralizada para seus aplicativos Web contra vulnerabilidades e explorações comuns. O WAF é baseado em regras dos [conjuntos de regras de núcleo do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (somente WAF_v2), 3.0 e 2.2.9. 

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Os ataques de injeção de SQL, os ataques de scripts entre sites, entre outros, são comuns entre essas explorações. Pode ser difícil impedir esses ataques no código do aplicativo e isso pode exigir manutenção, aplicação de patches e monitoramento rigorosos em muitas camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

Para saber mais, confira [O que é o Firewall do Aplicativo Web do Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Controlador de entrada para AKS
O Controlador de entrada do Gateway de Aplicativo (AGIC) permite usar o gateway de aplicativo como entrada para um cluster do [Serviço do Kubernetes do Azure (AKS)](https://azure.microsoft.com/services/kubernetes-service/). 

O controlador de entrada é executado como um pod no cluster do AKS e consome [Recursos de Entrada do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) e os converte em uma configuração do Gateway de Aplicativo que permite ao gateway balancear o tráfego para os pods do Kubernetes. O controlador de entrada oferece suporte apenas ao SKU do Gateway de Aplicativo V2. 

Para saber mais, consulte [Controlador de entrada do Gateway de Aplicativo (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Roteamento baseado em URL

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. Um dos cenários possíveis é encaminhar as solicitações de diferentes tipos de conteúdo para diferentes pools.

Por exemplo, as solicitações de `http://contoso.com/video/*` são encaminhadas para VideoServerPool, e as de `http://contoso.com/images/*` são encaminhadas para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

Para obter mais informações, consulte [roteamento baseado em URL com o Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Hospedagem de vários sites

A hospedagem de vários sites permite que você configure mais de um site na mesma instância de gateway de aplicativo. Esse recurso permite que você configure uma topologia mais eficiente para suas implantações, adicionando até 100 sites a um Gateway de Aplicativo ou 40 para WAF (para desempenho ideal). Cada site pode ser direcionado para seu próprio pool. Por exemplo, o gateway de aplicativo pode fornecer o tráfego para `contoso.com` e `fabrikam.com` de dois pools de servidores chamados ContosoServerPool e FabrikamServerPool.

As solicitações de `http://contoso.com` são encaminhadas para ContosoServerPool, e as de `http://fabrikam.com` são encaminhadas para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio pai podem ser hospedados na mesma implantação de gateway de aplicativo. Exemplos de uso de subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` hospedados em uma implantação de gateway de aplicativo único.

Para obter mais informações, consulte [hospedagem de vários sites com o Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitos aplicativos Web é dar suporte ao redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorra em um caminho criptografado.

No passado, talvez você tenha usado técnicas como a criação de um pool dedicado cujo único propósito é redirecionar as solicitações recebidas em HTTP para HTTPS. O gateway de aplicativo dá suporte à capacidade de redirecionar o tráfego no Gateway de Aplicativo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O suporte ao redirecionamento do Gateway de Aplicativo não está limitado apenas ao redirecionamento de HTTP para HTTPS. Esse é um mecanismo de redirecionamento genérico; portanto, você pode redirecionar bidirecionalmente em qualquer porta definida usando regras. Ele também dá suporte a redirecionamento para um site externo.

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

- Redirecionamento global de uma porta para outra porta no Gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- Redirecionamento baseado em caminho. Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionamento para um site externo.

Para obter mais informações, consulte [tráfego de redirecionamento](https://docs.microsoft.com/azure/application-gateway/redirect-overview) com o Gateway de Aplicativo.

## <a name="session-affinity"></a>Afinidade de sessão

O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o Gateway de Aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário.

## <a name="websocket-and-http2-traffic"></a>Tráfego do WebSocket e HTTP/2

O Gateway de Aplicativo fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket.

Os protocolos WebSocket e HTTP/2 permitem uma comunicação full duplex entre um servidor e um cliente em uma conexão TCP de execução longa. Isso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional, sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. Esses protocolos têm baixa sobrecarga, ao contrário do HTTP, e podem reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização mais eficiente de recursos. Esses protocolos foram projetados para funcionar em portas HTTP tradicionais de 80 e 443.

Para obter mais informações, consulte [suporte ao WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) e [suporte ao HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Descarregamento de conexão

O descarregamento de conexão ajuda você a efetuar a remoção normal de membros do pool de back-end durante atualizações de serviço planejadas. Essa configuração é habilitada por meio da configuração do http de back-end e pode ser aplicada a todos os membros de um pool de back-end durante a criação da regra. Com a configuração habilitada, o Gateway de Aplicativo garante que todas as instâncias de um pool de back-end cujos registros forem cancelados não receberão nenhuma nova solicitação, permitindo que solicitações existentes sejam concluídas dentro de um limite de tempo configurado. Isso se aplica a instâncias de back-end removidas explicitamente do pool de back-end por uma chamada à API e a instâncias de back-end relatadas como não íntegras, conforme determinado por investigações de integridade.

Para obter mais informações, confira a seção Drenagem de Conexão da [Visão geral de configuração do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de Aplicativo permite que você crie páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada.

Para obter mais informações, confira [Erros Personalizados](custom-error.md).

## <a name="rewrite-http-headers"></a>Reescrever cabeçalhos HTTP

Os cabeçalhos HTTP permitem que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta. A reescrita desses cabeçalhos HTTP ajuda você a realizar vários cenários importantes, como:

- Adição de campos de cabeçalho relacionados à segurança, como HSTS/X-XSS-Protection.
- Remoção de campos de cabeçalho de resposta que podem revelar informações confidenciais.
- Remoção de informações de porta de cabeçalhos X-Forwarded-For.

O Gateway de Aplicativo dá suporte à capacidade de adicionar, remover ou atualizar solicitações HTTP e cabeçalhos de resposta, enquanto os pacotes de solicitação e resposta são transferidos entre os pools de cliente e de back-end. Ele também fornece a capacidade de adicionar condições para garantir que os cabeçalhos especificados sejam reescritos somente quando determinadas condições forem atendidas.

Para saber mais, confira [Reescrever cabeçalhos HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Dimensionamento

O SKU Standard_v2 e WAF_v2 do Gateway de Aplicativo pode ser configurado para dimensionamento automático ou para implantações de tamanho fixas. Esses SKUs não oferecem diferentes tamanhos de instância. Para obter mais informações sobre o desempenho e os preços do v2, confira [Dimensionamento automático do SKU v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

No momento, o SKU Standard e WAF do Gateway de Aplicativo é oferecido em três tamanhos: **Pequeno**, **Médio** e **Grande**. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Para obter uma lista completa de limites do gateway de aplicativo, consulte [Limites de serviço do Gateway de Aplicativo](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela a seguir mostra uma produtividade de desempenho médio para cada instância do Gateway de Aplicativo v1 com o descarregamento SSL habilitado:

| Tamanho médio de resposta de página de back-end | Pequena | Média | grande |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Esses valores são valores aproximados para uma produtividade do Gateway de Aplicativo. A produtividade real depende de diversos detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para fornecer de uma página. Para obter números de desempenho exatos, você deve executar seus próprios testes. Esses valores são fornecidos apenas para a orientação do planejamento de capacidade.

## <a name="next-steps"></a>Próximas etapas

Dependendo de seus requisitos e do ambiente, você pode criar um Gateway de Aplicativo de teste usando o portal do Azure, o Azure PowerShell ou a CLI do Azure:

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md)
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure](quick-create-cli.md)
