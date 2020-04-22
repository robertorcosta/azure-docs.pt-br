---
title: Segurança da camada de transporte no Azure HDInsight
description: TLS (Transport Layer Security, segurança da camada de transporte) e camada de soquetes seguros (SSL) são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771957"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Segurança da camada de transporte no Azure HDInsight

As conexões com o cluster HDInsight `https://CLUSTERNAME.azurehdinsight.net` através do ponto final do cluster público são proxidas através de nós de gateway de cluster. Essas conexões são protegidas usando um protocolo chamado TLS. A aplicação de versões mais altas de TLS em gateways melhora a segurança dessas conexões. Para obter mais informações sobre por que você deve usar versões mais recentes do TLS, consulte [Resolvendo o problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Por padrão, os clusters Azure HDInsight aceitam conexões TLS 1.2 em pontos finais HTTPS públicos e versões mais antigas para compatibilidade retrógrada. Você pode controlar a versão TLS mínima suportada nos nós de gateway durante a criação de clusters usando o portal Azure ou um modelo de Gerenciador de recursos. Para o portal, selecione a versão TLS na guia **Segurança + rede** durante a criação do cluster. Para um modelo de gerenciador de recursos no momento da implantação, use a propriedade **minSupportedTlsVersion.** Para obter um modelo de exemplo, consulte [o modelo TLS 1.2 Quickstart mínimo do HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Esta propriedade suporta três valores: "1.0", "1.1" e "1.2", que correspondem ao TLS 1.0+, TLS 1.1+ e TLS 1.2+ respectivamente.

> [!IMPORTANT]
> A partir de 30 de junho de 2020, o Azure HDInsight irá impor versões TLS 1.2 ou posteriores para todas as conexões HTTPS. Recomendamos que você garanta que todos os seus clientes estejam prontos para lidar com versões TLS 1.2 ou posteriores. Para obter mais informações, consulte [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Próximas etapas

* [Planeje uma rede virtual para o Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Crie redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Grupos de segurança de rede](../virtual-network/security-overview.md).
