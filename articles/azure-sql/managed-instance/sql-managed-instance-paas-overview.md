---
title: O que é o Azure SQL Instância Gerenciada?
description: Saiba mais sobre como o Azure SQL Instância Gerenciada fornece a compatibilidade quase 100% com o mecanismo de banco de dados mais recente do SQL Server (Enterprise Edition)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 08/14/2020
ms.openlocfilehash: 72d0745e5a885ddbc57a9a849a7537a40e0b1215
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590057"
---
# <a name="what-is-azure-sql-managed-instance"></a>O que é o Azure SQL Instância Gerenciada?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Azure SQL Instância Gerenciada é o serviço de banco de dados de nuvem inteligente e escalonável que combina a mais ampla compatibilidade de mecanismo de banco de dados SQL Server com todos os benefícios de uma plataforma como serviço totalmente gerenciada e verde. O SQL Instância Gerenciada tem quase 100% de compatibilidade com o mecanismo de banco de dados mais recente do SQL Server (Enterprise Edition), fornecendo uma implementação de [rede virtual nativa (VNet)](../../virtual-network/virtual-networks-overview.md) que aborda questões de segurança comuns e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes existentes do SQL Server. O SQL Instância Gerenciada permite que os clientes existentes do SQL Server migrem e shiftem seus aplicativos locais para a nuvem com alterações mínimas de aplicativos e bancos de dados. Ao mesmo tempo, o SQL Instância Gerenciada preserva todos os recursos de PaaS (patches automáticos e atualizações de versão, [backups automatizados](../database/automated-backups-overview.md), [alta disponibilidade](../database/high-availability-sla.md)) que reduzem drasticamente a sobrecarga de gerenciamento e o TCO.

> [!IMPORTANT]
> Para obter uma lista de regiões em que o SQL Instância Gerenciada está disponível no momento, consulte [regiões com suporte](resource-limits.md#supported-regions).

O diagrama a seguir descreve os principais recursos do SQL Instância Gerenciada:

![Principais recursos](./media/sql-managed-instance-paas-overview/key-features.png)

O Azure SQL Instância Gerenciada foi projetado para clientes que buscam migrar um grande número de aplicativos de um ambiente local ou IaaS, autocompilado ou ISV fornecido para um ambiente de nuvem PaaS totalmente gerenciado, com o menor esforço de migração possível. Usando o serviço de [migração de dados do Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)totalmente automatizado, os clientes podem migrar e deslocar sua instância de SQL Server existente para o SQL instância gerenciada, que oferece compatibilidade com SQL Server e isolamento completo de instâncias de clientes com suporte a VNet nativa.  Com o Software Assurance, você pode trocar suas licenças existentes por tarifas com desconto no SQL Instância Gerenciada usando o [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). O SQL Instância Gerenciada é o melhor destino de migração na nuvem para SQL Server instâncias que exigem alta segurança e uma superfície de programação avançada.

## <a name="key-features-and-capabilities"></a>Principais recursos e capacidades

O SQL Instância Gerenciada combina os melhores recursos que estão disponíveis no banco de dados SQL do Azure e no mecanismo de banco de dados do SQL Server.

> [!IMPORTANT]
> O SQL Instância Gerenciada é executado com todos os recursos da versão mais recente do SQL Server, incluindo operações online, correções de plano automáticas e outros aprimoramentos de desempenho corporativo. Uma comparação dos recursos disponíveis é explicada na [comparação de recursos: Azure SQL instância gerenciada versus SQL Server](../database/features-comparison.md).

| **Benefícios de PaaS** | **Continuidade de negócios** |
| --- | --- |
|Sem gerenciamento e compra de hardware <br>Sem sobrecarga de gerenciamento para gerenciar infraestrutura subjacente <br>Rápido provisionamento e dimensionamento de serviço <br>Aplicação de patch automatizado e atualização da versão <br>Integração com outros serviços de dados PaaS |99,99% do SLA de tempo de atividade  <br>[Alta disponibilidade](../database/high-availability-sla.md) interna <br>Dados protegidos com [backups automatizados](../database/automated-backups-overview.md) <br>Período de retenção de backup configurável pelo cliente <br>[Backups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) iniciados pelo usuário <br>Recurso de [restauração pontual do banco de dados](../database/recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado ([integração VNet](connectivity-architecture-overview.md), serviço de locatário único, computação dedicada e armazenamento) <br>[Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure Active Directory (AD do Azure)](../database/authentication-aad-overview.md), suporte de logon único <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Entidades de segurança do servidor do Azure AD (logons)</a>  <br>Segue os padrões de conformidade iguais ao banco de dados SQL do Azure <br>[Auditoria do SQL](auditing-configure.md) <br>[Proteção Avançada contra Ameaças](threat-detection-configure.md) |API do Azure Resource Manager para automatizar o dimensionamento e provisionamento do serviço <br>Funcionalidade do Portal do Azure para dimensionamento e provisionamento manual do serviço <br>Serviço de Migração de Dados

> [!IMPORTANT]
> O Azure SQL Instância Gerenciada foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte as [ofertas de conformidade Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), em que você pode encontrar a lista mais atual de certificações de conformidade do SQL instância gerenciada, listadas em **banco de dados SQL**.

Os principais recursos do SQL Instância Gerenciada são mostrados na tabela a seguir:

|Recurso | Descrição|
|---|---|
| Versão/build SQL Server | Mecanismo de banco de dados SQL Server (mais recente estável) |
| Backups automatizados gerenciados | Sim |
| Métricas e monitoramento de banco de dados e instância interna | Sim |
| Aplicação automática de patches de software | Sim |
| Os recursos mais recentes do mecanismo de banco de dados | Sim |
| Número de arquivos de dados (LINHAS) por banco de dados | Vários |
| Número de arquivos de log (LOG) por banco de dados | 1 |
| VNet - Implantação do Azure Resource Manager | Sim |
| VNet - Modelo de implantação clássico | Não |
| Suporte do Portal | Sim|
| Serviço de Integração Integrado (SSIS) | Não - o SSIS faz parte da [PaaS do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Serviço de análise interna (SSAS) | Não - SSAS é separado [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Serviço interno de relatório (SSRS) | Não-use [Power bi relatórios paginados](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) em vez ou hospede o SSRS em uma VM do Azure. Embora o SQL Instância Gerenciada não possa executar o SSRS como um serviço, ele pode hospedar [bancos de dados de catálogo do SSRS](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) para um servidor de relatórios instalado na máquina virtual do Azure, usando a autenticação SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O [modelo de compra baseado em vCore](../database/service-tiers-vcore.md) para o SQL instância gerenciada oferece flexibilidade, controle, transparência e uma maneira simples de traduzir os requisitos de carga de trabalho local para a nuvem. Este modelo permite que você altere computação, memória e armazenamento com base em suas necessidades de carga de trabalho. O modelo vCore também é elegível para economia de até 55% com o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para SQL Server.

No modelo vCore, você pode escolher entre gerações de hardware.

- As CPUs lógicas **Gen4** são baseadas em processadores Intel® E5-2673 v3 (Haswell) 2,4 GHz, SSD anexado, núcleos físicos, 7 GB de RAM por núcleo e tamanhos de computação entre 8 e 24 vCores.
- As CPUs lógicas **Gen5** são baseadas em processadores Intel® E5-2673 V4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) e Intel® 8272CL (cascadey Lake) 2,5 GHz, SSD Fast NVMe, núcleo lógico Hyper-Threading e tamanhos de computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre as gerações de hardware nos [limites de recursos do SQL instância gerenciada](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Camadas de serviço

O SQL Instância Gerenciada está disponível em duas camadas de serviço:

- **Uso geral**: projetado para aplicativos com desempenho típico e requisitos de latência de e/s.
- **Comercialmente crítico**: projetado para aplicativos com requisitos de latência de e/s baixa e impacto mínimo sobre as operações de manutenção subjacentes na carga de trabalho.

Ambas as camadas de serviço garantem 99,99% de disponibilidade e permitem que você selecione de maneira independente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do Azure SQL Instância Gerenciada, consulte [alta disponibilidade e sql instância gerenciada do Azure](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral

A lista a seguir descreve as principais características da Uso Geral camada de serviço:

- Projetado para a maioria dos aplicativos de negócios com requisitos de desempenho típicos
- Armazenamento de Blobs do Azure de alto desempenho (8 TB)
- [Alta disponibilidade](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) interna com base no armazenamento de BLOBs do Azure confiável e no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Para obter mais informações, consulte [camada de armazenamento nas](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) práticas recomendadas de desempenho de armazenamento e camada de uso geral [e considerações sobre o SQL instância gerenciada (uso geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre as camadas de serviço nos [limites de recursos do SQL instância gerenciada](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica

A camada de serviço Comercialmente Crítico é criada para aplicativos com requisitos de e/s altos. Ele oferece a resiliência mais alta para falhas usando várias réplicas isoladas.

A lista a seguir apresenta as características principais da camada de serviço Comercialmente Crítico:

- Projetada para aplicativos de negócios com requisitos de alta disponibilidade e desempenho mais alto
- Vem com armazenamento SSD local super rápido (até 1 TB no Gen 4 e até 4 TB no Gen 5)
- [Alta disponibilidade](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) interna com base em [grupos de disponibilidade Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Réplica de banco de [dados somente leitura](../database/read-scale-out.md) interna adicional que pode ser usada para relatórios e outras cargas de trabalho somente leitura
- [OLTP In-Memory](../in-memory-oltp-overview.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre as diferenças entre as camadas de serviço nos [limites de recursos do SQL instância gerenciada](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operações de gerenciamento

O Azure SQL Instância Gerenciada fornece operações de gerenciamento que você pode usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não for mais necessária. A explicação detalhada das operações de gerenciamento pode ser encontrada na página [visão geral de operações de gerenciamento de instância gerenciada](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

O SQL Instância Gerenciada vem com recursos avançados de segurança fornecidos pela plataforma Azure e pelo mecanismo de banco de dados SQL Server.

### <a name="security-isolation"></a>Isolamento de segurança

O SQL Instância Gerenciada fornece isolamento de segurança adicional de outros locatários na plataforma do Azure. O isolamento de segurança inclui:

- Implementação e conectividade de [rede virtual nativa](connectivity-architecture-overview.md) para seu ambiente local usando o Azure ExpressRoute ou gateway de VPN.
- Em uma implantação padrão, o ponto de extremidade SQL é exposto somente por meio de um endereço IP privado, permitindo conectividade segura de redes privadas ou do Azure privada.
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para seus aplicativos:

![Alta disponibilidade](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração VNet e a imposição de políticas de rede no nível da sub-rede, confira [Arquitetura de VNet para instâncias gerenciadas](connectivity-architecture-overview.md) e [Conectar seu aplicativo à instância gerenciada](connect-application-instance.md).

> [!IMPORTANT]
> Coloque várias instâncias gerenciadas na mesma sub-rede, onde quer que isso seja permitido pelos seus requisitos de segurança, pois isso trará benefícios adicionais. A colocalização de instâncias na mesma sub-rede simplificará significativamente a manutenção da infraestrutura de rede e reduzirá o tempo de provisionamento da instância, uma vez que uma longa duração de provisionamento é associada ao custo da implantação da primeira instância gerenciada em uma sub-rede.

### <a name="security-features"></a>Recursos de segurança

O Azure SQL Instância Gerenciada fornece um conjunto de recursos de segurança avançados que podem ser usados para proteger seus dados.

- A [auditoria do SQL instância gerenciada](auditing-configure.md) rastreia eventos do banco de dados e os grava em um arquivo de log de auditoria colocado em sua conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.
- A criptografia de dados no Motion-SQL Instância Gerenciada protege seus dados fornecendo criptografia para dados em movimento usando a segurança da camada de transporte. Além da segurança da camada de transporte, o SQL Instância Gerenciada oferece proteção de dados confidenciais em trânsito, em repouso e durante o processamento de consultas com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). O Always Encrypted oferece segurança de dados contra violações envolvendo o roubo de dados críticos. Por exemplo, com o Always Encrypted, números de cartão de crédito são armazenados sempre criptografados no banco de dados, mesmo durante a consulta de processamento, permitindo a descriptografia no ponto de uso por pessoal autorizado ou aplicativos que precisam processar os dados.
- A [proteção avançada contra ameaças](threat-detection-configure.md) complementa a [auditoria](auditing-configure.md) , fornecendo uma camada adicional de inteligência de segurança incorporada ao serviço que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas da Proteção Avançada contra Ameaças podem ser visualizados no [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Eles fornecem detalhes de atividades suspeitas e recomendam ações sobre como investigar e mitigar a ameaça.  
- [Dynamic data masking](/sql/relational-databases/security/dynamic-data-masking) limits sensitive data exposure by masking it to non-privileged users. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. Ele é um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designados, sem que os dados no banco de dados sejam alterados.
- A RLS ( [segurança em nível de linha](/sql/relational-databases/security/row-level-security) ) permite que você controle o acesso a linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta (por exemplo, por associação de grupo ou contexto de execução). A RLS simplifica o design e a codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários possam acessar apenas as linhas de dados pertinentes ao seu departamento ou restringindo o acesso a dados apenas para dados relevantes.
- A [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) criptografa os arquivos de dados do SQL instância gerenciada, conhecidos como criptografia de dados em repouso. A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos arquivos de log e de dados. A criptografia usa uma chave de criptografia de banco de dados (DEK), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação. Você pode proteger todos os seus bancos de dados na instância gerenciada com a transparent data encryption. O TDE é uma tecnologia de criptografia em repouso comprovada em SQL Server que é exigida por muitos padrões de conformidade para proteger contra roubo de mídia de armazenamento.

A migração de um banco de dados criptografado para o SQL Instância Gerenciada é suportada por meio do serviço de migração de banco de dados do Azure ou Se você planeja migrar um banco de dados criptografado usando a restauração nativa, a migração do certificado TDE existente da instância de SQL Server para o SQL Instância Gerenciada é uma etapa necessária. Para obter mais informações sobre opções de migração, consulte [SQL Server migração para o SQL instância gerenciada](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integração do Microsoft Azure Active Directory

O SQL Instância Gerenciada dá suporte a logons e logons tradicionais do mecanismo de banco de dados SQL Server integrados ao Azure AD. As entidades de segurança do servidor do Azure AD (logons) (**Visualização pública**) são uma versão de nuvem do Azure de logons de banco de dados local que você está usando em seu ambiente local. As entidades de segurança do servidor do Azure AD (logons) permitem que você especifique usuários e grupos do seu locatário do Azure AD como entidades de segurança com escopo de instância verdadeiras, capazes de executar qualquer operação em nível de instância, incluindo consultas entre bancos de dados dentro da mesma instância gerenciada.

Uma nova sintaxe é introduzida para criar entidades de segurança do servidor do Azure AD (logons), **do provedor externo**. Para obter mais informações sobre a sintaxe, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">criar logon</a>e examinar o artigo [provisionar um administrador de Azure Active Directory para SQL instância gerenciada](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O SQL Instância Gerenciada permite que você gerencie de forma centralizada identidades de usuários de banco de dados e outros serviços da Microsoft com [integração Azure Active Directory](../database/authentication-aad-overview.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory dá suporte à [autenticação multifator](../database/authentication-mfa-ssms-configure.md) para aumentar a segurança de dados e aplicativos enquanto dá suporte a um processo de logon único.

### <a name="authentication"></a>Autenticação

A autenticação do SQL Instância Gerenciada refere-se à forma como os usuários provam sua identidade ao se conectarem ao banco de dados. O SQL Instância Gerenciada dá suporte a dois tipos de autenticação:  

- **Autenticação do SQL**:

  Este método de autenticação usa um nome de usuário e senha.
- **Autenticação do Active Directory do Azure**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

A autorização refere-se ao que um usuário pode fazer em um banco de dados no Azure SQL Instância Gerenciada e é controlada pelas associações de função de banco de dados da sua conta de usuário e permissões em nível de objeto. O SQL Instância Gerenciada tem os mesmos recursos de autorização que SQL Server 2017.

## <a name="database-migration"></a>Migração de banco de dados

O SQL Instância Gerenciada direciona cenários de usuário com migração de banco de dados em massa de implementações locais ou de banco de dados IaaS. O SQL Instância Gerenciada dá suporte a várias opções de migração de banco de dados:

### <a name="backup-and-restore"></a>Backup e restauração  

A abordagem de migração aproveita backups do SQL para o Armazenamento de Blobs do Azure. Os backups armazenados em um blob de armazenamento do Azure podem ser restaurados diretamente em uma instância gerenciada usando o [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para obter um guia de início rápido mostrando como restaurar a Wide World Importers-arquivo de backup de banco de dados padrão, consulte [restaurar um arquivo de backup para uma instância gerenciada](restore-sample-database-quickstart.md). Este guia de início rápido mostra que você precisa carregar um arquivo de backup no armazenamento de BLOBs do Azure e protegê-lo usando uma chave de assinatura de acesso compartilhado (SAS).
- Para obter informações sobre restauração de URL, consulte [Restauração nativa de URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> Os backups de uma instância gerenciada só podem ser restaurados para outra instância gerenciada. Eles não podem ser restaurados para uma instância do SQL Server ou para o banco de dados SQL do Azure.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e de SQL Server existentes para o Azure SQL Database, Azure SQL Instância Gerenciada e SQL Server na VM do Azure. Veja [como migrar seu banco de dados local para o SQL instância gerenciada usando o serviço de migração de banco de dados](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

O SQL Instância Gerenciada tem o objetivo de fornecer uma compatibilidade de área de superfície próxima a 100% com a versão mais recente do SQL Server por meio de um plano de lançamento em etapas. Para obter uma lista de recursos e comparação, consulte comparação de recursos do [sql instância gerenciada](../database/features-comparison.md)e para obter uma lista de diferenças de t-SQL no SQL Instância Gerenciada versus SQL Server, consulte [SQL instância gerenciada T-SQL differences from SQL Server](transact-sql-tsql-differences-sql-server.md).

O SQL Instância Gerenciada dá suporte à compatibilidade com versões anteriores para SQL Server bancos de dados 2008. Há suporte para a migração direta de servidores de banco de dados SQL Server 2005, e o nível de compatibilidade para bancos SQL Server 2005 migrados é atualizado para SQL Server 2008.
  
O diagrama a seguir descreve a compatibilidade da área de superfície no SQL Instância Gerenciada:  

![Migração](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Principais diferenças entre SQL Server local e SQL Instância Gerenciada

O SQL Instância Gerenciada beneficia-se sempre atualizado na nuvem, o que significa que alguns recursos do SQL Server podem estar obsoletos, ser desativados ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que um recurso específico funciona de forma ligeiramente diferente ou que o serviço está em execução em um ambiente que você não controla totalmente.

Algumas diferenças principais:

- A alta disponibilidade é incorporada e pré-configurada usando uma tecnologia semelhante à [grupos de disponibilidade Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Há apenas backups automatizados e restauração pontual. Os clientes podem iniciar `copy-only` backups que não interferem na cadeia de backup automática.
- A especificação de caminhos físicos completos não tem suporte, portanto, todos os cenários correspondentes precisam ter suporte diferentemente: RESTOre DB não dá suporte ao MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona somente com BLOBs do Azure, etc.
- O SQL Instância Gerenciada dá suporte à [autenticação do Azure ad](../database/authentication-aad-overview.md) como uma alternativa de nuvem para a autenticação do Windows.
- O SQL Instância Gerenciada gerencia automaticamente grupos de arquivos e arquivo XTP para bancos de dados que contêm objetos OLTP na memória.
- O SQL Instância Gerenciada dá suporte a SQL Server Integration Services (SSIS) e pode hospedar um catálogo do SSIS (SSISDB) que armazena pacotes do SSIS, mas eles são executados em um Azure-SSIS Integration Runtime gerenciado (IR) no Azure Data Factory. Consulte [criar Azure-SSIS ir em data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar os recursos do SSIS, consulte [comparar o banco de dados SQL com o SQL instância gerenciada](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Recursos de administração

O SQL Instância Gerenciada permite que os administradores do sistema gastem menos tempo em tarefas administrativas porque o serviço as executa para você ou simplifica bastante essas tarefas. Por exemplo, [instalação de so/RDBMS e aplicação de patches](../database/high-availability-sla.md), [redimensionamento e configuração de instância dinâmica](../database/single-database-scale.md), [backups](../database/automated-backups-overview.md), [replicação de banco](replication-between-two-instances-configure-tutorial.md) de dados (incluindo bancos de dados do sistema), [configuração de alta disponibilidade](../database/high-availability-sla.md)e configuração de fluxos de monitoramento de integridade e [desempenho](../../azure-monitor/insights/azure-sql.md) .

Para obter mais informações, consulte [uma lista de recursos do sql instância gerenciada com suporte e sem suporte](../database/features-comparison.md)e [diferenças de T-SQL entre o SQL Instância Gerenciada e o SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identificar programaticamente uma instância gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact-SQL, que você pode usar para detectar que seu aplicativo está trabalhando com o SQL Instância Gerenciada e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 07/03/2018 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no Banco de Dados SQL. Isso **não** indica o mecanismo SQL versão 12 (SQL Server 2014). O SQL Instância Gerenciada sempre executa a versão mais recente do mecanismo do SQL estável, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente a instância gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS da instância completo no seguinte formato:`<instanceName>`.`<dnsPrefix>`.database.windows.net, em que `<instanceName>` é o nome fornecido pelo cliente, enquanto `<dnsPrefix>` é a parte gerada automaticamente do nome, garantindo exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, consulte [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração da VNet, consulte [configuração da vnet do SQL instância gerenciada](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, consulte [criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o serviço de migração de banco de dados do Azure para migração, consulte [migração de SQL instância gerenciada usando o serviço de migração de banco](../../dms/tutorial-sql-server-to-managed-instance.md)
- Para o monitoramento avançado do desempenho do banco de dados do SQL Instância Gerenciada com inteligência de solução de problemas interna, consulte [monitorar o instância gerenciada SQL do Azure usando o análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
