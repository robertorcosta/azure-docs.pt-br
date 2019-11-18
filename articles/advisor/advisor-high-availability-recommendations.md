---
title: Melhorar a disponibilidade do aplicativo com o Assistente do Azure | Microsoft Docs
description: Use o Azure Advisor para melhorar a alta disponibilidade das implantações do Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: dd1b898adf4c4cdff45e05427757d90d5f80bf25
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145336"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhorar a disponibilidade do aplicativo com o Assistente do Azure

O Assistente do Azure ajuda a garantir e melhorar a continuidade de aplicativos críticos aos negócios. Você pode obter recomendações de alta disponibilidade do Advisor na guia **Alta Disponibilidade** do painel Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Assegurar tolerância a falhas de máquina virtual

Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, será preciso adicionar pelo menos mais uma máquina virtual nele. É recomendável agrupar duas ou mais máquinas virtuais em um conjunto de disponibilidade para garantir que pelo menos uma delas esteja disponível durante uma interrupção.

## <a name="ensure-availability-set-fault-tolerance"></a>Assegure que haja tolerância a falhas de conjunto de disponibilidade

Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. O Assistente identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais a ele. Essa configuração garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA da máquina virtual do Azure. Você pode optar por criar uma máquina virtual ou adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Usar o Managed Disks para melhorar a confiabilidade de dados

Máquinas virtuais que estão em um conjunto de disponibilidade com discos que compartilham contas de armazenamento ou unidades de escala de armazenamento não são resilientes a falhas de unidade de escala de armazenamento únicas durante interrupções. O Assistente identificará esses conjuntos de disponibilidade e recomendará a migração para o Azure Managed Disks. Isso garantirá que os discos das diferentes máquinas virtuais no conjunto de disponibilidade estejam isolados o suficiente para evitar um ponto único de falha. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância a falhas de Gateway de Aplicativo

Essa recomendação garante a continuidade de negócios de aplicativos de missão crítica viabilizados por gateways de aplicativo. O Assistente identifica as instâncias de gateway de aplicativo que não estão configuradas para tolerância a falhas e sugere ações de correção que podem ser executadas. O Assistente identifica Gateways de Aplicativo de instância única médios ou grandes e recomenda adicionar pelo menos uma instância a mais. Ele também identifica Gateways de Aplicativo de instância única ou de várias instâncias pequenos e recomenda a migração para SKUs médios ou grandes. O Assistente recomenda essas ações para garantir que suas instâncias de Gateway de Aplicativo estejam configuradas para satisfazer os requisitos de SLA atuais para esses recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger seus dados de máquina virtual contra exclusão acidental

Configurar o backup da máquina virtual garante a disponibilidade de seus dados críticos de negócios e oferece proteção contra corrupção ou exclusão acidental. O Assistente identifica as máquinas virtuais em que o backup não está habilitado e recomenda habilitar o backup. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que você terá acesso aos especialistas de nuvem do Azure quando precisar

Ao executar uma carga de trabalho de negócios crítica, é importante ter acesso ao suporte técnico quando necessário. O Assistente identifica possíveis assinaturas comercialmente críticas que não têm o suporte técnico incluído no plano de suporte e recomenda a atualização para uma opção que inclua o suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Criar alertas de Integridade do Serviço do Azure para ser notificado quando problemas do Azure afetarem você

É recomendável configurar os alertas de Integridade do Serviço do Azure para ser notificado quando problemas de serviço do Azure afetarem você. A [Integridade do Serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece diretrizes e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Assistente identifica as assinaturas que não têm alertas configurados e recomenda criar um.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos de extremidade do Gerenciador de Tráfego para resiliência

Perfis do Gerenciador de Tráfego com mais de um ponto de extremidade terão maior disponibilidade se qualquer ponto de extremidade falhar. Colocar pontos de extremidade em diferentes regiões melhora ainda mais a confiabilidade do serviço. O Assistente identifica os perfis do Gerenciador de Tráfego em que há apenas um ponto de extremidade e recomenda adicionar pelo menos mais um ponto de extremidade em outra região.

Se todos os pontos de extremidade em um perfil do Gerenciador de Tráfego configurado para roteamento de proximidade estiverem na mesma região, os usuários de outras regiões poderão sofrer atrasos de conexão. Adicionar ou mover um ponto de extremidade para outra região melhorará o desempenho geral e fornecerá maior disponibilidade se todos os pontos de extremidade em uma região falharem. O Assistente identifica os perfis do Gerenciador de Tráfego configurados para roteamento de proximidade onde os pontos de extremidade estão na mesma região. Ele recomenda adicionar ou mover um ponto de extremidade para outra região do Azure.

Se um perfil do Gerenciador de Tráfego estiver configurado para roteamento geográfico, o tráfego será roteado para os pontos de extremidade com base em regiões definidas. Se uma região falhar, não haverá nenhum failover predefinido. Ter um ponto de extremidade em que o Agrupamento Regional está configurado como "Todos (Mundo)" evitará que o tráfego seja descartado e aprimorará a disponibilidade do serviço. O Assistente identifica os perfis do Gerenciador de Tráfego configurados para roteamento geográfico em que não há nenhum ponto de extremidade configurado para ter o Agrupamento Regional como "Todos (Mundo)". Ele recomenda alterar a configuração para tornar um ponto de extremidade "Todos (Mundo).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Usar exclusão reversível na Conta de Armazenamento do Microsoft Azure para salvar e recuperar dados após uma substituição ou exclusão acidental

Habilite a [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na conta de armazenamento para que os blobs excluídos passem para um estado de exclusão reversível, em vez de serem excluídos permanentemente. Quando os dados são substituídos, um instantâneo de exclusão reversível é gerado para salvar o estado dos dados substituídos. O uso da exclusão reversível permite que você se recupere em caso de exclusões ou substituições acidentais. O Assistente identifica as contas do Armazenamento do Azure que não têm a exclusão reversível habilitada e recomenda habilitá-las.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configure seu gateway de VPN como ativo-ativo para ter resiliência de conexão

Na configuração ativo-ativo, ambas as instâncias de um gateway de VPN estabelecerão túneis VPN S2S para seu dispositivo VPN local. Quando um evento de manutenção planejada ou um evento não planejado ocorrer em uma instância do gateway, o tráfego será mudado para outro túnel IPsec ativo automaticamente. O Assistente do Azure identificará os gateways de VPN que não estão configurados como ativo-ativo e sugerirá que você os configure para alta disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Usar gateways de VPN de produção para executar suas cargas de trabalho de produção

O Azure Advisor verificará se há gateways de VPN que são uma SKU básica e recomendamos que você use uma SKU de produção. O SKU básico foi projetado para fins de desenvolvimento e teste. As SKUs de produção oferecem um número maior de túneis, suporte a BGP, opções de configuração ativo-ativo, política de IPSec/IKE personalizada e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detectará regras de alerta com consultas inválidas especificadas na seção de condição. Regras de alerta de log são criadas no Azure Monitor e são usadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinarão se um alerta precisar ser disparado. Consultas de análise podem se tornar inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que você corrija a consulta na regra de alerta para impedir que ela seja desabilitada automaticamente e garanta a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre como solucionar problemas de regras de alerta](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurar o modo de indexação consistente na sua coleção de Cosmos DB

Azure Cosmos DB contêineres configurados com o modo de indexação lento podem afetar a atualização dos resultados da consulta. O Advisor detectará contêineres configurados dessa maneira e recomendará alternar para o modo consistente. [Saiba mais sobre políticas de indexação no Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contêineres do Azure Cosmos DB com uma chave de partição

O Azure Advisor identificará Azure Cosmos DB coleções não particionadas que estão se aproximando da cota de armazenamento provisionada. Ele recomendará migrar essas coleções para novas coleções com uma definição de chave de partição para que elas possam ser automaticamente dimensionadas pelo serviço. [Saiba mais sobre como escolher uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK do .NET do Azure Cosmos DB para a versão mais recente do Nuget

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do SDK do .NET e recomendará atualizar para a versão mais recente do NuGet para as correções mais recentes, aprimoramentos de desempenho e novos recursos de recurso. [Saiba mais sobre o SDK do .NET Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK do Java do Azure Cosmos DB para a versão mais recente do Maven

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do SDK do Java e recomendará atualizar para a versão mais recente do Maven para as correções mais recentes, melhorias de desempenho e novos recursos de recurso. [Saiba mais sobre o SDK do Java Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o Conector do Spark do Azure Cosmos DB para a versão mais recente do Maven

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do conector Cosmos DB Spark e recomendará atualizar para a versão mais recente do Maven para as correções mais recentes, melhorias de desempenho e novos recursos de recurso. [Saiba mais sobre o conector do Spark Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Habilitar replicação de máquina virtual
As máquinas virtuais que não têm replicação habilitada para outra região não são resilientes a interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso nos negócios durante o tempo de uma interrupção da região do Azure. O Advisor detectará VMs que não têm a replicação habilitada e recomenda habilitar a replicação para que, no caso de uma interrupção, você possa abrir rapidamente suas máquinas virtuais em uma região remota do Azure. [Saiba mais sobre a replicação da máquina virtual](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de alta disponibilidade no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  No painel do Assistente, clique na guia **Alta Disponibilidade**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
