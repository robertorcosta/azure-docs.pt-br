---
title: Configurar a alta disponibilidade-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Como habilitar ou desabilitar a alta disponibilidade
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 0c7702c8832e22d889a5d785dad845430bfb7d17
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116872"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurar alta disponibilidade de Citus (hiperescala)

Banco de dados do Azure para PostgreSQL-o Citus (hiperescala) fornece alta disponibilidade (HA) para evitar tempo de inatividade do banco de dados. Com a HA habilitada, cada nó em um grupo de servidores receberá um modo de espera. Se o nó original se tornar não íntegro, seu modo de espera será promovido para substituí-lo.

> [!IMPORTANT]
> Como a HA duplica o número de servidores no grupo, ele também dobrará o custo.

Habilitar a HA é possível durante a criação do grupo de servidores ou posteriormente na guia **Configurar** do grupo de servidores na portal do Azure. A interface do usuário é semelhante em ambos os casos. Arraste o controle deslizante para **alta disponibilidade** para Sim:

![controle deslizante de alta disponibilidade](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Clique no botão **salvar** para aplicar sua seleção. Habilitar a HA pode levar algum tempo, pois o grupo de servidores provisiona em espera e transmite dados para eles.

A guia **visão geral** do grupo de servidores listará todos os nós e suas esperas, juntamente com uma coluna de **alta disponibilidade** que indica se a ha está habilitada com êxito para cada nó.

![a coluna de alta disponibilidade na visão geral do grupo de servidores](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [alta disponibilidade](concepts-hyperscale-high-availability.md).
