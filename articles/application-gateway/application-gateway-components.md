---
title: Componentes do gateway de aplicativo
description: Este artigo fornece informações sobre os vários componentes em um gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133102"
---
# <a name="application-gateway-components"></a>Componentes do gateway de aplicativo

 Um gateway de aplicativo serve como o único ponto de contato para os clientes. Ele distribui tráfego de aplicativos de entrada em vários pools de back-end, que incluem VMs do Azure, conjuntos de escala de máquinas virtuais, Serviço de Aplicativos Azure e servidores externos/locais/externos. Para distribuir tráfego, um gateway de aplicativo usa vários componentes descritos neste artigo.

![Os componentes usados em um gateway de aplicativo](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP frontend

Um endereço IP frontend é o endereço IP associado a um gateway de aplicativo. Você pode configurar um gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um gateway de aplicativo suporta um endereço IP público ou privado. Sua rede virtual e endereço IP público devem estar no mesmo local que o gateway do aplicativo. Depois de criado, um endereço IP frontend é associado a um ouvinte.

### <a name="static-versus-dynamic-public-ip-address"></a>Endereço IP público estático versus dinâmico

O Azure Application Gateway V2 SKU pode ser configurado para suportar tanto o endereço IP interno estático quanto o endereço IP público estático, ou apenas endereço IP público estático. Ele não pode ser configurado para suportar apenas endereço IP interno estático.

O V1 SKU pode ser configurado para suportar endereço IP interno estático ou dinâmico e endereço IP público dinâmico. O endereço IP dinâmico do Application Gateway não muda em um gateway em execução. Ele só pode mudar quando você parar ou iniciar o Gateway. Ele não muda em falhas do sistema, atualizações, atualizações do host do Azure etc. 

O nome DNS associado a um gateway de aplicativo não muda ao longo do ciclo de vida do gateway. Como resultado, você deve usar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicativo.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica pedidos de conexão recebidas. Um ouvinte aceita uma solicitação se o protocolo, porta, nome de host e endereço IP associados à solicitação corresponderem aos mesmos elementos associados à configuração do ouvinte.

Antes de usar um gateway de aplicativo, você deve adicionar pelo menos um ouvinte. Pode haver vários ouvintes conectados a um gateway de aplicativo, e eles podem ser usados para o mesmo protocolo.

Depois que um ouvinte detecta solicitações recebidas de clientes, o gateway de aplicativo encaminha essas solicitações para membros no pool de backend configurado na regra.

Os ouvintes suportam as seguintes portas e protocolos.

### <a name="ports"></a>Portas

Uma porta é onde um ouvinte ouve a solicitação do cliente. Você pode configurar portas que variam de 1 a 65502 para o V1 SKU e 1 a 65199 para o V2 SKU.

### <a name="protocols"></a>Protocolos

O Application Gateway suporta quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:
>[!NOTE]
>O suporte ao protocolo HTTP/2 está disponível para os clientes que se conectam apenas os ouvintes do Gateway de Aplicativo. A comunicação para pools de servidores backend é sempre sobre HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. Você pode optar por habilitá-lo.

- Especifique entre os protocolos HTTP e HTTPS na configuração do ouvinte.
- O suporte para [WebSockets e protocolos HTTP/2](features.md#websocket-and-http2-traffic) é fornecido nativamente, e [o suporte ao WebSocket](application-gateway-websocket.md) é ativado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. Use WebSockets com ouvintes HTTP e HTTPS.

Use um ouvinte HTTPS para rescisão TLS. Um ouvinte HTTPS descarrega o trabalho de criptografia e descriptografia no gateway do aplicativo, para que seus servidores web não sejam sobrecarregados pela sobrecarga.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de aplicativo permite criar páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada. O Gateway de aplicativo exibe uma página de erro personalizada quando uma solicitação não pode alcançar o backend.

Para obter mais informações, consulte [páginas de erro personalizadas para o gateway de aplicativo](custom-error.md).

### <a name="types-of-listeners"></a>Tipos de ouvintes

Existem dois tipos de ouvintes:

- **Básico**. Este tipo de ouvinte ouve um único site de domínio, onde ele tem um único mapeamento de DNS para o endereço IP do gateway do aplicativo. Essa configuração de ouvinte é necessária quando você hospeda um único site atrás de um gateway de aplicativo.

- **Vários sites**. Essa configuração de ouvinte é necessária quando você configura mais de um aplicativo web na mesma instância de gateway de aplicativo. Ele permite configurar uma topologia mais eficiente para suas implantações, somando até 100 sites a um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. Por exemplo, três subdomínios, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, apontam para o endereço IP do gateway de aplicativo. Você criaria três ouvintes multisites e configurava cada ouvinte para a respectiva configuração de porta e protocolo.

    Para obter mais informações, consulte [Hospedagem em vários sites](application-gateway-web-app-overview.md).

Depois de criar um ouvinte, você o associa a uma regra de roteamento de solicitação. Esta regra determina como a solicitação recebida no ouvinte deve ser encaminhada para o backend.

O Application Gateway processa os ouvintes na [ordem mostrada](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Solicitar regras de roteamento

Uma regra de roteamento de solicitação é um componente-chave de um gateway de aplicativo porque determina como direcionar o tráfego no ouvinte. A regra vincula o ouvinte, o pool de servidor back-end e as configurações HTTP back-end.

Quando um ouvinte aceita uma solicitação, a regra de roteamento de solicitação encaminha a solicitação para o backend ou redireciona-a para outro lugar. Se a solicitação for encaminhada para o backend, a regra de roteamento de solicitação define qual pool de servidor backend encaminhá-lo. A regra de roteamento de solicitação também determina se os cabeçalhos na solicitação devem ser reescritos. Um ouvinte pode ser anexado a uma regra.

Existem dois tipos de regras de roteamento de solicitações:

- **Básico**. Todas as solicitações no ouvinte associado (por exemplo, blog.contoso.com/*) são encaminhadas para o pool de backend associado usando a configuração HTTP associada.

- **Baseado em caminhos**. Esta regra de roteamento permite que você encaminhe as solicitações no ouvinte associado para um pool de backend específico, com base na URL na solicitação. Se o caminho da URL em uma solicitação corresponder ao padrão de caminho em uma regra baseada em caminho, a regra será que a solicitação é. Ele aplica o padrão de caminho apenas para o caminho url, não para seus parâmetros de consulta. Se o caminho de URL em uma solicitação de ouvinte não corresponder a nenhuma das regras baseadas em caminho, ele encaminha a solicitação para o pool de backup padrão e as configurações HTTP.

Para obter mais informações, consulte [roteamento baseado em URL](url-route-overview.md).

### <a name="redirection-support"></a>Suporte para redirecionamento

A regra de roteamento de solicitação também permite que você redirecione o tráfego no gateway do aplicativo. Este é um mecanismo de redirecionamento genérico, para que você possa redirecionar para e de qualquer porta que você definir usando regras.

Você pode escolher o alvo de redirecionamento para ser outro ouvinte (o que pode ajudar a ativar http automático para redirecionamento HTTPS) ou um site externo. Você também pode optar por que o redirecionamento seja temporário ou permanente, ou anexar o caminho URI e a seqüência de consulta à URL redirecionada.

Para obter mais informações, consulte [Redirecionar o tráfego no gateway de aplicativo](redirect-overview.md).

### <a name="rewrite-http-headers"></a>Reescrever cabeçalhos HTTP

Usando as regras de roteamento de solicitação, você pode adicionar, remover ou atualizar cabeçalhos de solicitação e resposta HTTP(S) à medida que os pacotes de solicitação e resposta se movem entre os pools de cliente e backend através do gateway do aplicativo.

Os cabeçalhos podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis do servidor. Isso ajuda com casos de uso importantes, como extrair endereços IP do cliente, remover informações confidenciais sobre o backend, adicionar mais segurança e assim por diante.

Para obter mais informações, consulte [Reescrever cabeçalhos HTTP no gateway do aplicativo](rewrite-http-headers.md).

## <a name="http-settings"></a>Configurações de HTTP

Um gateway de aplicativo direciona o tráfego para os servidores backend (especificado na regra de roteamento de solicitações que incluem configurações HTTP) usando o número da porta, o protocolo e outras configurações detalhadas neste componente.

A porta e o protocolo usados nas configurações HTTP determinam se o tráfego entre o gateway de aplicativo e os servidores back-end é criptografado (fornecendo TLS de ponta a ponta) ou não criptografado.

Este componente também está acostumado a:

- Determine se uma sessão de usuário deve ser mantida no mesmo servidor usando a [afinidade de sessão baseada em cookies](features.md#session-affinity).

- Remova graciosamente os membros do pool backend usando [a drenagem de conexão](features.md#connection-draining).

- Assione um teste personalizado para monitorar a saúde do backend, defina o intervalo de tempo de tempo de solicitação, anule o nome do host e o caminho na solicitação e forneça facilidade com um clique para especificar as configurações do backend do Serviço de Aplicativos.

## <a name="backend-pools"></a>Piscinas de backend

Um pool de back-end é solicitado aos servidores backend, que atendem à solicitação. As piscinas de backend podem conter:

- NICs
- conjuntos de escala de máquina virtual
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Backends multilocatários (como o Serviço de Aplicativos)

Os membros do pool de back-end do Application Gateway não estão vinculados a um conjunto de disponibilidade. Um gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que está. Como resultado, os membros dos pools de back-end podem estar em clusters, em data centers ou fora do Azure, desde que haja conectividade IP.

Se você usar IPs internos como membros do pool backend, você deve usar [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). O peering de rede virtual é suportado e benéfico para o tráfego de balanceamento de carga em outras redes virtuais.

Um gateway de aplicativo também pode se comunicar com servidores locais quando eles estão conectados por túneis Azure ExpressRoute ou VPN se o tráfego for permitido.

Você pode criar diferentes pools de back-end para diferentes tipos de solicitações. Por exemplo, crie um pool de back-end para solicitações gerais e, em seguida, outro pool de back-end para solicitações aos microserviços para sua aplicação.

## <a name="health-probes"></a>Investigações de integridade

Por padrão, um gateway de aplicativo monitora a saúde de todos os recursos em seu pool de backend e remove automaticamente os insalubres. Em seguida, monitora casos insalubres e os adiciona de volta à piscina de backend saudável quando eles se tornam disponíveis e respondem a sondas de saúde.

Além de usar o monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Sondas personalizadas permitem um controle mais granular sobre o monitoramento da saúde. Ao usar testes personalizados, você pode configurar o intervalo do teste, a URL e o caminho para testar e quantas respostas falhadas a serem aceitas antes que a instância do pool de backend seja marcada como insalubre. Recomendamos que você configure testes personalizados para monitorar a saúde de cada pool de back-end.

Para obter mais informações, consulte [Monitorar a saúde do gateway de aplicativo](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Próximas etapas

Criar um Application Gateway:

* [No portal Azure](quick-create-portal.md)
* [Usando o Azure PowerShell](quick-create-powershell.md)
* [Usando o Azure CLI](quick-create-cli.md)
