---
title: Grupo de servidores de escala - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Ajuste os recursos de memória, disco e CPU do grupo do servidor para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063111"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Dimensione um grupo de servidores Hyperscale (Citus)

O Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. O portal Azure facilita a adição de novos nódulos de trabalhadores e o aumento dos vCores dos nós existentes.

## <a name="add-worker-nodes"></a>Adicionar nódulos de trabalhador

Para adicionar nós, vá para a guia **Configurar** no grupo de servidor Hyperscale (Citus).  Arrastar o controle deslizante para **contagem de nó trabalhador** altera o valor.

![Controles deslizantes de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **Salvar** para fazer com que o valor alterado faça efeito.

> [!NOTE]
> Uma vez aumentado e salvo, o número de nós do trabalhador não pode ser diminuído usando o controle deslizante.

### <a name="rebalance-shards"></a>Reequilibrar fragmentos

Para aproveitar os nós recém-adicionados, você deve reequilibrar [os fragmentos de](concepts-hyperscale-distributed-data.md#shards)mesa distribuídos, o que significa mover alguns fragmentos dos nós existentes para os novos. Primeiro verifique se os novos trabalhadores terminaram o provisionamento com sucesso. Em seguida, inicie o rebalanceador de fragmentos, conectando-se ao nó do coordenador de cluster com psql e executando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` função reequilibra todas as tabelas no grupo de [colocation](concepts-hyperscale-colocation.md) da tabela nomeada em seu argumento. Assim, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocation.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir vCores em nomes

> [!NOTE]
> Esse recurso está atualmente na visualização. Para solicitar uma alteração nos vCores para nomes de nó sustais no grupo do servidor, entre [em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Além de adicionar novos nódulos, você pode aumentar as capacidades dos nódulos existentes. Ajustar a capacidade computacional para cima e para baixo pode ser útil para experimentos de desempenho, bem como mudanças de curto ou longo prazo nas demandas de tráfego.

Para alterar os vCores para todos os nós do trabalhador, ajuste o controle deslizante **vCores** em **Configuração (por nó do trabalhador)**. Os vCores do nó coordenador podem ser ajustados independentemente. Clique no link **De configuração Alterar** em **nó coordenador**. Uma caixa de diálogo será exibida com controles deslizantes para a capacidade de vCores e Armazenamento do coordenador. Altere os controles deslizantes conforme desejado e selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
