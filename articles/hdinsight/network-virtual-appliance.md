---
title: Configurar solução de virtualização de rede no Azure HDInsight
description: Saiba como configurar vários recursos adicionais para sua solução de virtualização de rede no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864702"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar solução de virtualização de rede no Azure HDInsight

> [!Important]
> As informações a seguir **só** serão necessárias se você quiser configurar uma solução de virtualização de rede (NVA) diferente do firewall do Azure.

O Firewall do Azure é configurado automaticamente para permitir o tráfego para muitos dos cenários importantes comuns. Usar outra solução de virtualização de rede exigirá que você configure vários recursos adicionais. Lembre-se dos seguintes fatores ao configurar sua solução de virtualização de rede:

* Os serviços compatíveis com o Ponto de Extremidade de Serviço devem ser configurados com pontos de extremidade de serviço.
* As dependências de endereço IP são para tráfego não HTTP/S (tráfego TCP e UDP).
* Os pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados em seu dispositivo NVA.
* Pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar com base em vários qualificadores.
* Atribua a tabela de rotas que você cria à sub-rede do HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências compatíveis com ponto de extremidade de serviço

| **Ponto de extremidade** |
|---|
| SQL do Azure |
| Armazenamento do Azure |
| Active Directory do Azure |

### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| **Ponto de extremidade** | **Detalhes** |
|---|---|
| \*:123 | Verificação do relógio do NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| IPs publicados [aqui](hdinsight-management-ip-addresses.md) | Esses IPs são o serviço do HDInsight |
| Os IPs privados do AAD-DS para clusters ESP |
| \*: 16800 para ativação do Windows KMS |
| \*12000 para Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN

> [!Important]
> A lista a seguir fornece apenas alguns dos FQDNs mais importantes. Você pode obter FQDNs adicionais (principalmente o armazenamento do Azure e o barramento de serviço do Azure) para configurar seu NVA [neste arquivo](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Ponto de extremidade**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Próximas etapas

* [Usar o firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
