---
title: Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: deb720638e0921762eb9c5800d218c3cfccb7f55
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213594"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é o Banco de Dados do Azure para PostgreSQL?
O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional na nuvem da Microsoft projetado para desenvolvedores. Ele se baseia na versão da comunidade do mecanismo de banco de dados do [PostgreSQL](https://www.postgresql.org/) de software livre e está disponível em duas opções de implantação: Servidor único e Hiperescala (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Banco de Dados do Azure para PostgreSQL – Servidor único
A opção de implantação Servidor Único oferece:

- [Alta disponibilidade](concepts-high-availability.md) interna sem nenhum custo adicional (SLA de 99,99%)
- Desempenho previsível, com preços pré-pagos inclusivos
- [Escale verticalmente conforme o necessário](concepts-pricing-tiers.md) em segundos
- [Monitoramento e alertas](concepts-monitoring.md) para avaliar seu servidor
- Segurança e conformidade de nível empresarial
- [Seguro para proteger](concepts-security.md) dados confidenciais em repouso e em movimento
- [Backups automáticos e restauração pontual](concepts-business-continuity.md) por até 35 dias


Todos esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de gastar tempo e recursos preciosos no gerenciamento de máquinas virtuais e de infraestrutura. Você pode continuar a desenvolver seu aplicativo usando as ferramentas de software livre e a plataforma de sua escolha, sem precisar aprender novas habilidades.

A opção de implantação Servidor Único oferece três tipos de preço: Básico, Uso Geral e Otimizado para Memória. Cada tipo oferece recursos diferentes para dar suporte a suas cargas de trabalho do banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja  [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)
A opção Hiperescala (Citus) escala horizontalmente as consultas em vários computadores usando a fragmentação. Seu mecanismo de consulta faz a correspondência entre consultas SQL recebidas nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados. Ele atende aplicativos que exigem maior escala e desempenho, geralmente cargas de trabalho que estão se aproximando, ou já excederam, 100 GB de dados.

A opção de implantação Hiperescala (Citus) oferece:

- Dimensionamento horizontal entre vários computadores usando a fragmentação
- Consultar a paralelização nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Um excelente suporte para aplicativos multilocatário, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para o PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com [bibliotecas de conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

## <a name="contacts"></a>Contatos
Para perguntas ou sugestões sobre como trabalhar com o Banco de Dados do Azure para PostgreSQL, envie um email para a equipe do Banco de Dados do Azure para PostgreSQL ([@Ask Banco de Dados do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Esse endereço destina-se a perguntas gerais em vez de tíquetes de suporte.

Além disso, considere esses pontos de contato conforme apropriado:
- Para entrar em contato com o Suporte do Azure ou corrigir um problema com sua conta, [arquive um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Próximas etapas
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras. As opções de implantação de Servidor Único e de Hiperescala (Citus) também oferecem descontos de instância reservada pré-paga. Confira as páginas de [Preços de RI de Servidor Único](concept-reserved-pricing.md) e [Preços de RI de Hiperescala](concepts-hyperscale-reserved-pricing.md) para obter mais detalhes.
- Comece pela criação de seu primeiro [Servidor único](./quickstart-create-server-database-portal.md) ou [Hiperescala (Citus)](./quickstart-create-hyperscale-portal.md) do Banco de Dados do Azure para PostgreSQL
- Crie seu primeiro aplicativo no Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexão](./concepts-connection-libraries.md)
