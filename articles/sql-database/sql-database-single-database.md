---
title: O que é um banco de dados individual
description: Saiba mais sobre o banco de dados individual no Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79500750"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é um banco de dados individual no Banco de Dados SQL do Azure

A opção de implantação de banco de dados individual cria um banco de dados no Banco de Dados SQL do Azure com seu próprio conjunto de recursos e é gerenciada por meio de um servidor de Banco de Dados SQL. Com um banco de dados individual, cada banco de dados é portátil e fica isolado uns dos outros, cada qual com sua própria camada de serviço dentro do [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou do [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e uma garantia de tamanho da computação.

> [!IMPORTANT]
> O banco de dados individual é uma das três opções de implantação para o Banco de Dados SQL do Azure. As outras duas são [pools elásticos](sql-database-elastic-pool.md) e [instância gerenciada](sql-database-managed-instance.md).
> [!NOTE]
> Para obter um glossário de termos no Banco de Dados SQL do Azure, consulte [Glossário de termos do Banco de Dados SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dimensionamento dinâmico

Você pode criar seu primeiro aplicativo em um banco de dados pequeno e único com baixo custo na camada de computação sem servidor ou em um tamanho de computação pequeno na camada de computação provisionada. Você altera a [camada de computação ou de serviço](sql-database-single-database-scale.md) manualmente ou programaticamente a qualquer momento para atender às necessidades de sua solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

## <a name="single-databases-and-elastic-pools"></a>Bancos de dados individuais e pools elásticos

Um banco de dados individual pode ser movido de ou para um [pool elástico](sql-database-elastic-pool.md) para compartilhamento de recursos. Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Os pools elásticos são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho a um pool em vez de a um banco de dados individual e paga pelos recursos de desempenho coletivo do pool, em vez de pelo desempenho de banco de dados único.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Use as ferramentas de [monitoramento de desempenho interno](sql-database-performance-guidance.md) e [alerta](sql-database-insights-alerts-portal.md) em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho atuais ou de projeto. Além disso, o banco de dados SQL pode [emitir métricas e logs de recursos](sql-database-metrics-diag-logging.md) para facilitar o monitoramento.

## <a name="availability-capabilities"></a>Recursos de disponibilidade

Os bancos de dados individuais, os pools elásticos e as instâncias gerenciadas oferecem muitas características de disponibilidade. Para obter informações, confira [Características de disponibilidade](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor funcionam da mesma maneira no SQL Server e no Banco de Dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste artigo).
Além disso, há alguns recursos e sintaxe sem suporte nenhum, porque o Banco de Dados SQL do Azure foi criado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As opções e instruções Transact-SQL não estão disponíveis se elas configuram opções no nível do servidor, componentes do sistema operacional ou se especificam a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure.

Para obter mais informações, confira [Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Segurança

O Banco de Dados SQL fornece uma variedade de [recursos internos de segurança e conformidade](sql-database-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de conformidade e segurança.

> [!IMPORTANT]
> O banco de dados SQL do Azure (todas as opções de implantação) foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Próximas etapas

- Para começar rapidamente com um único banco de dados, comece com o [Guia de início rápido do banco de dados individual](sql-database-single-database-quickstart-guide.md).
- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md).
