---
title: Janela de Manutenção
description: Entenda como o banco de dados SQL do Azure e a janela de manutenção Instância Gerenciada podem ser configurados.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/04/2021
ms.openlocfilehash: 0a9a4b2de03c62640bb1c643d3ff3da4139d42a4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101198"
---
# <a name="maintenance-window-preview"></a>Janela de manutenção (visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O recurso janela de manutenção permite a configuração de agendamentos de janela de manutenção previsíveis para o [banco de dados SQL do Azure](sql-database-paas-overview.md) e a [instância gerenciada do SQL](../managed-instance/sql-managed-instance-paas-overview.md). 

Para obter mais informações sobre eventos de manutenção, consulte [planejar eventos de manutenção do Azure no banco de dados SQL do Azure e Azure sql instância gerenciada](planned-maintenance.md).

## <a name="overview"></a>Visão geral

O Azure executa atualizações de manutenção planejadas no banco de dados SQL do Azure e OS recursos do SQL Instância Gerenciada periodicamente, que geralmente incluem atualizações para hardware subjacente, software, incluindo sistema operacional subjacente (SO) e o mecanismo do SQL. Durante uma atualização de manutenção, os recursos estão totalmente disponíveis e acessíveis, mas algumas das atualizações de manutenção exigem um failover, pois o Azure coloca as instâncias offline por um curto período para aplicar as atualizações de manutenção (oito segundos de duração em média).  As atualizações de manutenção planejadas ocorrem uma vez a cada 35 dias em média, o que significa que o cliente pode esperar aproximadamente um evento de manutenção planejada por mês por banco de dados SQL do Azure ou instância gerenciada do SQL e somente durante os slots da janela de manutenção selecionados pelo cliente.   

A janela de manutenção destina-se a cargas de trabalho de negócios que não são resilientes a problemas de conectividade intermitente que podem resultar de eventos de manutenção planejados.

A janela de manutenção pode ser configurada usando o portal do Azure, PowerShell, CLI ou API do Azure. Ele pode ser configurado na criação ou para bancos de dados SQL existentes e instâncias gerenciadas do SQL.

> [!Important]
> A configuração da janela de manutenção é uma operação assíncrona de execução longa, semelhante à alteração da camada de serviço do recurso SQL do Azure. O recurso está disponível durante a operação, exceto um failover curto que ocorre no final da operação e normalmente dura até 8 segundos, mesmo no caso de transações de longa execução interrompidas. Para minimizar o impacto do failover, você deve executar a operação fora do horário de pico.

### <a name="gain-more-predictability-with-maintenance-window"></a>Obter mais previsibilidade com a janela de manutenção

Por padrão, todos os bancos de dados SQL do Azure e os bancos de dados de instância gerenciada são atualizados somente durante 17:00 a 8:00 horas locais diariamente para evitar interrupções de pico no horário comercial. A hora local é determinada pela [região do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que hospeda o recurso. Você pode ajustar ainda mais as atualizações de manutenção para um horário adequado ao seu banco de dados escolhendo entre dois slots de janela de manutenção adicionais:
 
* Janela da semana, 19:10 6h hora local segunda a sexta-feira
* Janela de fim de semana, 19:10 até 6h hora local sexta-feira

Depois que a seleção da janela de manutenção for feita e a configuração do serviço for concluída, todas as atualizações de manutenção planejadas só ocorrerão durante a janela de sua escolha.   

> [!Note]
> Além das atualizações de manutenção planejadas, em raras circunstâncias, eventos de manutenção não planejados podem causar indisponibilidade. 

### <a name="cost-and-eligibility"></a>Custo e qualificação

Configurar e usar a janela de manutenção é gratuito para todos os [tipos de oferta](https://azure.microsoft.com/support/legal/offer-details/)qualificados: pré-pago, provedor de soluções de nuvem (CSP), Microsoft Enterprise ou contrato de cliente da Microsoft.

> [!Note]
> Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Por exemplo, uma assinatura com [tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure via Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)e [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) são ofertas do Azure. Cada oferta ou plano tem diferentes termos e benefícios. Sua oferta ou plano é mostrado na visão geral da assinatura. Para obter mais informações sobre como alternar sua assinatura para uma oferta diferente, consulte [alterar sua assinatura do Azure para uma oferta diferente](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Notificações antecipadas

As notificações de manutenção podem ser configuradas para alertá-lo sobre eventos de manutenção planejada futuros para o banco de dados SQL do Azure 24 horas de antecedência, no momento da manutenção e quando a janela de manutenção for concluída. Para obter mais informações, consulte [notificações antecipadas](advance-notifications.md).

## <a name="availability"></a>Disponibilidade

### <a name="supported-service-level-objectives"></a>Objetivos de nível de serviço com suporte

A escolha de uma janela de manutenção que não seja a padrão está disponível em todos os SLOs **, exceto para**:
* Hiperescala 
* Pools da instância
* VCore Gen4 herdado
* Básico, S0 e S1 
* DC, Fsv2, série M

### <a name="azure-region-support"></a>Suporte de região do Azure

A escolha de uma janela de manutenção diferente do padrão está disponível atualmente nas seguintes regiões:

- Leste da Austrália
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Japan East
- NorthCentral nós
- Norte da Europa
- Centro nós
- Sudeste da Ásia
- Sul do Reino Unido
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Manutenção de gateway para banco de dados SQL do Azure

Para obter o benefício máximo das janelas de manutenção, verifique se os aplicativos cliente estão usando a política de conexão de redirecionamento. Redirecionar é a política de conexão recomendada, em que os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados, levando à latência reduzida e à taxa de transferência aprimorada.  

* No banco de dados SQL do Azure, todas as conexões que usam a política de conexão proxy podem ser afetadas pela janela de manutenção escolhida e uma janela de manutenção de nó de gateway. No entanto, as conexões de cliente usando a política de conexão de redirecionamento recomendada não são afetadas por um failover de manutenção de nó de gateway. 

* Na instância gerenciada do SQL do Azure, os nós de gateway são hospedados [no cluster virtual](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e têm a mesma janela de manutenção que a instância gerenciada, mas usar a política de conexão de redirecionamento ainda é recomendado para minimizar o número de interrupções durante o evento de manutenção.

Para obter mais informações sobre a política de conexão do cliente no banco de dados SQL do Azure, consulte [política de conexão do banco de dados SQL](../database/connectivity-architecture.md#connection-policy) 

Para obter mais informações sobre a política de conexão do cliente na instância gerenciada do SQL do Azure, consulte [tipos de conexão do Azure sql instância gerenciada](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Próximas etapas

* [Notificações antecipadas](advance-notifications.md)
* [Configurar janela de manutenção](maintenance-window-configure.md)

## <a name="learn-more"></a>Saiba mais

* [FAQ da janela de manutenção](maintenance-window-faq.yml)
* [Banco de Dados SQL do Azure](sql-database-paas-overview.md) 
* [Instância Gerenciada de SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planejar eventos de manutenção do Azure no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada](planned-maintenance.md)




