---
title: Planejando eventos de manutenção do Azure
description: Saiba como preparar-se para eventos de manutenção planejada para o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821315"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planejando eventos de manutenção do Azure – Banco de Dados SQL do Azure

Saiba como preparar-se para eventos de manutenção planejada no banco de dados SQL do Azure.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planejada

Para cada banco de dados, o BD SQL do Azure mantém um quorum de réplicas de banco de dados em que uma réplica é a primária. Em todos os momentos, uma réplica primária deve estar fazendo a manutenção online e, pelo menos uma réplica secundária deve ser íntegra. Durante a manutenção planejada, os membros do quorum do banco de dados ficarão offline um por vez com a intenção de que há uma réplica primária respondendo e pelo menos uma réplica secundária online para garantir que não haja tempo de inatividade do cliente. Quando a réplica primária precisa ser colocada offline, ocorrerá um processo de reconfiguração/failover no qual uma réplica secundária se tornará a nova primária.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planejada

Geralmente, as reconfigurações/failovers são concluídos dentro de 30 segundos – a média é de 8 segundos. Se já estiver conectado, seu aplicativo deverá reconectar-se à nova réplica primária da cópia íntegra do seu banco de dados. Se uma nova conexão for tentada enquanto o banco de dados estiver passando por uma reconfiguração antes que a nova réplica primária esteja online, você receberá o erro 40613 (banco de dados indisponível): "o banco de dados ' {DatabaseName} ' no servidor ' {servername} ' não está disponível no momento. Tente a conexão novamente mais tarde.". Se seu banco de dados tiver uma consulta de execução prolongada, ela será interrompida durante uma reconfiguração e precisará ser reiniciada.

## <a name="retry-logic"></a>Lógica de repetição

Qualquer aplicativo de produção cliente que se conecta a um serviço de banco de dados de nuvem deve implementar uma [lógica de repetição](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) de conexão robusta. Isso ajudará a reduzir tais situações e geralmente deverá tornar os erros transparentes para o usuário final.

## <a name="frequency"></a>Frequência

Em média, ocorre 1,7 evento de manutenção planejada a cada mês.

## <a name="resource-health"></a>Integridade de recursos

Se o banco de dados SQL estiver apresentando falhas de logon, verifique o status atual da janela do [Resource Health](../service-health/resource-health-overview.md#get-started) no [portal do Azure](https://portal.azure.com). A seção Histórico de Integridade contém o motivo do tempo de inatividade para cada evento (quando disponível).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Resource Health](sql-database-resource-health.md) para o Banco de Dados SQL
- Para saber mais sobre a lógica de repetição, confira [Retry logic for transient errors](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) (Lógica de repetição para erros transitórios)
