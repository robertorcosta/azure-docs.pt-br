---
title: Solucionar problemas de conectividade da NAT da Rede Virtual do Azure
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Solucione problemas com a NAT da Rede Virtual.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302977"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Solucionar problemas de conectividade da NAT da Rede Virtual do Azure

Este artigo ajuda os administradores a diagnosticar e resolver problemas de conectividade ao usar a NAT da Rede Virtual.

>[!NOTE] 
>A NAT de Rede Virtual está disponível atualmente como versão prévia pública. No momento, ela só está disponível em um conjunto limitado de [regiões](nat-overview.md#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter detalhes.

## <a name="problems"></a>Problemas

- [Esgotamento de SNAT](#snat-exhaustion).
- [O ping do ICMP está falhando](#icmp-ping-is-failing).

Para resolver esses problemas, siga as etapas na seção a seguir.

## <a name="resolution"></a>Resolução

### <a name="snat-exhaustion"></a>Esgotamento de SNAT

Um só [recurso do Gateway da NAT](nat-gateway-resource.md) dá suporte de 64.000 a até 1 milhão de fluxos simultâneos.  Cada endereço IP fornece 64.000 portas SNAT para o inventário disponível. Você pode usar até 16 endereços IP por recurso do Gateway da NAT.  O mecanismo SNAT é descrito [aqui](nat-gateway-resource.md#source-network-address-translation) mais detalhadamente.

#### <a name="steps"></a>Etapas:

1. Investigar como o aplicativo está criando a conectividade de saída (por exemplo, revisão de código ou captura de pacote). 
2. Determinar se essa atividade é um comportamento esperado ou se o aplicativo está tendo um comportamento inadequado.  Usar [métricas](nat-metrics.md) no Azure Monitor para substanciar suas descobertas.
3. Avaliar se os padrões apropriados são seguidos.
4. Avaliar se o esgotamento de porta SNAT deve ser mitigado com endereços IP adicionais atribuídos ao recurso do Gateway da NAT.

#### <a name="design-pattern"></a>Padrão de design:

Sempre aproveite a reutilização de conexão e o pool de conexões quando possível.  Esse padrão evitará problemas de esgotamento de recursos imediatamente e resultará em um comportamento previsível. Os primitivos desses padrões podem ser encontrados em muitas bibliotecas e estruturas de desenvolvimento.
- Considere os [padrões de sondagem assíncrona](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) nas operações de execução longa, a fim de liberar recursos de conexão para outras operações.
- Os fluxos de longa vida (por exemplo, conexões TCP reutilizadas) devem usar keep alives TCP ou keep alives da camada de aplicativo para evitar que os sistemas intermediários atinjam o tempo limite.
- Os [padrões normais de repetição](https://docs.microsoft.com/azure/architecture/patterns/retry) devem ser usados para evitar tentativas agressivas/intermitências durante uma falha transitória ou uma recuperação de falha.
A criação de uma conexão TCP para todas as operações HTTP (também conhecidas como "conexões atômicas") é um antipadrão.  As conexões atômicas impedirão que o aplicativo seja bem dimensionado e gaste recursos.  Sempre inclua várias operações em um pipeline na mesma conexão.  Seu aplicativo se beneficiará da velocidade da transação e dos custos de recursos.  Quando o aplicativo usa uma criptografia de camada de transporte (por exemplo, TLS), há um custo significativo associado ao processamento de novas conexões.  Examine [Padrões de design de nuvem do Azure](https://docs.microsoft.com/azure/architecture/patterns/) para obter padrões adicionais de melhores práticas.

#### <a name="mitigations"></a>Atenuações

Você pode escalar a conectividade de saída da seguinte maneira:

| Cenário | Atenuação |
|---|---|
| Você está enfrentando a contenção de portas SNAT e o esgotamento de porta SNAT durante períodos de alto uso. | Determine se você pode adicionar recursos adicionais de endereço IP público ou recursos de prefixo IP público. Essa adição permitirá até 16 endereços IP no total para o Gateway da NAT. Essa adição fornecerá mais inventário para as portas SNAT disponíveis (64.000 por endereço IP) e permitirá que você escale ainda mais o seu cenário.|
| Você já recebeu 16 endereços IP e ainda está enfrentando o esgotamento de porta SNAT. | Distribua o ambiente de aplicativo em várias sub-redes e forneça um recurso do Gateway da NAT para cada sub-rede. |

>[!NOTE]
>É importante entender por que o esgotamento de SNAT ocorre. Verifique se você está usando os padrões corretos para cenários escalonáveis e confiáveis.  A adição de mais portas SNAT a um cenário sem entender a causa da demanda deve ser o último recurso. Se você não entender por que o cenário está aplicando pressão no inventário de portas SNAT, a adição de mais portas SNAT ao inventário pela adição de mais endereços IP só atrasará a mesma falha de esgotamento à medida que o aplicativo for dimensionado.  Você pode estar mascarando outras ineficiências e antipadrões.

### <a name="icmp-ping-is-failing"></a>O ping do ICMP está falhando

O [NAT da Rede Virtual](nat-overview.md) dá suporte a protocolos UDP e TCP IPv4. Não há suporte para o ICMP e é esperada uma falha.  Em vez disso, use testes de conexão TCP (por exemplo, "ping do TCP") e testes da camada de aplicativo específicos do UDP para validar a conectividade de ponta a ponta.

A tabela a seguir pode ser usada como um ponto de partida para saber quais ferramentas serão usadas para iniciar os testes.

| Sistema operacional | Teste de conexão TCP genérica | Teste de camada de aplicativo TCP | UDP |
|---|---|---|---|
| Linux | nc (teste de conexão genérica) | cURL (teste da camada de aplicativo TCP) | específico ao aplicativo |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) do PowerShell | específico ao aplicativo |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [NAT de Rede Virtual](nat-overview.md)
- Saiba mais sobre o [recurso do Gateway da NAT](nat-gateway-resource.md)
- Saiba mais sobre [métricas e alertas para recursos do Gateway da NAT](nat-metrics.md).
