---
title: Use pontos finais privados
titleSuffix: Azure Storage
description: Visão geral dos pontos finais privados para acesso seguro a contas de armazenamento de redes virtuais.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299049"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Use pontos finais privados para armazenamento Azure

Você pode usar [pontos finais privados](../../private-link/private-endpoint-overview.md) para suas contas de armazenamento do Azure para permitir que os clientes em uma rede virtual (VNet) acessem com segurança os dados por meio de um [Link Privado](../../private-link/private-link-overview.md). O ponto final privado usa um endereço IP do espaço de endereço VNet para o serviço da conta de armazenamento. O tráfego de rede entre os clientes no VNet e a conta de armazenamento atravessa o VNet e um link privado na rede backbone da Microsoft, eliminando a exposição da internet pública.

O uso de pontos finais privados para sua conta de armazenamento permite:

- Proteja sua conta de armazenamento configurando o firewall de armazenamento para bloquear todas as conexões no ponto final público do serviço de armazenamento.
- Aumente a segurança para a rede virtual (VNet), permitindo que você bloqueie a exfiltração de dados do VNet.
- Conecte-se com segurança às contas de armazenamento de redes locais que se conectam ao VNet usando [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../../expressroute/expressroute-locations.md) com peering privado.

## <a name="conceptual-overview"></a>Visão geral conceitual

![Visão geral dos pontos finais privados para o Armazenamento Azure](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Um ponto final privado é uma interface de rede especial para um serviço Azure em sua [Rede Virtual](../../virtual-network/virtual-networks-overview.md) (VNet). Quando você cria um ponto final privado para sua conta de armazenamento, ele fornece conectividade segura entre os clientes em seu VNet e seu armazenamento. O ponto final privado é atribuído a um endereço IP da faixa de endereço IP do seu VNet. A conexão entre o ponto final privado e o serviço de armazenamento usa um link privado seguro.

Os aplicativos no VNet podem se conectar ao serviço de armazenamento através do ponto final privado sem **problemas, usando as mesmas strings de conexão e mecanismos de autorização que eles usariam de outra forma**. Os pontos finais privados podem ser usados com todos os protocolos suportados pela conta de armazenamento, incluindo REST e SMB.

Os pontos finais privados podem ser criados em sub-redes que usam [pontos finais de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md). Assim, os clientes em uma sub-rede podem se conectar a uma conta de armazenamento usando o ponto final privado, enquanto usam pontos finais de serviço para acessar outros.

Quando você cria um ponto de extremidade privado para um serviço de armazenamento em sua rede virtual, uma solicitação de consentimento é enviada para aprovação para o proprietário da conta de armazenamento. Se o usuário que solicitar a criação do ponto final privado também for proprietário da conta de armazenamento, essa solicitação de consentimento será automaticamente aprovada.

Os proprietários de contas de armazenamento podem gerenciar solicitações de consentimento e os pontos finais privados, através da guia '*Private endpoints*' para a conta de armazenamento no [portal Azure](https://portal.azure.com).

> [!TIP]
> Se você quiser restringir o acesso à sua conta de armazenamento apenas através do ponto final privado, configure o firewall de armazenamento para negar ou controlar o acesso através do ponto final público.

Você pode proteger sua conta de armazenamento para aceitar apenas conexões do seu VNet, [configurando o firewall](storage-network-security.md#change-the-default-network-access-rule) de armazenamento para negar acesso através de seu ponto final público por padrão. Você não precisa de uma regra de firewall para permitir o tráfego de um VNet que tenha um ponto final privado, já que o firewall de armazenamento só controla o acesso através do ponto final público. Os pontos finais privados, em vez disso, dependem do fluxo de consentimento para conceder acesso a sub-redes ao serviço de armazenamento.

### <a name="private-endpoints-for-azure-storage"></a>Pontos finais privados para armazenamento Azure

Ao criar o ponto final privado, você deve especificar a conta de armazenamento e o serviço de armazenamento ao qual ele se conecta. Você precisa de um ponto final privado separado para cada serviço de armazenamento em uma conta de armazenamento que você precisa acessar, ou seja, [Blobs,](../blobs/storage-blobs-overview.md) [Data Lake Storage Gen2,](../blobs/data-lake-storage-introduction.md) [Files,](../files/storage-files-introduction.md) [Filas,](../queues/storage-queues-introduction.md) [Tabelas](../tables/table-storage-overview.md)ou [Sites Estáticos.](../blobs/storage-blob-static-website.md)

> [!TIP]
> Crie um ponto final privado separado para a instância secundária do serviço de armazenamento para obter melhor desempenho de leitura em contas RA-GRS.

Para ler o acesso à região secundária com uma conta de armazenamento configurada para armazenamento geo-redundante, você precisa de pontos finais privados separados para as instâncias primária e secundária do serviço. Você não precisa criar um ponto final privado para a instância secundária para **failover**. O ponto final privado se conectará automaticamente à nova instância primária após o failover. Para obter mais informações sobre as opções de redundância de armazenamento, consulte [a redundância do Azure Storage](storage-redundancy.md).

Para obter informações mais detalhadas sobre a criação de um ponto final privado para sua conta de armazenamento, consulte os seguintes artigos:

- [Conecte-se privadamente a uma conta de armazenamento a partir da experiência da Conta de Armazenamento no portal Azure](../../private-link/create-private-endpoint-storage-portal.md)
- [Crie um ponto final privado usando o Private Link Center no portal Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado usando a CLI do Azure](../../private-link/create-private-endpoint-cli.md)
- [Crie um ponto final privado usando o Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Conectando-se a pontos finais privados

Os clientes em um VNet usando o ponto final privado devem usar a mesma seqüência de conexão para a conta de armazenamento, como clientes que se conectam ao ponto final público. Contamos com a resolução DNS para direcionar automaticamente as conexões do VNet para a conta de armazenamento por um link privado.

> [!IMPORTANT]
> Use a mesma seqüência de conexões para se conectar à conta de armazenamento usando pontos finais privados, como você usaria de outra forma. Por favor, não se conecte à conta de armazenamento usando sua URL de subdomínio '*privatelink*'.

Criamos uma [zona de DNS privada](../../dns/private-dns-overview.md) anexada ao VNet com as atualizações necessárias para os pontos finais privados, por padrão. No entanto, se você estiver usando seu próprio servidor DNS, talvez seja necessário fazer alterações adicionais na configuração do DNS. A seção sobre [alterações de DNS](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para pontos finais privados.

## <a name="dns-changes-for-private-endpoints"></a>Alterações de DNS para pontos finais privados

Quando você cria um ponto final privado, o registro de recurso DNS CNAME para a conta de armazenamento é atualizado para um alias em um subdomínio com o prefixo '*privatelink*'. Por padrão, também criamos uma [zona DNS privada,](../../dns/private-dns-overview.md)correspondente ao subdomínio '*privatelink*', com os registros de recursos DNS A para os pontos finais privados.

Quando você resolve a URL de ponto final de armazenamento de fora do VNet com o ponto final privado, ele resolve para o ponto final público do serviço de armazenamento. Quando resolvido a partir do VNet que hospeda o ponto final privado, a URL do ponto final de armazenamento se resolve para o endereço IP do ponto final privado.

Para o exemplo ilustrado acima, os registros de recursos DNS para a conta de armazenamento 'StorageAccountA', quando resolvidos fora do VNet hospedando o ponto final privado, serão:

| Nome                                                  | Type  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<ponto final público do serviço de armazenamento\>                   |
| \<ponto final público do serviço de armazenamento\>                   | Um     | \<endereço IP público do serviço de armazenamento\>                 |

Como mencionado anteriormente, você pode negar ou controlar o acesso para clientes fora do VNet através do ponto final público usando o firewall de armazenamento.

Os registros de recursos DNS para StorageAccountA, quando resolvidos por um cliente no VNet hospedando o ponto final privado, serão:

| Nome                                                  | Type  | Valor                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | Um     | 10.1.1.5                                              |

Essa abordagem permite o acesso à conta de armazenamento **usando a mesma seqüência de conexões** para clientes no VNet que hospeda os pontos finais privados, bem como clientes fora do VNet.

Se você estiver usando um servidor DNS personalizado em sua rede, os clientes devem ser capazes de resolver o FQDN para o ponto final da conta de armazenamento para o endereço IP de ponto final privado. Você deve configurar seu servidor DNS para delegar seu subdomínio de link privado para a região dns privada para o VNet ou configurar os registros A de '*StorageAccountA.privatelink.blob.core.windows.net*' com o endereço IP de ponto final privado.

> [!TIP]
> Ao usar um servidor DNS personalizado ou no local, você deve configurar seu servidor DNS para resolver o nome da conta de armazenamento no subdomínio 'privatelink' para o endereço IP de ponto final privado. Você pode fazer isso delegando o subdomínio 'privatelink' para a zona DNS privada do VNet ou configurando a zona DNS no servidor DNS e adicionando os registros DNS A.

Os nomes de zona DeDNS recomendados para pontos finais privados para serviços de armazenamento são:

| Serviço de armazenamento        | Nome da zona                            |
| :--------------------- | :----------------------------------- |
| Serviço Blob           | `privatelink.blob.core.windows.net`  |
| Armazenamento do Data Lake Gen2 | `privatelink.dfs.core.windows.net`   |
| Serviço de arquivo           | `privatelink.file.core.windows.net`  |
| Serviço de fila          | `privatelink.queue.core.windows.net` |
| Serviço de mesa          | `privatelink.table.core.windows.net` |
| Sites estáticos        | `privatelink.web.core.windows.net`   |

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:

- [Resolução de nomes para recursos em redes virtuais do Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuração de DNS para pontos finais privados](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Preços

Para obter detalhes de preço, confira [Preço do Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemas conhecidos

Tenha em mente os seguintes problemas conhecidos sobre pontos finais privados para o Azure Storage.

### <a name="copy-blob-support"></a>Suporte ao Copy Blob

Se a conta de armazenamento estiver protegida por um firewall e a conta for acessada através de pontos finais privados, essa conta não poderá servir como fonte de uma operação [do Copy Blob.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Restrições de acesso de armazenamento para clientes em VNets com pontos finais privados

Clientes em VNets com pontos finais privados existentes enfrentam restrições ao acessar outras contas de armazenamento que possuem pontos finais privados. Por exemplo, suponha que um VNet N1 tenha um ponto final privado para uma conta de armazenamento A1 para armazenamento Blob. Se a conta de armazenamento A2 tiver um ponto final privado em um VNet N2 para armazenamento Blob, os clientes no VNet N1 também devem acessar o armazenamento Blob na conta A2 usando um ponto final privado. Se a conta de armazenamento A2 não tiver nenhum ponto final privado para armazenamento Blob, os clientes no VNet N1 poderão acessar o armazenamento Blob nessa conta sem um ponto final privado.

Essa restrição é resultado das alterações de DNS feitas quando a conta A2 cria um ponto final privado.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regras de grupo de segurança de rede para sub-redes com pontos de extremidade privados

Atualmente, você não pode configurar regras do [Network Security Group](../../virtual-network/security-overview.md) (NSG) e rotas definidas pelo usuário para pontos finais privados. As regras de NSG aplicadas à sub-rede que hospeda o ponto final privado são aplicadas ao ponto final privado. Uma solução limitada para este problema é implementar suas regras de acesso para pontos finais privados nas sub-redes de origem, embora essa abordagem possa exigir uma sobrecarga de gerenciamento mais alta.

## <a name="next-steps"></a>Próximas etapas

- [Configure firewalls de armazenamento Azure e redes virtuais](storage-network-security.md)
- [Recomendações de segurança para armazenamento Blob](../blobs/security-recommendations.md)
