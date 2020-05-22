---
title: Recursos com suporte do SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre os recursos com suporte no SQL do Azure no Edge (versão prévia)
keywords: introdução ao SQL no Edge, o que é o SQL no Edge, visão geral do SQL no Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7d33c2bef1cd0f7bfab4ec3c09f16c049881d1cd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594615"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Recursos com suporte do SQL do Azure no Edge (versão prévia) 

Este artigo fornece detalhes dos recursos com suporte no SQL do Azure no Edge. O SQL do Azure no Edge foi criado com base na versão mais recente do Mecanismo de Banco de Dados do Microsoft SQL Server no Linux e dá suporte a um subconjunto dos recursos com suporte no SQL Server 2019 para Linux, além de alguns recursos que atualmente não têm suporte ou estão disponíveis no SQL Server 2019 no Linux ou no Windows. Para obter uma lista completa dos recursos com suporte no SQL Server em Linux, consulte [Edições e recursos com suporte do SQL Server 2019 em Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Para edições e recursos com suporte do SQL Server no Windows, consulte [Edições e recursos com suporte do SQL Server 2019 (15.x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> O SQL do Azure no Edge está atualmente em versão prévia e, como tal, não deve ser usado em ambientes de produção. A Microsoft poderá recomendar a execução do SQL do Azure no Edge em ambientes de produção após a validação da implantação e dos cenários de caso de uso.

## <a name="azure-sql-edge-editions"></a>Edições do SQL do Azure no Edge

O SQL do Azure no Edge está disponível com duas edições ou planos de software diferentes. Essas edições têm conjuntos de recursos idênticos e diferem apenas em termos de seus direitos de uso e da quantidade de CPU/memória que eles podem acessar no sistema host.

   |**Plano**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor do SQL do Azure no Edge  |  No SKU somente de desenvolvimento, cada contêiner de desenvolvedor do SQL do Azure no Edge é limitado a até 4 núcleos e 32 GB de memória  |
   |SQL do Azure no Edge    |  No SKU de produção, cada contêiner do SQL do Azure no Edge é limitado a até 8 núcleos e 64 GB de memória  |

## <a name="operating-system"></a>Sistema operacional

Os contêineres do SQL do Azure no Edge atualmente são baseados no Ubuntu (16.04 e 18.04) e, como tal, só há suporte para execução em hosts do docker que executam o Ubuntu 16.04 e 18.04. O SQL do Azure no Edge também pode ser executado em outros hosts de sistema operacional, por exemplo, outras distribuições do Linux ou do Windows (usando o Docker CE ou o Docker EE), no entanto, essas configurações não são amplamente testadas pela Microsoft.

No momento, o SQL do Azure no Edge só tem suporte para implantação por meio de Azure IoT Edge. Para obter mais informações sobre os sistemas com suporte para Azure IoT Edge, consulte [Sistemas com suporte para Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support).

A configuração recomendada para executar o SQL do Azure no Edge no Windows é configurar uma VM do Ubuntu no host do Windows e, em seguida, executar o SQL no Edge dentro da VM do Linux.

## <a name="hardware-support"></a>Suporte a hardware

O SQL do Azure no Edge requer um processador de 64 bits, que pode ser da Intel, AMD ou ARM, com um mínimo de um processador e um GB de RAM no host. Embora o volume de memória de inicialização do SQL do Azure no Edge esteja perto de 500 MB, a memória adicional é necessária para outros módulos de IoT Edge em execução no dispositivo de borda.

## <a name="azure-sql-edge-components"></a>Componentes do SQL do Azure no Edge

O SQL do Azure no Edge só dá suporte ao mecanismo de banco de dados e não inclui suporte para outros componentes disponíveis com o SQL Server 2019 no Windows ou com o SQL Server 2019 no Linux. Especificamente, o SQL do Azure no Edge não dá suporte a componentes do SQL Server como o Analysis Services, Reporting Services, Integration Services, Master Data Services, Serviços de Machine Learning (no banco de dados) e Machine Learning Server (autônomo).

## <a name="supported-features"></a>Recursos com suporte

Além de oferecer suporte a um subconjunto de recursos do SQL Server em Linux, o SQL do Azure no Edge inclui suporte para os novos recursos a seguir. 

- O streaming do SQL, que se baseia no mesmo mecanismo que fornece o Azure Stream Analytics, fornece recursos de streaming de dados em tempo real no SQL do Azure no Edge. 
- Nova chamada de função T-SQL Date_Bucket para análise de dados de série temporal.
- Recursos de Machine Learning por meio do tempo de execução do ONNX, incluídos no mecanismo do SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Recursos do SQL Server em Linux que não oferecem suporte no SQL do Azure no Edge

A lista a seguir inclui os recursos do SQL Server 2019 em Linux que atualmente não têm suporte no Azure SQL Edge.

| Área | Recurso ou serviço sem suporte |
|-----|-----|
| **Design de banco de dados** | OLTP na memória, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico |
| &nbsp; | Tipo de dados HierarchyID e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico |
| &nbsp; | Tipo de dados espaciais, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico |
| &nbsp; | Stretch Database, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico |
| &nbsp; | Índices de texto completo e de pesquisa, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico|
| &nbsp; | FileTable, FILESTREAM, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico|
| **Mecanismo de Banco de Dados** | Replicação. No entanto, o SQL do Azure no Edge pode ser configurado como um assinante de envio por push de uma topologia de replicação. |
| &nbsp; | PolyBase. No entanto, o SQL do Azure no Edge pode ser configurado como um destino para tabelas externas no Polybase. |
| &nbsp; | Extensibilidade de linguagem por meio de Java e Spark |
| &nbsp; | Integração do Active Directory |
| &nbsp; | Instantâneos do banco de dados |
| &nbsp; | Suporte para memória persistente |
| &nbsp; | Coordenador de Transações Distribuídas da Microsoft |
| &nbsp; | Resource Governor e Governança de recursos de E/S |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | Consulta distribuída com conexões de terceiros |
| &nbsp; | Servidores vinculados |
| &nbsp; | Procedimentos armazenados estendidos do sistema (XP_CMDSHELL etc.) |
| &nbsp; | Assemblies CLR, comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico |
| &nbsp; | Funções de T-SQL dependentes do CLR, como ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE |
| &nbsp; | Exibições de catálogo de data e hora dependentes do CLR, funções e cláusulas de consulta |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | Database Mail |
| **SQL Server Agent** |  Subsistemas: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Alertas |
| &nbsp; | Backup Gerenciado |
| **Alta disponibilidade** | Grupos de Disponibilidade AlwaysOn  |
| &nbsp; | Grupos básicos de disponibilidade |
| &nbsp; | Instância do cluster de failover do Always On |
| &nbsp; | Espelhamento de banco de dados |
| &nbsp; | Adição de memória a quente e CPU |
| **Segurança** | Gerenciamento Extensível de Chaves |
| &nbsp; | Integração do Active Directory|
| &nbsp; | Suporte para enclaves seguros|
| **Serviços** | SQL Server Browser |
| &nbsp; | Machine Learning por meio de R e Python |
| &nbsp; | StreamInsight |
| &nbsp; | Serviços de análise |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Capacidade de gerenciamento** | Ponto de controle do Utilitário do SQL Server |

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL do Azure no Edge](deploy-portal.md)
- [Configurar o SQL do Azure no Edge](configure.md)
- [Conectar-se a uma instância do SQL do Azure no Edge usando as ferramentas de cliente SQL Server](connect.md)
- [Backup e restauração de bancos de dados no SQL do Azure no Edge](backup-restore.md)
