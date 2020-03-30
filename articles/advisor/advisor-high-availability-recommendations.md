---
title: Melhorar a disponibilidade do aplicativo com o Assistente do Azure
description: Use o Azure Advisor para melhorar a alta disponibilidade das implantações do Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443101"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhorar a disponibilidade do aplicativo com o Assistente do Azure

O Assistente do Azure ajuda a garantir e melhorar a continuidade de aplicativos críticos aos negócios. Você pode obter recomendações de alta disponibilidade do Advisor na guia **Alta Disponibilidade** do painel Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Assegurar tolerância a falhas de máquina virtual

Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, será preciso adicionar pelo menos mais uma máquina virtual nele. É recomendável agrupar duas ou mais máquinas virtuais em um conjunto de disponibilidade para garantir que pelo menos uma delas esteja disponível durante uma interrupção.

## <a name="ensure-availability-set-fault-tolerance"></a>Assegure que haja tolerância a falhas de conjunto de disponibilidade

Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. O Assistente identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais a ele.Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure.Você pode optar por criar uma máquina virtual ou então por adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

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

Na configuração ativa, ambas as instâncias de um gateway VPN estabelecerão túneis Vpn S2S para o seu dispositivo VPN no local. Quando um evento de manutenção planejada ou um evento não planejado ocorrer em uma instância do gateway, o tráfego será mudado para outro túnel IPsec ativo automaticamente. O Assistente do Azure identificará os gateways de VPN que não estão configurados como ativo-ativo e sugerirá que você os configure para alta disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Use gateways VPN de produção para executar suas cargas de trabalho de produção

O Azure Advisor verificará se há gateways VPN que sejam um SKU básico e recomendará que você use um SKU de produção em vez disso. O SKU Básico foi projetado para fins de desenvolvimento e teste. Os SKUs de produção oferecem um maior número de túneis, suporte a BGP, opções de configuração ativa, política Ipsec/IKE personalizada e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detectará regras de alerta que tenham consultas inválidas especificadas em sua seção de condições. Regras de alerta de log são criadas no Azure Monitor e são usadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinarão se um alerta precisar ser disparado. Consultas de análise podem se tornar inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que você corrija a consulta na regra de alerta para evitar que ela seja desativada automaticamente e garanta a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre regras de alerta para solução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configure o modo de indexação consistente na coleção Cosmos DB

Os recipientes Azure Cosmos DB configurados com o modo de indexação Lazy podem afetar o frescor dos resultados da consulta. O Advisor detectará os recipientes configurados desta forma e recomendará a mudança para o modo Consistente. [Saiba mais sobre políticas de indexação no Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contêineres do Azure Cosmos DB com uma chave de partição

O Azure Advisor identificará coleções não particionadas do Azure Cosmos DB que estão se aproximando de sua cota de armazenamento provisionada. Ele recomendará migrar essas coleções para novas coleções com uma definição de chave de partição para que elas possam ser automaticamente dimensionadas pelo serviço. [Saiba mais sobre a escolha de uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK do .NET do Azure Cosmos DB para a versão mais recente do Nuget

O Azure Advisor identificará as contas do Azure Cosmos DB que estão usando versões antigas do .NET SDK e recomendará a atualização para a versão mais recente do Nuget para as correções mais recentes, melhorias de desempenho e novos recursos. [Saiba mais sobre cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK do Java do Azure Cosmos DB para a versão mais recente do Maven

O Azure Advisor identificará as contas do Azure Cosmos DB que estão usando versões antigas do Java SDK e recomendará a atualização para a versão mais recente da Maven para as correções mais recentes, melhorias de desempenho e novos recursos. [Saiba mais sobre o Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o Conector do Spark do Azure Cosmos DB para a versão mais recente do Maven

O Azure Advisor identificará as contas do Azure Cosmos DB que estão usando versões antigas do conector Cosmos DB Spark e recomendará a atualização para a versão mais recente da Maven para as correções mais recentes, melhorias de desempenho e novos recursos. [Saiba mais sobre o conector Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Habilitar replicação de máquina virtual
Máquinas virtuais que não possuem replicação habilitada para outra região não são resistentes a paralisações regionais. Replicar máquinas virtuais reduz qualquer impacto adverso aos negócios durante o tempo de paralisação da região do Azure. O Advisor detectará VMs que não possuem replicação ativada e recomendará a replicação para que, em caso de paralisação, você possa trazer rapidamente suas máquinas virtuais em uma região remota do Azure. [Saiba mais sobre a replicação de máquinas virtuais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

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
* [Recomendações de Excelência Operacional do Orientador](advisor-operational-excellence-recommendations.md)
