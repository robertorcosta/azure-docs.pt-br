---
title: Dimensionar grupo de servidores-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Ajuste os recursos de memória do grupo de servidores, disco e CPU para lidar com o aumento de carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 10d253b3e470ea009944d4ccd756d4bbbe3766e7
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029810"
---
# <a name="server-group-size"></a>Tamanho do grupo de servidores

A opção de implantação de hiperescala (Citus) usa servidores de banco de dados de cooperabilidade para paralelizar a execução da consulta e armazenar mais dados. O grupo de servidores "tamanho" refere-se ao número de servidores e aos recursos de hardware de cada um.

## <a name="picking-initial-size"></a>Tamanho inicial da separação

O tamanho de um grupo de servidores, em termos de número de nós e sua capacidade de hardware, é fácil de alterar ([Veja abaixo](#scale-a-hyperscale-citus-server-group)). No entanto, você ainda precisa escolher um tamanho inicial para um novo grupo de servidores. Aqui estão algumas dicas para uma escolha razoável.

### <a name="multi-tenant-saas-use-case"></a>Caso de uso de SaaS de vários locatários

Para as migrações para Citus (hiperescala) de uma instância de banco de dados PostgreSQL de nó único existente, é recomendável escolher um cluster em que o número de vCores de trabalho e RAM no total seja igual ao da instância original. Nesses cenários, vimos aprimoramentos de desempenho de 2 a 3, pois a fragmentação melhora a utilização de recursos, permitindo índices menores etc.

O número de vCores necessário para o nó de coordenador depende da carga de trabalho existente (taxa de transferência de gravação/leitura). O nó de coordenador não exige tanta RAM quanto nós de trabalho, mas a alocação de RAM é determinada com base na contagem de vCore (conforme descrito nas [Opções de configuração de hiperescala](concepts-hyperscale-configuration-options.md)) para que a contagem VCORE seja essencialmente a decisão real.

### <a name="real-time-analytics-use-case"></a>Caso de uso de análise em tempo real

Total de vCores: quando os dados de trabalho se ajustam à RAM, você pode esperar uma melhoria de desempenho linear em hiperescala (Citus) proporcional ao número de núcleos de trabalho. Para determinar o número certo de vCores para suas necessidades, considere a latência atual para consultas em seu banco de dados de nó único e a latência necessária em hiperescala (Citus). Divida a latência atual pela latência desejada e arredonde o resultado.

RAM de trabalho: o melhor caso seria fornecer memória suficiente para que a maior parte do conjunto de trabalho caiba na memória. O tipo de consultas que seu aplicativo usa afeta os requisitos de memória. Você pode executar explicar analisar em uma consulta para determinar a quantidade de memória necessária. Lembre-se de que vCores e RAM são dimensionados juntos, conforme descrito no artigo [Opções de configuração de hiperescala](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Dimensionar um grupo de servidores de hiperescala (Citus)

Banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. A portal do Azure facilita a adição de novos nós de trabalho e o aumento da vCores dos nós existentes. Adicionar nós não causa nenhum tempo de inatividade e até mesmo mover fragmentos para os novos nós (chamados de [rebalanceamento de fragmentos](#rebalance-shards)) ocorre sem interromper as consultas.

### <a name="add-worker-nodes"></a>Adicionar nós de trabalho

Para adicionar nós, vá para a guia **computação + armazenamento** em seu grupo de servidores de hiperescala (Citus).  Arrastar o controle deslizante para **contagem de nós de trabalho** altera o valor.

![Controles deslizantes de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **salvar** para fazer o valor alterado entrar em vigor.

> [!NOTE]
> Depois de aumentado e salvo, o número de nós de trabalho não pode ser reduzido usando o controle deslizante.

#### <a name="rebalance-shards"></a>Reequilibrar fragmentos

Para aproveitar os nós recém-adicionados, você deve reequilibrar os [fragmentos](concepts-hyperscale-distributed-data.md#shards)de tabela distribuída, o que significa mover alguns fragmentos de nós existentes para os novos. Primeiro, verifique se os novos trabalhadores concluíram com êxito o provisionamento. Em seguida, inicie o rebalanceador de fragmento, conectando-se ao nó de coordenador de cluster com psql e executando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` função reequilibra todas as tabelas no grupo de [colocalização](concepts-hyperscale-colocation.md) da tabela chamada em seu argumento. Portanto, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocalização.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir o vCores em nós

> [!NOTE]
> Esse recurso está atualmente na visualização. Para solicitar uma alteração no vCores para nós em seu grupo de servidores, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Além de adicionar novos nós, você pode aumentar os recursos dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para testes de desempenho, bem como alterações de curto ou longo prazo em demandas de tráfego.

Para alterar o vCores de todos os nós de trabalho, ajuste o controle deslizante **vCores** em **configuração (por nó de trabalho)**. O vCores do nó de coordenador pode ser ajustado de forma independente. Ajuste o controle deslizante **vCores** em  **configuração (nó de coordenador)**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
