---
title: Reduza os custos de serviço usando o Assistente do Azure
description: Use o Azure Advisor para otimizar o custo de suas implantações do Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259689"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduza os custos de serviço usando o Assistente do Azure

O Advisor ajuda você a otimizar e a reduzir seus gastos gerais com o Azure, identificando recursos ociosos e subutilizados.Você pode obter recomendações de custo na guia **Custo** no painel do Assistente.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto da máquina virtual redimensionando ou desligando instâncias subutilizadas 

Embora alguns cenários de aplicativos possam resultar na baixa utilização por design, normalmente, é possível economizar dinheiro gerenciando o tamanho e o número de máquinas virtuais. Modelos de avaliação avançada do Advisor consideram as máquinas virtuais para desligamento quando o P95º do valor máximo da utilização da CPU é inferior a 3% e a utilização da rede é inferior a 2% durante um período de 7 dias. As máquinas virtuais são consideradas para o tamanho certo quando é possível encaixar a carga atual em um SKU menor (dentro da mesma família SKU) ou um número menor # de instância, de tal forma que a carga atual não ultrapasse 80% de utilização quando não é necessário ter cargas de trabalho voltadas para o usuário e não acima de 40% quando a carga de trabalho voltada para o usuário. Aqui, o tipo de carga de trabalho é determinado analisando as características de utilização da CPU da carga de trabalho.

As ações recomendadas são desligamento ou redimensionamento, específicos para o recurso que está sendo recomendado. O Advisor mostra a economia de custos estimada para ações recomendadas - redimensionar ou desligar. Além disso, para redimensionar a ação recomendada, o Advisor fornece informações sku atuais e alvo. 

Se você quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, você pode ajustar a regra de utilização da CPU por assinatura.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos eliminando circuitos do ExpressRoute não provisionados

O Assistente identifica circuitos do ExpressRoute que estão no status de provedor *Não Provisionado* por mais de um mês e recomenda excluir o circuito se você não estiver planejando provisioná-lo com o seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos excluindo ou reconfigurando gateways de rede virtual ociosos

O Advisor identifica gateways de rede virtuais que estão ociosos há mais de 90 dias. Como esses gateways são cobradas por hora, você deverá considerar a reconfiguração ou a exclusão deles se não pretender mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Compre instâncias de máquina virtual reservadas para economizar nos custos de pagamento conforme o uso

O Assistente examinará seu uso da máquina virtual nos últimos 30 dias e determinará se você poderá economizar dinheiro com a compra de uma reserva do Azure. O Assistente mostrará as regiões e os tamanhos nos quais você tem maior potencial de economia e mostrará as economias estimadas com a compra de reservas. Com as reservas do Azure, você pode comprar previamente os custos de base para as máquinas virtuais. Descontos serão aplicados automaticamente a VMs novas ou existentes com o mesmo tamanho e na mesma região que suas reservas. [Saiba mais sobre as Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Advisor também irá notificá-lo de instâncias reservadas que você tem que expirarão nos próximos 30 dias. Ele recomendará que você compre novas instâncias reservadas para evitar pagar preços pagos.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Exclua endereços IP públicos não associados para economizar dinheiro

O Advisor identifica endereços IP públicos que não estão atualmente associados aos recursos do Azure, como Balanceadores de carga ou VMs. Esses endereços IP públicos vêm com uma carga nominal. Se você não planeja usá-los, excluí-los pode resultar em redução de custos.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Excluir os pipelines do Azure Data Factory que estão falhando

O Azure Data Advisor detectará os pipelines da Fábrica de Dados do Azure que falham repetidamente e recomendará que você resolva os problemas ou exclua os pipelines em falha se eles não forem mais necessários. Você será cobrado por esses oleodutos, mesmo que eles não estejam servindo você enquanto eles estão falhando. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Use instantâneos padrão para discos gerenciados
Para economizar 60% do custo, é recomendável armazenar seus instantâneos no Armazenamento Standard do Azure, independentemente do tipo de armazenamento do disco principal. Esta opção é a opção padrão para snapshots de discos gerenciados. O Azure Advisor identificará instantâneos armazenados no Premium Storage e recomendará migrar seu snapshot do Premium para o Standard Storage. [Saiba mais sobre preços gerenciados de disco](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Utilizar o gerenciamento do ciclo de vida do dispositivo
O Azure Advisor utilizará informações sobre a contagem de objetos de armazenamento do Azure Blob, tamanho total e transações para detectar se uma ou mais de suas contas de armazenamento seriam mais adequadas para permitir o gerenciamento do ciclo de vida para o nível de dados. Ele solicitará que você crie regras de gerenciamento do ciclo de vida para complementar automaticamente seus dados no Cool ou Archive para otimizar seus custos de armazenamento, mantendo seus dados no armazenamento blob do Azure para compatibilidade de aplicativos.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Assistente do Azure

1. Faça login no [portal Azure](https://portal.azure.com).

1. Procure e selecione [**o Advisor**](https://aka.ms/azureadvisordashboard) em qualquer página.

1. No **painel do Advisor,** selecione a guia **Custo.**

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Comece](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional do Orientador](advisor-operational-excellence-recommendations.md)
