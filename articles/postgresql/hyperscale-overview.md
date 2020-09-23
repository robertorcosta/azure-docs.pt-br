---
title: Visão geral da Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral da opção de implantação Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943199"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>O que é a Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL?

O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional na nuvem da Microsoft projetado para desenvolvedores. Ele é baseado na versão da comunidade do mecanismo de banco de dados [PostgreSQL](https://www.postgresql.org/) de software livre.

A Hiperescala (Citus) é uma opção de implantação que escala horizontalmente as consultas em vários computadores por meio da fragmentação. Seu mecanismo de consulta faz a correspondência entre consultas SQL recebidas nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados. Ele atende aplicativos que exigem maior escala e desempenho do que outras opções de implantação: geralmente, cargas de trabalho que estão se aproximando dos 100 GB de dados ou que já excederam esse limite.

A Hiperescala (Citus) fornece:

- Dimensionamento horizontal entre vários computadores usando a fragmentação
- Consultar a paralelização nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Excelente suporte para aplicativos multilocatário, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para o PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com [bibliotecas de conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="next-steps"></a>Próximas etapas

- Comece pela [criação do seu primeiro](./quickstart-create-hyperscale-portal.md) grupo de servidores da Hiperescala (Citus) do Banco de Dados do Azure para PostgreSQL.
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras. A Hiperescala (Citus) oferece descontos de Instância Reservada pré-paga também; confira as páginas de [preços da Hiperescala em RI](concepts-hyperscale-reserved-pricing.md) para obter detalhes.
- Determinar o melhor [tamanho inicial](howto-hyperscale-scaling.md#picking-initial-size) para seu grupo de servidores
