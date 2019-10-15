---
title: Visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral do serviço de banco de dados relacional Banco de Dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177958"
---
# <a name="what-is-azure-database-for-postgresql"></a>O que é o Banco de Dados do Azure para PostgreSQL?
O Banco de Dados do Azure para PostgreSQL é um serviço de banco de dados relacional na nuvem da Microsoft projetado para desenvolvedores. Ele se baseia na versão da comunidade do mecanismo de banco de dados do [PostgreSQL](https://www.postgresql.org/) de software livre e está disponível em duas opções de implantação: Servidor único e Hiperescala (Citus) (versão prévia).

## <a name="azure-database-for-postgresql---single-server"></a>Banco de Dados do Azure para PostgreSQL – Servidor único
A opção de implantação Servidor Único oferece:

- Alta disponibilidade interna sem nenhum custo adicional (SLA de 99,99%)
- Desempenho previsível, com preços pré-pagos inclusivos
- Escale verticalmente conforme necessário em segundos
- Monitoramento e alertas para avaliar rapidamente o impacto da expansão
- Seguro para proteger dados confidenciais em repouso e em movimento
- Backups automáticos e restauração pontual por até 35 dias
- Segurança e conformidade de nível empresarial

Todos esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de gastar tempo e recursos preciosos no gerenciamento de máquinas virtuais e de infraestrutura. Você pode continuar a desenvolver seu aplicativo usando as ferramentas de software livre e a plataforma de sua escolha, sem precisar aprender novas habilidades.

A opção de implantação Servidor Único oferece três tipos de preço: Básico, Uso Geral e Otimizado para Memória. Cada tipo oferece recursos diferentes para dar suporte a suas cargas de trabalho do banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja  [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) (versão prévia)
A opção Hiperescala (Citus) escala horizontalmente as consultas em vários computadores usando a fragmentação. Seu mecanismo de consulta faz a correspondência entre consultas SQL recebidas nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados. Ele atende aplicativos que exigem maior escala e desempenho, geralmente cargas de trabalho que estão se aproximando, ou já excederam, 100 GB de dados.

A opção de implantação Hiperescala (Citus) oferece:

- Dimensionamento horizontal entre vários computadores usando a fragmentação
- Consultar a paralelização nesses servidores para obter respostas mais rápidas em grandes conjuntos de dados
- Um excelente suporte para aplicativos multilocatário, análise operacional em tempo real e cargas de trabalho transacionais de alta taxa de transferência

Os aplicativos criados para o PostgreSQL podem executar consultas distribuídas em Hiperescala (Citus) com [bibliotecas de conexão](./concepts-connection-libraries.md) padrão e alterações mínimas.

Observe que a Hiperescala (Citus) está em versão prévia pública e como tal, ainda não oferece um SLA.

## <a name="data-security"></a>Segurança de dados
O Banco de Dados do Azure para PostgreSQL detém a tradição dos serviços do banco de dados do Azure de segurança de dados. Ele tem recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam você a monitorar a atividade. Visite a [Central de Confiabilidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/) para obter informações sobre a segurança da plataforma do Azure.

O serviço Banco de Dados do Azure para PostgreSQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada. Por padrão, o serviço do Banco de Dados do Azure para PostgreSQL exige conexões seguras para dados em movimento em toda a rede e entre o banco de dados e o aplicativo cliente.

## <a name="contacts"></a>Contatos
Para perguntas ou sugestões sobre como trabalhar com o Banco de Dados do Azure para PostgreSQL, envie um email para a equipe do Banco de Dados do Azure para PostgreSQL ([@Ask Banco de Dados do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Esse endereço destina-se a perguntas gerais em vez de tíquetes de suporte.

Além disso, considere esses pontos de contato conforme apropriado:
- Para entrar em contato com o Suporte do Azure ou corrigir um problema com sua conta, [arquive um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Próximas etapas
- Consultar a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparações de custo e calculadoras.
- Comece pela criação de seu primeiro [Servidor único](./quickstart-create-server-database-portal.md) ou [Hiperescala (Citus) (versão prévia)](./quickstart-create-hyperscale-portal.md) do Banco de Dados do Azure para PostgreSQL
- Crie seu primeiro aplicativo no Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexão](./concepts-connection-libraries.md)
