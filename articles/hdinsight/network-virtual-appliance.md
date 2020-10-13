---
title: Configurar solução de virtualização de rede no Azure HDInsight
description: Saiba como configurar vários recursos adicionais para sua solução de virtualização de rede no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630926"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar solução de virtualização de rede no Azure HDInsight

> [!Important]
> As informações a seguir **só** serão necessárias se você quiser configurar uma solução de virtualização de rede (NVA) diferente do firewall do Azure.

O Firewall do Azure é configurado automaticamente para permitir o tráfego para muitos dos cenários importantes comuns. Usar outra solução de virtualização de rede exigirá que você configure vários recursos adicionais. Lembre-se dos seguintes fatores ao configurar sua solução de virtualização de rede:

* Os serviços com capacidade de ponto de extremidade de serviço podem ser configurados com pontos de extremidade de serviço que resultam em ignorar o NVA, geralmente para considerações de custo ou desempenho.
* As dependências de endereço IP são para tráfego não HTTP/S (tráfego TCP e UDP).
* Os pontos de extremidade HTTP/HTTPS do FQDN podem ser aprovados em seu dispositivo NVA.
* Atribua a tabela de rotas que você cria à sub-rede do HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências compatíveis com ponto de extremidade de serviço

Opcionalmente, você pode habilitar um ou mais dos seguintes pontos de extremidade de serviço, o que resultará em ignorar o NVA. Essa opção pode ser útil para grandes quantidades de transferências de dados para economizar em custos e também para otimizações de desempenho. 

| **Ponto de extremidade** |
|---|
| SQL do Azure |
| Armazenamento do Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| **Ponto de extremidade** | **Detalhes** |
|---|---|
| IPs publicados [aqui](hdinsight-management-ip-addresses.md) | Esses IPs são para o local de controle do HDInsight e devem ser incluídos no UDR para evitar o roteamento assimétrico |
| AAD – IPs privados DS | Necessário somente para clusters ESP|


### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN

> [!Important]
> A lista a seguir fornece apenas alguns FQDNs que podem ser necessários para o sistema operacional e aplicação de patches de segurança ou validações de certificado depois que o cluster é criado e durante o tempo de vida das operações de cluster. Você pode obter a lista de dependências de FQDNs (principalmente o armazenamento do Azure e o barramento de serviço do Azure) para configurar seu NVA [neste arquivo](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Essas dependências são usadas pelo provedor de recursos do HDInsight (RP) para criar e monitorar e gerenciar clusters com êxito. Isso inclui logs de telemetria/diagnóstico, metadados de provisionamento, configurações relacionadas a cluster, scripts, modelos de ARM, etc. A lista de dependências de FQDN pode mudar com a liberação de atualizações de HDIngisht futuras.

| **Ponto de extremidade**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>Próximas etapas

* [Usar o firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
