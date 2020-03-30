---
title: O que é o serviço Azure Private Link?
description: Saiba mais sobre o serviço Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280424"
---
# <a name="what-is-azure-private-link-service"></a>O que é o serviço Azure Private Link?

O serviço de Link Privado do Azure é a referência para seu serviço que é desenvolvido com o Link Privado do Azure. Seu serviço que está executando atrás [do Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) pode ser habilitado para acesso ao Private Link para que os consumidores ao seu serviço possam acessá-lo privadamente a partir de seus próprios VNets. Seus clientes podem criar um ponto final privado dentro de seu VNet e mapeá-lo para este serviço. Este artigo explica conceitos relacionados ao lado do prestador de serviços. 

## <a name="workflow"></a>Fluxo de trabalho

![Fluxo de trabalho do serviço Private Link](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Crie seu serviço de link privado

- Configure seu aplicativo para executar atrás de um balanceador de carga padrão em sua rede virtual. Se você já tiver seu aplicativo configurado atrás de um balanceador de carga padrão, você pode pular essa etapa.   
- Crie um serviço de link privado fazendo referência ao balanceador de carga acima. No processo de seleção do balanceador de carga, escolha a configuração de IP frontend onde deseja receber o tráfego. Escolha uma sub-rede para endereços IP NAT para o Serviço de Link Privado. Recomenda-se ter pelo menos oito endereços IP NAT disponíveis na sub-rede. Todo o tráfego de consumidores parecerá ter origem neste pool de endereços IP privados para o provedor de serviços. Escolha as propriedades/configurações apropriadas para o Serviço de Link Privado.    

    > [!NOTE]
    > O Azure Private Link Service só é suportado no Standard Load Balancer. 
    
### <a name="share-your-service"></a>Compartilhe seu serviço

Depois de criar um serviço de Private Link, o Azure gerará um apelido globalmente único chamado "alias" com base no nome que você fornece para o seu serviço. Você pode compartilhar o alias ou o RECURSO URI do seu serviço com seus clientes offline. Os consumidores podem iniciar uma conexão Private Link usando o alias ou o recurso URI.
 
### <a name="manage-your-connection-requests"></a>Gerencie suas solicitações de conexão

Depois que um consumidor inicia uma conexão, o provedor de serviços pode aceitar ou rejeitar a solicitação de conexão. Todas as solicitações de conexão serão listadas na propriedade **privateendpointconnections** no serviço Private Link.
 
### <a name="delete-your-service"></a>Exclua seu serviço

Se o serviço Private Link não estiver mais em uso, você pode excluí-lo. No entanto, antes de excluir o serviço, certifique-se de que não há conexões de ponto final privado associadas a ele. Você pode rejeitar todas as conexões e excluir o serviço.

## <a name="properties"></a>Propriedades

Um serviço de Link Privado especifica as seguintes propriedades: 

|Propriedade |Explicação  |
|---------|---------|
|Estado de Provisionamento (Provisionamento)  |Uma propriedade somente leitura que lista o estado de provisionamento atual para o serviço Private Link. Os estados de provisionamento aplicáveis são: "Exclusão; Falhou; Conseguiu; Atualização". Quando o estado de provisionamento é "Bem sucedido", você provisionou com sucesso o seu serviço de Private Link.        |
|Alias (alias)     | Alias é uma seqüência de string somente leitura exclusiva globalmente para o seu serviço. Ele ajuda você a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de compartilhar para o seu serviço. Quando você cria um serviço de Private Link, o Azure gera o alias para o seu serviço que você pode compartilhar com seus clientes. Seus clientes podem usar este alias para solicitar uma conexão com seu serviço.          |
|Visibilidade (visibilidade)     | Visibilidade é a propriedade que controla as configurações de exposição para o seu serviço Private Link. Os provedores de serviços podem optar por limitar a exposição ao seu serviço a assinaturas com permissões RBAC (Role-Based Access Control, control de acesso baseado em função), um conjunto restrito de assinaturas ou todas as assinaturas do Azure.          |
|Aprovação automática (aprovação automática)    |   A aprovação automática controla o acesso automatizado ao serviço Private Link. As assinaturas especificadas na lista de aprovação automática são aprovadas automaticamente quando uma conexão é solicitada a partir de pontos finais privados nessas assinaturas.          |
|Configuração IP do Balancer de carga Frontend (loadBalancerFrontendIpConfigurations)    |    O serviço Private Link está vinculado ao endereço IP frontend de um Balanceador de carga padrão. Todo o tráfego destinado ao serviço chegará à frente do SLB. Você pode configurar as regras do SLB para direcionar esse tráfego para pools de backend apropriados onde seus aplicativos estão sendo executados. As configurações IP do balanceador de carga são diferentes das configurações DO NAT IP.      |
|Configuração IP NAT (ipConfigurações)    |    Esta propriedade refere-se à configuração IP NAT (Network Address Translation) para o serviço Private Link. O IP NAT pode ser escolhido a partir de qualquer sub-rede na rede virtual de um provedor de serviços. O serviço Private Link executa o nat-ing do lado do destino no tráfego do Private Link. Isso garante que não haja conflito de IP entre o espaço de endereço de origem (lado do consumidor) e destino (provedor de serviços). No lado de destino (lado do provedor de serviços), o endereço IP NAT aparecerá como IP fonte para todos os pacotes recebidos pelo seu serviço e IP de destino para todos os pacotes enviados pelo seu serviço.       |
|Conexões de ponto final privados (privateEndpointConnections)     |  Esta propriedade lista os pontos finais privados que se conectam ao serviço Private Link. Vários pontos finais privados podem se conectar ao mesmo serviço de Private Link e o provedor de serviços pode controlar o estado para pontos finais privados individuais.        |
|Proxy TCP V2 (EnableProxyProtocol)     |  Essa propriedade permite que o provedor de serviços use o proxy tcp v2 para recuperar informações de conexão sobre o consumidor do serviço. O Provedor de Serviços é responsável pela configuração das configurações do receptor para poder analisar o cabeçalho v2 do protocolo proxy.        |
|||


### <a name="details"></a>Detalhes

- O serviço Private Link pode ser acessado a partir de pontos finais privados aprovados na mesma região. O ponto final privado pode ser alcançado a partir da mesma rede virtual, VNets regionalmente peered, VNets de peering globalmente e em instalações usando conexões PRIVADAS VPN ou ExpressRoute. 
 
- Ao criar um Serviço de Link Privado, uma interface de rede é criada para o ciclo de vida do recurso. Esta interface não é gerenciável pelo cliente.
 
- O Serviço de Link Privado deve ser implantado na mesma região que a rede virtual e o Balanceador de Carga Padrão.  
 
- Um único Serviço de Link Privado pode ser acessado a partir de vários pontos finais privados pertencentes a diferentes VNets, assinaturas e/ou inquilinos do Active Directory. A conexão é estabelecida através de um fluxo de trabalho de conexão. 
 
- Vários serviços de Private Link podem ser criados no mesmo Balancer de carga padrão usando diferentes configurações de IP front-end. Há limites para o número de serviços de Private Link que você pode criar por Balanceador de Carga Padrão e por assinatura. Para obter detalhes, consulte [os limites do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- O serviço Private Link pode ter mais de uma configuração DE IP NAT vinculada a ele. A escolha de mais de uma configuração DE IP NAT pode ajudar os provedores de serviços a escalar. Hoje, os provedores de serviços podem atribuir até oito endereços IP NAT por serviço Private Link. Com cada endereço IP NAT, você pode atribuir mais portas para suas conexões TCP e, assim, dimensionar. Depois de adicionar vários endereços IP NAT a um serviço de Link Privado, você não pode excluir os endereços IP NAT. Isso é feito para garantir que as conexões ativas não sejam impactadas ao excluir os endereços IP NAT.


## <a name="alias"></a>Alias

**Alias** é um nome globalmente único para o seu serviço. Ele ajuda você a mascarar os dados do cliente para o seu serviço e, ao mesmo tempo, cria um nome fácil de compartilhar para o seu serviço. Quando você cria um serviço de Private Link, o Azure gera um alias para o seu serviço que você pode compartilhar com seus clientes. Seus clientes podem usar este alias para solicitar uma conexão com seu serviço.

O alias é composto de três partes: *Prefixo*. *GUIA*. *Sufixo*

- Prefixo é o nome do serviço. Você pode escolher seu próprio prefixo. Depois que "Alias" for criado, você não pode alterá-lo, então selecione seu prefixo apropriadamente.  
- Guid será fornecido por plataforma. Isso ajuda a tornar o nome globalmente único. 
- Sufixo é anexado pelo Azure: *região*.azure.privatelinkservice 

Alias completo: *Prefixo*. {GUID}. *região*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Exposição do serviço de controle

O serviço Private Link oferece opções para controlar a exposição do seu serviço através da configuração "Visibilidade". Você pode tornar o serviço privado para consumo de diferentes VNets que você possui (apenas permissões RBAC), restringir a exposição a um conjunto limitado de assinaturas em que você confia ou torná-lo público para que todas as assinaturas do Azure possam solicitar conexões no Private Link Serviço. Suas configurações de visibilidade decidem se um consumidor pode se conectar ao seu serviço ou não. 

## <a name="control-service-access"></a>Controle o acesso ao serviço

Os consumidores que têm exposição (controlada pela configuração de visibilidade) ao seu serviço Private Link podem criar um ponto final privado em seus VNets e solicitar uma conexão com o seu serviço Private Link. A conexão de ponto final privado será criada em um estado "Pendente" no objeto de serviço Private Link. O prestador de serviços é responsável por atuar na solicitação de conexão. Você pode aprovar a conexão, rejeitar a conexão ou excluir a conexão. Somente conexões aprovadas podem enviar tráfego para o serviço Private Link.

A ação de aprovação das conexões pode ser automatizada usando a propriedade de aprovação automática no serviço Private Link. A aprovação automática é uma capacidade dos provedores de serviços de pré-aprovar um conjunto de assinaturas para acesso automatizado ao seu serviço. Os clientes precisarão compartilhar suas assinaturas offline para que os provedores de serviços adicionem à lista de aprovação automática. A aprovação automática é um subconjunto da matriz de visibilidade. A visibilidade controla as configurações de exposição, enquanto a aprovação automática controla as configurações de aprovação do seu serviço. Se um cliente solicitar uma conexão de uma assinatura na lista de aprovação automática, a conexão será automaticamente aprovada e a conexão será estabelecida. Os provedores de serviços não precisam mais aprovar manualmente a solicitação. Por outro lado, se um cliente solicitar uma conexão de uma assinatura no array de visibilidade e não no array de aprovação automática, a solicitação chegará ao provedor de serviços, mas o provedor de serviços terá que aprovar manualmente as conexões.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Obtendo informações de conexão usando o Proxy TCP v2

Ao usar o serviço de link privado, o endereço IP de origem dos pacotes provenientes do ponto final privado é o endereço de rede traduzido (NAT) no lado do provedor de serviços usando o IP NAT alocado da rede virtual do provedor. Assim, os aplicativos recebem o endereço IP NAT alocado em vez do endereço IP de origem real dos consumidores de serviços. Se o aplicativo precisar de um endereço IP de origem real do lado do consumidor, você pode ativar o protocolo Proxy em seu serviço e recuperar as informações do cabeçalho do protocolo proxy. Além do endereço IP de origem, o cabeçalho do protocolo proxy também carrega o LinkID do ponto final privado. A combinação de endereço IP de origem e LinkID pode ajudar os provedores de serviços a identificar exclusivamente seus consumidores. Para obter mais informações sobre o Proxy Protocol, visite [aqui](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Essas informações são codificadas usando um vetor de Tamanho de Tipo (TLV) personalizado da seguinte forma:

Detalhes personalizados da TLV:

|Campo |Comprimento (Octetos)  |Descrição  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Comprimento  |2      |Comprimento do valor|
|Valor  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) representando o LINKID do ponto final privado. Codificado em formato final.|

 > [!NOTE]
 > O provedor de serviços é responsável por garantir que o serviço por trás do balanceador de carga padrão esteja configurado para analisar o cabeçalho do protocolo proxy de acordo com a [especificação](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) quando o protocolo proxy estiver ativado no serviço de link privado. A solicitação falhará se a configuração do protocolo proxy estiver ativada no serviço de link privado, mas o serviço do provedor de serviços não está configurado para analisar o cabeçalho. Da mesma forma, a solicitação falhará se o serviço do provedor de serviço estiver esperando um cabeçalho de protocolo proxy enquanto a configuração não estiver ativada no serviço de link privado. Uma vez que a configuração do protocolo proxy esteja ativada, o cabeçalho do protocolo proxy também será incluído nos testes de saúde HTTP/TCP do host para as máquinas virtuais backend, embora não haja informações do cliente no cabeçalho. 

## <a name="limitations"></a>Limitações

A seguir estão as limitações conhecidas ao usar o serviço Private Link:
- Suportado apenas no Balanceador de Carga Padrão 
- Suporta apenas tráfego IPv4
- Suporta apenas tráfego TCP

## <a name="next-steps"></a>Próximas etapas
- [Crie um serviço de link privado usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Crie um serviço de link privado usando o Azure CLI](create-private-link-service-cli.md)
