---
title: Recursos com suporte do SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre os recursos com suporte do Azure SQL Edge (versão prévia).
keywords: introdução ao SQL no Edge, o que é o SQL no Edge, visão geral do SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 34d2ba05b00ab92066bc7fa3ccd0b7b2aa59e15c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669630"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Recursos com suporte do SQL do Azure no Edge (versão prévia) 

O Azure SQL Edge é criado com base na versão mais recente do Mecanismo de Banco de Dados do Microsoft SQL Server no Linux. Ele dá suporte a um subconjunto dos recursos com suporte no SQL Server 2019 no Linux, além de alguns recursos que atualmente não têm suporte ou estão disponíveis no SQL Server 2019 no Linux (ou no SQL Server no Windows).

Para obter uma lista completa dos recursos com suporte no SQL Server em Linux, consulte [Edições e recursos com suporte do SQL Server 2019 em Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Para edições e recursos com suporte do SQL Server no Windows, consulte [edições e recursos com suporte do SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> O Azure SQL Edge está atualmente em visualização e, como tal, não deve ser usado em ambientes de produção. A Microsoft pode recomendar a execução do Azure SQL Edge em ambientes de produção, dependendo da validação da implantação e de seus cenários de caso de uso.

## <a name="azure-sql-edge-editions"></a>Edições do Azure SQL Edge

O SQL do Azure no Edge está disponível com duas edições ou planos de software diferentes. Essas edições têm conjuntos de recursos idênticos e diferem apenas em termos de seus direitos de uso e da quantidade de memória e núcleos que eles podem acessar no sistema host.

   |**Plano**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor do SQL do Azure no Edge  |  Somente para desenvolvimento. Cada contêiner de desenvolvedor do Azure SQL Edge é limitado a até 4 núcleos e 32 GB de memória.  |
   |SQL do Azure no Edge    |  Para produção. Cada contêiner do Azure SQL Edge é limitado a até 8 núcleos e 64 GB de memória.  |

## <a name="operating-system"></a>Sistema operacional

Os contêineres do Azure SQL Edge atualmente são baseados no Ubuntu 16, 4 e, como tal, só há suporte para execução em hosts do Docker que executam o Ubuntu 16, 4 (recomendado) ou o Ubuntu 18, 4. O Azure SQL Edge também pode ser executado em outros hosts do sistema operacional. Por exemplo, ele pode ser executado em outras distribuições do Linux ou no Windows (usando Docker CE ou Docker EE). No entanto, observe que a Microsoft não testou extensivamente essas configurações.

No momento, o SQL do Azure no Edge só tem suporte para implantação por meio de Azure IoT Edge. Para obter mais informações, consulte [Azure IOT Edge sistemas com suporte](https://docs.microsoft.com/azure/iot-edge/support).

A configuração recomendada para executar o Azure SQL Edge no Windows é configurar uma VM do Ubuntu no host do Windows e, em seguida, executar o Azure SQL Edge dentro da VM do Linux.

## <a name="hardware-support"></a>Suporte a hardware

O Azure SQL Edge requer um processador de 64 bits, que pode ser da Intel, AMD ou ARM, com um mínimo de um processador e um GB de RAM no host. Embora o volume de memória de inicialização do SQL do Azure no Edge esteja perto de 500 MB, a memória adicional é necessária para outros módulos de IoT Edge em execução no dispositivo de borda.

## <a name="azure-sql-edge-components"></a>Componentes do Azure SQL Edge

O Azure SQL Edge só dá suporte ao mecanismo de banco de dados. Ele não inclui suporte para outros componentes disponíveis com o SQL Server 2019 no Windows ou com SQL Server 2019 no Linux. Especificamente, o Azure SQL Edge não dá suporte a componentes SQL Server como Analysis Services, Reporting Services, Integration Services, Master Data Services, Serviços de Machine Learning (no banco de dados) e Machine Learning Server (autônomo).

## <a name="supported-features"></a>Recursos compatíveis

Além de dar suporte a um subconjunto de recursos do SQL Server em Linux, o Azure SQL Edge inclui suporte para os seguintes novos recursos: 

- O streaming do SQL, que se baseia no mesmo mecanismo que dá Azure Stream Analytics, fornece recursos de streaming de dados em tempo real no Azure SQL Edge. 
- A função T-SQL chamada `Date_Bucket` para análise de dados de série temporal.
- Recursos de aprendizado de máquina por meio do tempo de execução do ONNX, incluídos no mecanismo do SQL.

## <a name="unsupported-features"></a>Recursos sem suporte

A lista a seguir inclui os SQL Server 2019 em recursos do Linux que atualmente não têm suporte no Azure SQL Edge.

| Área | Recurso ou serviço sem suporte |
|-----|-----|
| **Design de banco de dados** | OLTP na memória e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico. |
| &nbsp; | `HierarchyID`tipo de dados e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico. |
| &nbsp; | `Spatial`tipo de dados e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico. |
| &nbsp; | Stretch DB e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico. |
| &nbsp; | Índices de texto completo e pesquisa e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico.|
| &nbsp; | `FileTable`, `FILESTREAM` , e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico.|
| **Mecanismo de Banco de Dados** | Replicação. Observe que você pode configurar o Azure SQL Edge como um assinante de push de uma topologia de replicação. |
| &nbsp; | PolyBase. Observe que você pode configurar o Azure SQL Edge como um destino para tabelas externas no polybase. |
| &nbsp; | Extensibilidade de linguagem por meio de Java e Spark. |
| &nbsp; | Integração de Active Directory. |
| &nbsp; | Instantâneos de banco de dados. |
| &nbsp; | Suporte para memória persistente. |
| &nbsp; | Microsoft Coordenador de Transações Distribuídas. |
| &nbsp; | Administrador de recursos e governança de recursos de e/s. |
| &nbsp; | Extensão do pool de buffers. |
| &nbsp; | Consulta distribuída com conexões de terceiros. |
| &nbsp; | Servidores vinculados. |
| &nbsp; | Procedimentos armazenados estendidos do sistema (como `XP_CMDSHELL` ). |
| &nbsp; | Assemblies CLR e comandos DDL relacionados e funções Transact-SQL, exibições de catálogo e exibições de gerenciamento dinâmico. |
| &nbsp; | Funções T-SQL dependentes do CLR, como `ASSEMBLYPROPERTY` , `FORMAT` , `PARSE` e `TRY_PARSE` . |
| &nbsp; | Exibições de catálogo de data e hora dependentes do CLR, funções e cláusulas de consulta. |
| &nbsp; | Extensão do pool de buffers. |
| &nbsp; | Database Mail. |
| **SQL Server Agent** |  Subsistemas: CmdExec, PowerShell, leitor de fila, SSIS, SSAS e SSRS. |
| &nbsp; | Alertas. |
| &nbsp; | Backup gerenciado. |
| **Alta disponibilidade** | grupos de disponibilidade Always On.  |
| &nbsp; | Grupos de disponibilidade básicos. |
| &nbsp; | Always On instância de cluster de failover. |
| &nbsp; | Espelhamento de banco de dados. |
| &nbsp; | Adição de memória e CPU a quente. |
| **Segurança** | Gerenciamento extensível de chaves. |
| &nbsp; | Integração de Active Directory.|
| &nbsp; | Suporte para Secure enclaves.|
| **Serviços** | SQL Server Browser. |
| &nbsp; | Machine Learning por meio de R e Python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Reporting Services. |
| &nbsp; | Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Capacidade de gerenciamento** | Ponto de controle de Utilitário do SQL Server. |

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL do Azure no Edge](deploy-portal.md)
- [Configurar o SQL do Azure no Edge](configure.md)
- [Conectar-se a uma instância do SQL do Azure no Edge usando as ferramentas de cliente SQL Server](connect.md)
- [Backup e restauração de bancos de dados no Azure SQL Edge](backup-restore.md)
