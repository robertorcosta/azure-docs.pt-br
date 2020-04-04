---
title: Visão geral da configuração do Azure Application Gateway
description: Este artigo descreve como configurar os componentes do Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 89d894a5125a16f95e6ef8a15c2503d48f3a8e55
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632185"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do Gateway de aplicativos

O Azure Application Gateway consiste em vários componentes que você pode configurar de várias maneiras para diferentes cenários. Este artigo mostra como configurar cada componente.

![Gráfico de fluxo de componentes do Application Gateway](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra um aplicativo que tem três ouvintes. Os dois primeiros são ouvintes multi-site para `http://acme.com/*` e, `http://fabrikam.com/*`respectivamente. Ambos escutam no porto 80. O terceiro é um ouvinte básico que tem rescisão tls (Transport Layer Security, segurança de camada de transporte) de ponta a ponta, anteriormente conhecida como terminação SSL (Secure Sockets Layer).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual azure e sub-rede dedicada

Um gateway de aplicativo é uma implantação dedicada em sua rede virtual. Dentro de sua rede virtual, uma sub-rede dedicada é necessária para o gateway de aplicativo. Você pode ter várias instâncias de uma determinada implantação de gateway de aplicativo em uma sub-rede. Você também pode implantar outros gateways de aplicativo na sub-rede. Mas você não pode implantar qualquer outro recurso na sub-rede do gateway de aplicativo.

> [!NOTE]
> Você não pode misturar Standard_v2 e Standard Azure Application Gateway na mesma sub-rede.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O Application Gateway usa um endereço IP privado por instância, além de outro endereço IP privado se um IP front-end privado estiver configurado.

O Azure também reserva cinco endereços IP em cada sub-rede para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicativo sem IP front-end privado. Você precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias de gateway do aplicativo. Então, você precisa de um tamanho de sub-rede /27 ou maior.

Considere uma sub-rede que tenha 27 instâncias de gateway de aplicativo e um endereço IP para um IP front-end privado. Neste caso, você precisa de 33 endereços IP: 27 para as instâncias de gateway do aplicativo, um para o front-end privado e cinco para uso interno. Então, você precisa de um tamanho de sub-rede /26 ou maior.

Recomendamos que você use um tamanho de sub-rede de pelo menos /28. Este tamanho oferece 11 endereços IP utilizáveis. Se a carga do aplicativo exigir mais de 10 instâncias do Application Gateway, considere um tamanho de sub-rede de /27 ou /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na sub-rede do Gateway de aplicativos

Os NSGs (Network Security Groups, grupos de segurança de rede) são suportados no Application Gateway. Mas há algumas restrições:

- Você deve permitir o tráfego de Internet de entrada nas portas TCP 65503-65534 para o Application Gateway v1 SKU, e as portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como **Any** e origem como tag de serviço **GatewayManager.** Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por certificados Azure. Entidades externas, incluindo os clientes desses gateways, não podem se comunicar nesses pontos finais.

- A conectividade de internet de saída não pode ser bloqueada. As regras de saída padrão no NSG permitem a conectividade com a internet. É recomendável que você:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que neguem qualquer conectividade de saída.

- O tráfego da tag **AzureLoadBalancer** deve ser permitido.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso do Application Gateway a alguns IPs de origem

Para este cenário, use NSGs na sub-rede Application Gateway. Coloque as seguintes restrições na sub-rede nesta ordem de prioridade:

1. Permitir tráfego de entrada a partir de uma faixa IP ou IP de origem com o destino como toda a faixa de endereço e porta de destino da sub-rede do Application Gateway como sua porta de acesso de entrada, por exemplo, porta 80 para acesso HTTP.
2. Permitir solicitações recebidas de origem como tag de serviço **gatewaymanager** e destino como **Quaisquer** e portas de destino como 65503-65534 para o Gateway de aplicativo v1 SKU, e portas 65200-65535 para v2 SKU para [comunicação de status de saúde back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por certificados Azure. Sem certificados apropriados, as entidades externas não podem iniciar alterações nesses pontos finais.
3. Permitir testes de balanceador de carga azure (tag*AzureLoadBalancer)* e tráfego de rede virtual de entrada *(tag VirtualNetwork)* no [grupo de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)da rede .
4. Bloqueie todos os outros tráfegos de entrada usando uma regra de negação.
5. Permitir tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>As rotas definidas pelo usuário são suportadas na sub-rede do Gateway de Aplicativo

> [!IMPORTANT]
> O uso de UDRs na sub-rede Application Gateway pode fazer com que o estado de saúde na [visão de saúde back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) apareça como **Desconhecido**. Também pode fazer com que a geração de logs e métricas do Application Gateway falhe. Recomendamos que você não use UDRs na sub-rede Application Gateway para que você possa visualizar a saúde, logs e métricas de back-end.

- **v1**

   Para o SKU v1, as rotas definidas pelo usuário (UDRs) são suportadas na sub-rede do Application Gateway, desde que não alterem a comunicação de solicitação/resposta de ponta a ponta. Por exemplo, você pode configurar um UDR na sub-rede Application Gateway para apontar para um aparelho de firewall para inspeção de pacotes. Mas você deve ter certeza de que o pacote pode chegar ao seu destino após a inspeção. Caso contrário, pode resultar em um comportamento incorreto de sonda de saúde ou roteamento de tráfego. Isso inclui rotas aprendidas ou rotas padrão 0.0.0.0/0 que são propagadas por gateways Azure ExpressRoute ou VPN na rede virtual.

- **v2**

   Para o V2 SKU, existem cenários suportados e sem suporte:

   **cenários suportados pela V2**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rota pode resultar em roteamento assimétrico no Gateway de aplicativo v2. Certifique-se de que todo o tráfego de plano de gerenciamento/controle seja enviado diretamente para a Internet e não através de um aparelho virtual. O registro e as métricas também podem ser afetados.


  **Cenário 1**: UDR para desativar a propagação de rota do Border Gateway Protocol (BGP) para a sub-rede do Gateway de aplicativo

   Às vezes, a rota de gateway padrão (0.0.0.0/0) é anunciada através dos gateways ExpressRoute ou VPN associados à rede virtual Application Gateway. Isso quebra o tráfego de aviões de gestão, o que requer um caminho direto para a Internet. Em tais cenários, um UDR pode ser usado para desativar a propagação da rota BGP. 

   Para desativar a propagação da rota BGP, use as seguintes etapas:

   1. Crie um recurso de tabela de rota no Azure.
   2. Desative o parâmetro de propagação de **rota de gateway de rede virtual.** 
   3. Associe a Tabela de Rota à sub-rede apropriada. 

   Habilitar o UDR para este cenário não deve quebrar nenhuma configuração existente.

  **Cenário 2**: UDR para direcionar 0.0.0.0/0 para a Internet

   Você pode criar um UDR para enviar tráfego 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para Azure Kubernetes Service kubenet

  Se você estiver usando kubenet com o Azure Kubernetes Service (AKS) e o Application Gateway Ingress Controller (AGIC), você precisa configurar uma tabela de rota para permitir que o tráfego enviado para os pods seja encaminhado para o nó correto. Isso não será necessário se você usar o Azure CNI. 

   Para configurar a tabela de rota para permitir que o kubenet funcione, use as seguintes etapas:

  1. Crie um recurso de tabela de rota no Azure. 
  2. Uma vez criado, vá para a página **Rotas.** 
  3. Adicionar uma nova rota:
     - O prefixo de endereço deve ser a faixa IP dos pods que você deseja alcançar no AKS. 
     - O próximo tipo de salto deve ser **Virtual Appliance**. 
     - O próximo endereço de salto deve ser o endereço IP do nó que hospeda os pods dentro do intervalo IP definido no campo de prefixo de endereço. 
    
  **v2 cenários sem suporte**

  **Cenário 1**: UDR para Aparelhos Virtuais

  Qualquer cenário em que 0.0.0.0/0 precise ser redirecionado através de qualquer aparelho virtual, uma rede virtual hub/spoke ou on-premise (túnel forçado) não é suportado para V2.

## <a name="front-end-ip"></a>Front-end IP

Você pode configurar o gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um IP público é necessário quando você hospeda um back-end que os clientes devem acessar pela internet através de um IP virtual (VIP) voltado para a Internet. 

Um IP público não é necessário para um ponto final interno que não esteja exposto à internet. Isso é conhecido como um ponto final *de balanceador de carga interno* (ILB) ou IP frontend privado. Um Gateway de aplicativo ILB é útil para aplicativos internos de linha de negócios que não estão expostos à internet. Também é útil para serviços e níveis em um aplicativo de vários níveis dentro de um limite de segurança que não estão expostos à internet, mas que exigem distribuição de carga de round-robin, pegajosa de sessão ou término do TLS.

Apenas 1 endereço IP público ou um endereço IP privado é suportado. Você escolhe o IP front-end ao criar o gateway de aplicativo.

- Para um IP público, você pode criar um novo endereço IP público ou usar um IP público existente no mesmo local que o gateway do aplicativo. Para obter mais informações, consulte [endereço IP público estático vs. dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um IP privado, você pode especificar um endereço IP privado da sub-rede onde o gateway do aplicativo é criado. Se você não especificar um, um endereço IP arbitrário será automaticamente selecionado na sub-rede. O tipo de endereço IP que você seleciona (estático ou dinâmico) não pode ser alterado mais tarde. Para obter mais informações, consulte [Criar um gateway de aplicativo com um balanceador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP front-end está associado a um *ouvinte,* que verifica as solicitações recebidas no IP front-end.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão recebidas usando o endereço de porta, protocolo, host e IP. Ao configurar o ouvinte, você deve inserir valores para estes que correspondem aos valores correspondentes na solicitação de entrada no gateway.

Quando você cria um gateway de aplicativo usando o portal Azure, você também cria um ouvinte padrão escolhendo o protocolo e a porta para o ouvinte. Você pode escolher se deseja ativar o suporte HTTP2 no ouvinte. Depois de criar o gateway de aplicativo, você pode editar as configurações desse ouvinte padrão *(appGatewayHttpListener)* ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Quando você cria um novo ouvinte, você escolhe entre [ *o básico* e *o multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se você quiser que todas as suas solicitações (para qualquer domínio) sejam aceitas e encaminhadas para pools de back-end, escolha o básico. Aprenda [a criar um gateway de aplicativo com um ouvinte básico.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Se você quiser encaminhar solicitações para diferentes pools de back-end com base no cabeçalho *do host* ou no nome do host, escolha o ouvinte de vários sites, onde você também deve especificar um nome de host que corresponde à solicitação recebida. Isso porque o Application Gateway conta com cabeçalhos de host HTTP 1.1 para hospedar mais de um site no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento de ouvintes

Para o V1 SKU, as solicitações são correspondidas de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com ouvinte básico vier primeiro na ordem, ela será processada primeiro e aceitará qualquer solicitação para essa combinação de porta e IP. Para evitar isso, configure as regras com ouvintes de vários sites primeiro e empurre a regra com o ouvinte básico para o último da lista.

Para o V2 SKU, os ouvintes multi-site são processados antes dos ouvintes básicos.

### <a name="front-end-ip"></a>Front-end IP

Escolha o endereço IP front-end que você pretende associar a este ouvinte. O ouvinte ouvirá as solicitações recebidas neste IP.

### <a name="front-end-port"></a>Porta front-end

Escolha a porta front-end. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor da [faixa permitida de portas](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Você pode usar não apenas portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada permitida que seja adequada. Uma porta pode ser usada para ouvintes voltados para o público ou para ouvintes de frente para o privado.

### <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se você escolher HTTP, o tráfego entre o cliente e o gateway do aplicativo será descriptografado.

- Escolha HTTPS se você quiser [terminação TLS](features.md#secure-sockets-layer-ssltls-termination) ou [criptografia TLS de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). O tráfego entre o cliente e o gateway de aplicativo é criptografado. E a conexão TLS termina no gateway de aplicativo. Se você quiser criptografia TLS de ponta a ponta, você deve escolher HTTPS e configurar a configuração **HTTP back-end.** Isso garante que o tráfego seja recriptografado quando ele viaja do gateway do aplicativo para o back-end.


Para configurar o término do TLS e a criptografia TLS de ponta a ponta, você deve adicionar um certificado ao ouvinte para permitir que o gateway do aplicativo obtenha uma chave simétrica. Isso é ditado pela especificação do protocolo TLS. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado de gateway deve estar no formato PFX (Personal Information Exchange). Esse formato permite exportar a chave privada que o gateway usa para criptografar e descriptografar o tráfego.

#### <a name="supported-certificates"></a>Certificados suportados

Consulte [os certificados suportados para rescisão do TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte adicional ao protocolo

#### <a name="http2-support"></a>Suporte HTTP2

O suporte ao protocolo HTTP/2 está disponível apenas para clientes que se conectam apenas aos ouvintes do gateway de aplicativos. A comunicação aos pools de servidores back-end é via HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. O seguinte trecho de código Azure PowerShell mostra como habilitar isso:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte para WebSocket

O suporte ao WebSocket é ativado por padrão. Não há configuração configurável pelo usuário para habilitá-la ou desativá-la. Você pode usar WebSockets com ouvintes HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Você pode definir erro personalizado em nível global ou ouvinte. Mas a criação de páginas de erro personalizadas de nível global do portal Azure não é suportada no momento. Você pode configurar uma página de erro personalizada para um erro de firewall de aplicativo web 403 ou uma página de manutenção 502 no nível do ouvinte. Você também deve especificar uma URL blob acessível publicamente para o código de status de erro dado. Para obter mais informações, confira [Criar páginas de erro personalizadas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [a configuração Do Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Política TLS

Você pode centralizar o gerenciamento de certificados TLS/SSL e reduzir a sobrecarga de descriptografia de criptografia para uma fazenda de servidor back-end. O manuseio centralizado do TLS também permite que você especifique uma política TLS central adequada aos seus requisitos de segurança. Você pode escolher a política *TLS padrão,* *predefinida*ou *personalizada.*

Você configura a diretiva TLS para controlar versões de protocolo TLS. Você pode configurar um gateway de aplicativo para usar uma versão de protocolo mínimo para apertos de mão TLS de TLS1.0, TLS1.1 e TLS1.2. Por padrão, o SSL 2.0 e o 3.0 estão desativados e não são configuráveis. Para obter mais informações, consulte visão geral da política do Gateway de [aplicativos TLS](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, você o associa a uma regra de roteamento de solicitações. Essa regra determina como as solicitações recebidas no ouvinte são encaminhadas para o back-end.

## <a name="request-routing-rules"></a>Solicitar regras de roteamento

Quando você cria um gateway de aplicativo usando o portal Azure, você cria uma regra padrão *(regra1).* Essa regra vincula o ouvinte padrão *(appGatewayHttpListener)* com o pool back-end padrão *(appGatewayBackendPool)* e as configurações HTTP de back-end padrão *(appGatewayBackendHttpSettings).* Depois de criar o gateway, você pode editar as configurações da regra padrão ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Quando você cria uma regra, você escolhe entre [ *básico* e *baseado em caminhos*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Escolha básico se quiser encaminhar todas as solicitações no ouvinte associado (por exemplo, *blog<i></i>.contoso.com/\*)* para um único pool de back-end.
- Escolha baseado em caminho satisfaz as solicitações de caminhos específicos de URL para pools de back-end específicos. O padrão de caminho é aplicado apenas ao caminho da URL, não aos parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem de regras de processamento

Para o SKU v1, a correspondência de padrões das solicitações recebidas é processada na ordem de que os caminhos estão listados no mapa de caminho de URL da regra baseada em caminho. Se uma solicitação corresponder ao padrão em dois ou mais caminhos no mapa de caminho, o caminho listado primeiro será correspondido. E o pedido é encaminhado para o back-end que está associado a esse caminho.

Para o V2 SKU, uma correspondência exata é maior prioridade do que a ordem de caminho no mapa de caminho url. Se uma solicitação corresponder ao padrão em dois ou mais caminhos, a solicitação é encaminhada para o back-end que está associado ao caminho que corresponde exatamente à solicitação. Se o caminho na solicitação recebida não corresponder exatamente a qualquer caminho no mapa, a correspondência de padrões da solicitação será processada na lista de pedidos do mapa de caminho para a regra baseada no caminho.

### <a name="associated-listener"></a>Ouvinte associado

Associar um ouvinte à regra para que a *regra de roteamento de solicitação* associada ao ouvinte seja avaliada para determinar o pool de back-end para a qual a solicitação.

### <a name="associated-back-end-pool"></a>Piscina back-end associada

Associar à regra o pool back-end que contém os alvos back-end que servem solicitações que o ouvinte recebe.

 - Para uma regra básica, apenas um pool back-end é permitido. Todas as solicitações no ouvinte associado são encaminhadas para esse pool back-end.

 - Para uma regra baseada em caminho, adicione vários pools de back-end que correspondem a cada caminho de URL. As solicitações que correspondem ao caminho de URL inserido são encaminhadas para o pool de back-end correspondente. Além disso, adicione um pool de back-end padrão. As solicitações que não correspondem a nenhum caminho de URL na regra são encaminhadas para esse pool.

### <a name="associated-back-end-http-setting"></a>Configuração HTTP de back-end associada

Adicione uma configuração HTTP back-end para cada regra. As solicitações são roteadas do gateway de aplicativo para os alvos back-end usando o número da porta, o protocolo e outras informações especificadas nesta configuração.

Para uma regra básica, apenas uma configuração HTTP back-end é permitida. Todas as solicitações no ouvinte associado são encaminhadas para os alvos de back-end correspondentes usando esta configuração HTTP.

Para uma regra baseada em caminho, adicione várias configurações HTTP back-end que correspondem a cada caminho de URL. As solicitações que correspondem ao caminho url nesta configuração são encaminhadas para os alvos de back-end correspondentes usando as configurações HTTP que correspondem a cada caminho de URL. Além disso, adicione uma configuração HTTP padrão. As solicitações que não correspondem a nenhum caminho de URL nesta regra são encaminhadas para o pool de back-end padrão usando a configuração HTTP padrão.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento for configurado para uma regra básica, todas as solicitações no ouvinte associado serão redirecionadas para o destino. Isso é redirecionamento *global.* Se o redirecionamento for configurado para uma regra baseada em caminho, apenas as solicitações em uma área específica do local serão redirecionadas. Um exemplo é uma área de carrinho de compras que é denotada por */cart/\**. Isso é um redirecionamento *baseado em caminhos.*

Para obter mais informações sobre redirecionamentos, consulte [visão geral do redirecionamento do Gateway de aplicativos](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de redirecionamento necessário: *Permanente(301)*, *Temporário(307)*, *Encontrado(302)* ou *Ver outro(303)*.

#### <a name="redirection-target"></a>Alvo de redirecionamento

Escolha outro ouvinte ou um site externo como alvo de redirecionamento.

##### <a name="listener"></a>Ouvinte

Escolha o ouvinte como alvo de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Essa configuração é necessária quando você deseja ativar o redirecionamento HTTP-to-HTTPS. Ele redireciona o tráfego do ouvinte de origem que verifica as solicitações HTTP recebidas para o ouvinte de destino que verifica as solicitações HTTPS recebidas. Você também pode optar por incluir a seqüência de consulta e o caminho da solicitação original na solicitação encaminhada para o destino de redirecionamento.

![Caixa de diálogo de componentes do Application Gateway](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre o redirecionamento HTTP-to-HTTPS, consulte:
- [Redirecionamento HTTP-to-HTTPS usando o portal Azure](redirect-http-to-https-portal.md)
- [Redirecionamento HTTP-para-HTTPS usando powershell](redirect-http-to-https-powershell.md)
- [Redirecionamento HTTP-to-HTTPS usando o Cli Do Zure](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Site externo

Escolha o site externo quando quiser redirecionar o tráfego no ouvinte associado a esta regra para um site externo. Você pode optar por incluir a seqüência de consulta da solicitação original na solicitação encaminhada para o destino de redirecionamento. Você não pode encaminhar o caminho para o site externo que estava na solicitação original.

Para obter mais informações sobre o redirecionamento, consulte:
- [Redirecione o tráfego para um site externo usando o PowerShell](redirect-external-site-powershell.md)
- [Redirecione o tráfego para um site externo usando o CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Reescrever a configuração de cabeçalho HTTP

Essa configuração adiciona, remove ou atualiza cabeçalhos de solicitação e resposta HTTP enquanto os pacotes de solicitação e resposta se movem entre os pools cliente e back-end. Para obter mais informações, consulte:

 - [Reescrever a visão geral dos cabeçalhos HTTP](rewrite-http-headers.md)
 - [Configurar a reescrita do cabeçalho HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Configurações de HTTP

O gateway de aplicativo direciona o tráfego para os servidores back-end usando a configuração que você especifica aqui. Depois de criar uma configuração HTTP, você deve associá-la a uma ou mais regras de roteamento de solicitação.

### <a name="cookie-based-affinity"></a>Afinidade baseada em cookie

O Azure Application Gateway usa cookies gerenciados por gateway para manter as sessões do usuário. Quando um usuário envia a primeira solicitação para o Application Gateway, ele define um cookie de afinidade na resposta com um valor de hash que contém os detalhes da sessão, de modo que as solicitações subseqüentes que carregam o cookie de afinidade serão encaminhadas para o mesmo servidor backend para manter a pegajosa. 

Esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo servidor e quando o estado de sessão é salvo localmente no servidor para uma sessão de usuário. Se o aplicativo não puder lidar com a afinidade baseada em cookies, você não poderá usar esse recurso. Para usá-lo, certifique-se de que os clientes suportem cookies.

A [atualização v80](https://chromiumdash.appspot.com/schedule) [do navegador Chromium](https://www.chromium.org/Home) trouxe um mandato onde cookies HTTP sem atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) devem ser tratados como SameSite=Lax. No caso das solicitações do CORS (Cross-Origin Resource Sharing), se o cookie tiver que ser enviado em um contexto de terceiros, ele deve usar *o SameSite=None; Proteja* atributos e ele deve ser enviado apenas por HTTPS. Caso contrário, em um cenário somente HTTP, o navegador não envia os cookies no contexto de terceiros. O objetivo desta atualização do Chrome é aumentar a segurança e evitar ataques de CSRF (Cross-Site Request Forgery). 

Para suportar essa mudança, a partir de 17 de fevereiro de 2020, o Application Gateway (todos os tipos de SKU) injetará outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *ApplicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados (*"SameSite=None; Seguro"*) para que a sessão pegajosa seja mantida mesmo para solicitações de origem cruzada.

Observe que o nome padrão do cookie de afinidade é *ApplicationGatewayAffinity* e você pode alterá-lo. No caso de você estar usando um nome de cookie de afinidade personalizado, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite=None* estiver definido, é obrigatório que o cookie também contenha o sinalizador *Secure* e seja enviado por HTTPS.  Se for necessária afinidade de sessão sobre o CORS, você deve migrar sua carga de trabalho para HTTPS. Consulte a documentação TLS offload e TLS de ponta a ponta para gateway de aplicativo aqui – [Visão geral,](ssl-overview.md) [Configure um gateway de aplicativo com terminação TLS usando o portal Azure,](create-ssl-portal.md) [Configure TLS de ponta a ponta usando o Gateway de aplicativo com o portal](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Descarregamento de conexão

A drenagem de conexões ajuda você a remover graciosamente os membros do pool back-end durante as atualizações planejadas do serviço. Você pode aplicar essa configuração a todos os membros de um pool de back-end durante a criação de regras. Ele garante que todas as instâncias de descadastramento de um pool de back-end continuem a manter conexões existentes e atendam a solicitações contínuas para um tempo hábil configurável e não recebam novas solicitações ou conexões. A única exceção a isso são as solicitações destinadas a instâncias de descadastramento por causa da afinidade de sessão gerenciada por gateway e continuarão a ser encaminhadas para as instâncias de descadastramento. A drenagem de conexões se aplica a instâncias de back-end que são explicitamente removidas do pool back-end.

### <a name="protocol"></a>Protocolo

O Application Gateway suporta HTTP e HTTPS para solicitações de roteamento para os servidores back-end. Se você escolher HTTP, o tráfego para os servidores back-end será descriptografado. Se a comunicação não criptografada não for aceitável, escolha HTTPS.

Esta configuração combinada com HTTPS no ouvinte suporta [TLS de ponta a ponta](ssl-overview.md). Isso permite que você transmita com segurança dados confidenciais criptografados para o back-end. Cada servidor back-end no pool back-end que tenha TLS de ponta a ponta ativado deve ser configurado com um certificado para permitir uma comunicação segura.

### <a name="port"></a>Porta

Essa configuração especifica a porta onde os servidores back-end ouvem o tráfego a partir do gateway de aplicativo. Você pode configurar portas que variam de 1 a 65535.

### <a name="request-timeout"></a>Tempo limite da solicitação

Esta configuração é o número de segundos que o gateway de aplicativo espera para receber uma resposta do servidor back-end.

### <a name="override-back-end-path"></a>Substituir o caminho de back-end

Esta configuração permite configurar um caminho de encaminhamento personalizado opcional para usar quando a solicitação for encaminhada para o back-end. Qualquer parte do caminho de entrada que corresponda ao caminho personalizado no campo de **caminho de backup de substituição** é copiada para o caminho encaminhado. A tabela a seguir mostra como esse recurso funciona:

- Quando a configuração HTTP estiver anexada a uma regra básica de roteamento de solicitações:

  | Pedido original  | Substituir o caminho de back-end | Pedido encaminhado para back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /casa/            | /substituição/            | /override/home/              |
  | /home/secondhome/ | /substituição/            | /override/home/secondhome/   |

- Quando a configuração HTTP estiver anexada a uma regra de roteamento de solicitação baseada em caminho:

  | Pedido original           | Regra do caminho       | Substituir o caminho de back-end | Pedido encaminhado para back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /substituição/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /substituição/            | /override/home/secondhome/   |
  | /casa/                     | /pathrule*      | /substituição/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /substituição/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /substituição/            | /substituição/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /substituição/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /substituição/            | /substituição/                   |

### <a name="use-for-app-service"></a>Use para serviço de aplicativo

Este é um atalho somente de UI que seleciona as duas configurações necessárias para o back-end do Azure App Service. Ele habilita **escolher o nome do host a partir do endereço back-end**e cria um novo teste personalizado se você ainda não tiver um. (Para obter mais informações, consulte o nome de escolher o host na seção de configuração de [endereço back-end](#pick) deste artigo.) Um novo teste é criado e o cabeçalho do teste é escolhido no endereço do membro back-end.

### <a name="use-custom-probe"></a>Use sonda personalizada

Esta configuração associa um [teste personalizado](application-gateway-probe-overview.md#custom-health-probe) a uma configuração HTTP. Você pode associar apenas um teste personalizado a uma configuração HTTP. Se você não associar explicitamente um teste personalizado, o [teste padrão](application-gateway-probe-overview.md#default-health-probe-settings) será usado para monitorar a saúde do back-end. Recomendamos que você crie uma sonda personalizada para maior controle sobre o monitoramento de saúde de suas extremidades traseiras.

> [!NOTE]
> O teste personalizado não monitora a saúde do pool back-end, a menos que a configuração HTTP correspondente esteja explicitamente associada a um ouvinte.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Escolha o nome do host no endereço back-end

Esse recurso define dinamicamente o cabeçalho *do host* na solicitação para o nome de host do pool back-end. Ele usa um endereço IP ou FQDN.

Esse recurso ajuda quando o nome de domínio do back-end é diferente do nome DNS do gateway de aplicativo, e o back-end depende de um cabeçalho de host específico para resolver até o ponto final correto.

Um exemplo são os serviços de vários inquilinos como back-end. Um serviço de aplicativo é um serviço multi-inquilino que usa um espaço compartilhado com um único endereço IP. Assim, um serviço de aplicativo só pode ser acessado através dos nomes de host que estão configurados nas configurações de domínio personalizados.

Por padrão, o nome de domínio personalizado é *example.azurewebsites.net*. Para acessar o serviço do aplicativo usando um gateway de aplicativo através de um nome de host que não está explicitamente registrado no serviço do aplicativo ou através do FQDN do gateway do aplicativo, você anula o nome de host na solicitação original ao nome de host do serviço de aplicativo. Para fazer isso, habilite o nome de host de seleção na configuração **de endereço back-end.**

Para um domínio personalizado cujo nome de DNS personalizado existente é mapeado para o serviço do aplicativo, você não precisa ativar essa configuração.

> [!NOTE]
> Essa configuração não é necessária para o App Service Environment, que é uma implantação dedicada.

### <a name="host-name-override"></a>Substituição do nome do host

Esse recurso substitui o cabeçalho *host* na solicitação de entrada no gateway do aplicativo pelo nome do host que você especificar.

Por exemplo, se *www.contoso.com* for especificado na configuração`https://appgw.eastus.cloudapp.azure.com/path1` de nome`https://www.contoso.com/path1` **do Host,** a solicitação original * será alterada para * quando a solicitação for encaminhada para o servidor back-end.

## <a name="back-end-pool"></a>Pool de back-end

Você pode apontar um pool back-end para quatro tipos de membros back-end: uma máquina virtual específica, um conjunto de escala de máquina virtual, um endereço IP/FQDN ou um serviço de aplicativo. 

Depois de criar um pool back-end, você deve associá-lo a uma ou mais regras de roteamento de solicitação. Você também deve configurar testes de saúde para cada pool de back-end no gateway de aplicativo. Quando uma condição de regra de roteamento de solicitação é atendida, o gateway de aplicativo encaminha o tráfego para os servidores saudáveis (conforme determinado pelos testes de saúde) no pool de back-end correspondente.

## <a name="health-probes"></a>Investigações de integridade

Um gateway de aplicativo monitora a saúde de todos os recursos em seu back-end por padrão. Mas recomendamos fortemente que você crie um teste personalizado para cada configuração HTTP back-end para obter maior controle sobre o monitoramento de saúde. Para saber como configurar um teste personalizado, consulte [configurações de teste de saúde personalizados](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar um teste de saúde personalizado, você precisa associá-lo a uma configuração HTTP back-end. Um teste personalizado não monitorará a saúde do pool back-end a menos que a configuração HTTP correspondente esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe sobre os componentes do Application Gateway, você pode:

- [Crie um gateway de aplicativo no portal Azure](quick-create-portal.md)
- [Crie um gateway de aplicativo usando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)
