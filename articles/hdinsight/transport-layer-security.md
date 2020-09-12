---
title: Segurança da camada de transporte no Azure HDInsight
description: O protocolo TLS e SSL são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 6e46cca28c049a794db617797d5f09e9f3b5720d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006884"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Segurança da camada de transporte no Azure HDInsight

As conexões com o cluster HDInsight por meio do ponto de extremidade do cluster público `https://CLUSTERNAME.azurehdinsight.net` são proxies por meio de nós de gateway de cluster. Essas conexões são protegidas usando um protocolo chamado TLS. A imposição de versões mais altas do TLS em gateways melhora a segurança dessas conexões. Para obter mais informações sobre por que você deve usar versões mais recentes do TLS, consulte [resolvendo o problema de tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

Por padrão, os clusters do Azure HDInsight aceitam conexões TLS 1,2 em pontos de extremidade HTTPS públicos e versões mais antigas para compatibilidade com versões anteriores. Você pode controlar a versão mínima do TLS com suporte nos nós de gateway durante a criação do cluster usando o portal do Azure ou um modelo do Resource Manager. Para o portal, selecione a versão do TLS na guia **segurança + rede** durante a criação do cluster. Para um modelo do Resource Manager no momento da implantação, use a propriedade **minSupportedTlsVersion** . Para obter um modelo de exemplo, consulte [modelo de início rápido de TLS 1,2 mínimo do HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Essa propriedade dá suporte a três valores: "1,0", "1,1" e "1,2", que correspondem ao TLS 1.0 +, TLS 1.1 + e TLS 1.2 + respectivamente.


## <a name="next-steps"></a>Próximas etapas

* [Planejar uma rede virtual para o Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Crie redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md).
* [Grupos de segurança de rede](../virtual-network/security-overview.md).
