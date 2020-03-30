---
title: Configurar alta disponibilidade - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Como ativar ou desativar alta disponibilidade
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977636"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurar hiperescala (Citus) de alta disponibilidade

O Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) fornece alta disponibilidade (HA) para evitar o tempo de inatividade do banco de dados. Com o HA ativado, cada nó em um grupo de servidor receberá uma espera. Se o nó original se tornar insalubre, seu standby será promovido para substituí-lo.

> [!IMPORTANT]
> Como o HA dobra o número de servidores no grupo, também dobrará o custo.

A ativação do HA é possível durante a criação do grupo de servidores ou, posteriormente, na guia **Configurar** para o grupo do servidor no portal Azure. A interface do usuário é semelhante em ambos os casos. Arraste o controle deslizante para **alta disponibilidade** para SIM:

![ha controle deslizante](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Clique no botão **Salvar** para aplicar sua seleção. A habilitação do HA pode levar algum tempo à medida que as disposições do grupo de servidores permanecem em espera e transmite dados para eles.

A **guia Visão geral** para o grupo de servidores listará todos os nós e suas esperas, juntamente com uma coluna de alta **disponibilidade** indicando se o HA está habilitado com sucesso para cada nó.

![a coluna ha em visão geral do grupo de servidores](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [alta disponibilidade.](concepts-hyperscale-high-availability.md)
