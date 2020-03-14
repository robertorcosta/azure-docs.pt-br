---
title: Reduza os custos de serviço usando o Assistente do Azure
description: Use o Azure Advisor para otimizar o custo de suas implantações do Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259689"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduza os custos de serviço usando o Assistente do Azure

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **custo** no painel do Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 

Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. Os modelos de avaliação avançada do Advisor consideram as máquinas virtuais para desligar quando P95th do valor máximo de máximo da utilização da CPU for menor que 3% e a utilização da rede for inferior a 2% em um período de 7 dias. As máquinas virtuais são consideradas para o tamanho certo quando é possível ajustar a carga atual em uma SKU menor (dentro da mesma família de SKUs) ou um número menor de instância, de modo que a carga atual não ultrapasse 80% de utilização quando cargas de trabalho não voltadas para o usuário e não acima de 40% quando a carga de trabalho voltada para o usuário. Aqui, o tipo de carga de trabalho é determinado analisando as características de utilização da CPU da carga de trabalho.

As ações recomendadas são desligadas ou redimensionadas, específicas ao recurso recomendado para o. O Advisor mostra a economia de custos estimada para ações recomendadas – redimensionar ou desligar. Além disso, para a ação recomendada de redimensionamento, o Advisor fornece informações de SKU atuais e de destino. 

Se você quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, poderá ajustar a regra de utilização da CPU por assinatura.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados

O Assistente identifica circuitos do ExpressRoute que estão no status de provedor *Não Provisionado* por mais de um mês e recomenda excluir o circuito se você não estiver planejando provisioná-lo com o seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos excluindo ou reconfigurando gateways de rede virtual ociosos

O Advisor identifica os gateways de rede virtual que ficaram ociosos por mais de 90 dias. Como esses gateways são cobradas por hora, você deverá considerar a reconfiguração ou a exclusão deles se não pretender mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Compre instâncias de máquina virtual reservadas para economizar nos custos de pagamento conforme o uso

O Assistente examinará seu uso da máquina virtual nos últimos 30 dias e determinará se você poderá economizar dinheiro com a compra de uma reserva do Azure. O Assistente mostrará as regiões e os tamanhos nos quais você tem maior potencial de economia e mostrará as economias estimadas com a compra de reservas. Com as reservas do Azure, você pode comprar previamente os custos de base para as máquinas virtuais. Descontos serão aplicados automaticamente a VMs novas ou existentes com o mesmo tamanho e na mesma região que suas reservas. [Saiba mais sobre as Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Advisor também notificará você das instâncias reservadas que você tem que expirarão nos próximos 30 dias. Será recomendável que você compre novas instâncias reservadas para evitar pagar o preço pago conforme o uso.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Exclua endereços IP públicos não associados para economizar dinheiro

O Advisor identifica endereços IP públicos que não estão associados atualmente a recursos do Azure, como balanceadores de carga ou VMs. Esses endereços IP públicos vêm com um custo nominal. Se você não planeja usá-las, excluí-las pode resultar em economia de custos.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Excluir Azure Data Factory pipelines que estão falhando

O Azure Advisor detectará Azure Data Factory pipelines que falham repetidamente e recomendam que você resolva os problemas ou exclua os pipelines com falha se eles não forem mais necessários. Você será cobrado por esses pipelines mesmo se eles não estiverem servindo você enquanto estiverem falhando. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Usar instantâneos padrão para Managed Disks
Para economizar 60% do custo, é recomendável armazenar seus instantâneos no Armazenamento Standard do Azure, independentemente do tipo de armazenamento do disco principal. Essa opção é a opção padrão para instantâneos de Managed Disks. O assistente do Azure identificará os instantâneos armazenados no armazenamento Premium e recomendará migrar seu instantâneo do Premium para o armazenamento Standard. [Saiba mais sobre os preços do disco gerenciado](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Utilizar o gerenciamento do ciclo de vida do dispositivo
O Azure Advisor utilizará inteligência em relação à contagem de objetos do armazenamento de BLOBs do Azure, tamanho total e transações para detectar se uma ou mais de suas contas de armazenamento seriam mais adequadas para permitir o gerenciamento do ciclo de vida para dados de camadas. Ele solicitará que você crie regras de gerenciamento de ciclo de vida para hierarquizar seus dados automaticamente para frio ou arquivo para otimizar os custos de armazenamento e, ao mesmo tempo, manter seus dados no armazenamento de BLOBs do Azure para compatibilidade de aplicativos.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Assistente do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise e selecione [**Advisor**](https://aka.ms/azureadvisordashboard) em qualquer página.

1. No painel do **Advisor** , selecione a guia **custo** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
