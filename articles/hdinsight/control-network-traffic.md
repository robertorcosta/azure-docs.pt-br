---
title: Controlar o tráfego de rede no Azure HDInsight
description: Aprenda técnicas para controlar o tráfego de entrada e de saída para clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 031dbb0e8c9b9fb8dc37b264f9ba8e1186efc832
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783585"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Controlar o tráfego de rede no Azure HDInsight

O tráfego de rede em Redes Virtuais do Azure pode ser controlado com os seguintes métodos:

* Os **NSGs** (grupos de segurança de rede) permitem filtrar o tráfego de entrada e de saída para a rede. Para obter mais informações, consulte o documento [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md).

* NVA (soluções de **virtualização de rede** ) podem ser usadas somente com tráfego de saída. NVAs replicar a funcionalidade de dispositivos como firewalls e roteadores. Para obter mais informações, consulte o documento [Dispositivos de rede](https://azure.microsoft.com/solutions/network-appliances).

Como um serviço gerenciado, o HDInsight requer acesso irrestrito aos serviços de integridade e gerenciamento do HDInsight para o tráfego de entrada e saída da VNET. Ao usar o NSGs, você deve garantir que esses serviços ainda possam se comunicar com o cluster HDInsight.

![Diagrama de entidades do HDInsight criadas na VNET personalizada do Azure](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight com grupos de segurança de rede

Se você planeja usar **grupos de segurança de rede** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifique a região do Azure que você pretende usar para o HDInsight.

2. Identifique as marcas de serviço exigidas pelo HDInsight para sua região. Para obter mais informações, consulte [marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight](hdinsight-service-tags.md).

3. Crie ou modifique os grupos de segurança de rede para a sub-rede na qual você planeja instalar o HDInsight.

    * __Grupos de segurança de rede__: permita o tráfego de __entrada__ na porta __443__ dos endereços IP. Isso garantirá que os serviços de gerenciamento do HDInsight possam acessar o cluster de fora da rede virtual.

Para obter mais informações sobre grupos de segurança de rede, consulte a [visão geral dos grupos de segurança de rede](../virtual-network/security-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlando o tráfego de saída de clusters HDInsight

Para obter mais informações sobre como controlar o tráfego de saída de clusters HDInsight, consulte [Configurar a restrição de tráfego de rede de saída para clusters do Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Túnel forçado para local

O túnel forçado é uma configuração de roteamento definida pelo usuário em que todo o tráfego de uma sub-rede é forçado para uma rede ou local específico, como sua rede local ou firewall. O túnel forçado de todas as transferências de dados de volta para o local _não_ é recomendado devido a grandes volumes de transferência de dados e possível impacto no desempenho.

Os clientes interessados em configurar o túnel forçado devem usar [metastores personalizados](./hdinsight-use-external-metadata-stores.md) e configurar a conectividade apropriada a partir da sub-rede do cluster ou da rede local para esses metarepositórios personalizados.

Para ver um exemplo da configuração do UDR com o Firewall do Azure, consulte [Configurar a restrição de tráfego de rede de saída para clusters do Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ip-addresses"></a> Endereços IP obrigatórios

Se você usar grupos de segurança de rede ou rotas definidas pelo usuário para controlar o tráfego, consulte [endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a>Portas obrigatórias

Se você pretende usar um **firewall** e acessar o cluster de fora em determinadas portas, será preciso permitir o tráfego nas portas necessárias para o seu cenário. Por padrão, nenhuma lista de permissões especial de portas é necessária, contanto que o tráfego de gerenciamento do Azure explicado na seção anterior tenha permissão para alcançar o cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte o documento [Portas usadas pelos serviços do Apache Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para obter mais informações sobre as regras de firewall para soluções de virtualização, consulte o documento [Cenário de solução de virtualização](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de código e exemplos de criação de redes virtuais do Azure, consulte [criar redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md).
* Para obter um exemplo de ponta a ponta de como configurar o HDInsight para se conectar a uma rede local, consulte [Conectar o HDInsight a uma rede local](./connect-on-premises-network.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte a [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).
* Para obter mais informações sobre os Grupos de Segurança de Rede, veja [Grupos de segurança de rede](../virtual-network/security-overview.md).
* Para obter mais informações sobre rotas definidas pelo usuário, consulte [rotas definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).
* Para obter mais informações sobre redes virtuais, consulte [Plan VNETs for HDInsight](./hdinsight-plan-virtual-network-deployment.md).
