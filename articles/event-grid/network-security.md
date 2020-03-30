---
title: Segurança de rede para recursos da Azure Event Grid
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300148"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Segurança de rede para recursos da Azure Event Grid
Este artigo descreve como usar os seguintes recursos de segurança com o Azure Event Grid: 

- Tags de serviço para saída (visualização)
- Regras de firewall IP para ingestão (visualização)
- Pontos finais privados para ingestão (visualização)


## <a name="service-tags"></a>Marcas de serviço
Uma tag de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre tags de serviço, consulte [visão geral das tags de serviço](../virtual-network/service-tags-overview.md).

Você pode usar tags de serviço para definir controles de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou [Firewall Azure](../firewall/service-tags.md). Use tags de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, **AzureEventGrid**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Tag de serviço | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall Do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Grade de Eventos do Azure. <br/><br/>*Nota:* Esta tag abrange os pontos finais da Azure Event Grid no Centro-Sul dos EUA, Leste dos EUA, Leste dos EUA 2, US West 2 e US Central apenas. | Ambos | Não | Não |


## <a name="ip-firewall"></a>Firewall de IP 
O Azure Event Grid suporta controles de acesso baseados em IP para publicação em tópicos e domínios. Com controles baseados em IP, você pode limitar os editores a um tópico ou domínio a apenas um conjunto de máquinas e serviços em nuvem aprovados. Este recurso complementa os [mecanismos de autenticação](security-authentication.md) suportados pela Event Grid.

Por padrão, tópico e domínio são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IP ou faixas de endereçoIP na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida).


## <a name="private-endpoints"></a>Pontos finais privados
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir a ingestão de eventos diretamente de sua rede virtual para seus tópicos e domínios com segurança através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. Um ponto final privado é uma interface de rede especial para um serviço Azure em seu VNet. Quando você cria um ponto final privado para o seu tópico ou domínio, ele fornece conectividade segura entre os clientes em seu VNet e seu recurso Event Grid. O ponto final privado é atribuído a um endereço IP da faixa de endereço IP do seu VNet. A conexão entre o ponto final privado e o serviço Event Grid usa um link privado seguro.

![Diagrama de arquitetura](./media/network-security/architecture-diagram.png)

O uso de pontos finais privados para o recurso Event Grid permite:

- Proteja o acesso ao seu tópico ou domínio a partir de uma rede de backbone VNet sobre a Microsoft em oposição à internet pública.
- Conecte-se com segurança a partir de redes locais que se conectam ao VNet usando VPN ou ExpressRoutes com peering privado.

Quando você cria um ponto final privado para um tópico ou domínio em seu VNet, uma solicitação de consentimento é enviada para aprovação ao proprietário do recurso. Se o usuário que solicitar a criação do ponto final privado também for proprietário do recurso, essa solicitação de consentimento será automaticamente aprovada. Caso contrário, a conexão está em estado **pendente** até ser aprovada. Os aplicativos no VNet podem se conectar ao serviço Event Grid através do ponto final privado sem problemas, usando as mesmas strings de conexão e mecanismos de autorização que eles usariam de outra forma. Os proprietários de recursos podem gerenciar solicitações de consentimento e os pontos finais privados, através da guia **pontos finais privados** para o recurso no portal Azure.

### <a name="connect-to-private-endpoints"></a>Conecte-se a pontos finais privados
Os editores em um VNet usando o ponto final privado devem usar a mesma seqüência de conexões para o tópico ou domínio que os clientes que se conectam ao ponto final público. A resolução DNS encaminha automaticamente conexões do VNet para o tópico ou domínio por um link privado. Event Grid cria uma [zona de DNS privada](../dns/private-dns-overview.md) anexada ao VNet com a atualização necessária para os pontos finais privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS.

### <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos finais privados
Quando você cria um ponto final privado, o registro DNS CNAME para o recurso `privatelink`é atualizado para um alias em um subdomínio com o prefixo . Por padrão, é criada uma região DNS privada que corresponde ao subdomínio do link privado. 

Quando você resolve a URL de ponto final de tópico ou domínio de fora do VNet com o ponto final privado, ele resolve para o ponto final público do serviço. Os registros de recursos do DNS para 'topicA', quando **resolvidos de fora do VNet** hospedando o ponto final privado, serão:

| Nome                                          | Type      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<perfil gerente de tráfego azul\>

Você pode negar ou controlar o acesso de um cliente fora do VNet através do ponto final público usando o [firewall IP](#ip-firewall). 

Quando resolvido a partir do VNet que hospeda o ponto final privado, a URL de ponto final de tópico ou domínio se resolve para o endereço IP do ponto final privado. Os registros de recursos do DNS para o tópico 'topicA', quando **resolvidos de dentro do VNet** hospedando o ponto final privado, serão:

| Nome                                          | Type      | Valor                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | Um         | 10.0.0.5

Essa abordagem permite o acesso ao tópico ou ao domínio usando a mesma seqüência de conexões para clientes no VNet que hospeda os pontos finais privados e clientes fora do VNet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes podem resolver o FQDN para o tópico ou ponto final de domínio para o endereço IP de ponto final privado. Configure seu servidor DNS para delegar seu subdomínio de link privado à zona DNS privada para o VNet ou configure os registros A para `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` com o endereço IP de ponto final privado.

O nome da zona `privatelink.eventgrid.azure.net`DNS recomendado é .

### <a name="private-endpoints-and-publishing"></a>Pontos finais privados e publicações

A tabela a seguir descreve os vários estados da conexão de ponto final privado e os efeitos na publicação:

| Estado da Conexão   |  Publicar com sucesso (Sim/Não) |
| ------------------ | -------------------------------|
| Aprovado           | Sim                            |
| Rejeitado           | Não                             |
| Pendente            | Não                             |
| Desconectado       | Não                             |

Para que a publicação seja bem sucedida, o estado privado de conexão de ponto final deve ser **aprovado**. Se uma conexão for rejeitada, ela não poderá ser aprovada usando o portal Azure. A única possibilidade é excluir a conexão e criar uma nova.

## <a name="pricing-and-quotas"></a>Preços e cotas
**Os pontos finais privados** só estão disponíveis com tópicos e domínios de nível premium. Event Grid permite que até 64 conexões de ponto final privado sejam criadas por tópico ou domínio. Para atualizar do nível básico para o nível premium, consulte o artigo [do nível de preços de atualização.](update-tier.md)

**O** recurso ip firewall está disponível em níveis básicos e premium do Event Grid. Permitimos que até 16 regras de Firewall IP sejam criadas por tópico ou domínio.


## <a name="next-steps"></a>Próximas etapas
Você pode configurar o firewall IP para o recurso Event Grid para restringir o acesso à internet pública a partir de apenas um conjunto seleto de endereços IP ou faixas de endereços IP. Para obter instruções passo a passo, consulte [Configurar firewall IP](configure-firewall.md).

Você pode configurar pontos finais privados para restringir o acesso somente de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [Configure pontos finais privados](configure-private-endpoints.md).
