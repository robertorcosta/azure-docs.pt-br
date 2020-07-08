---
title: Perguntas frequentes
titleSuffix: Azure SQL Managed Instance
description: Perguntas frequentes sobre o Azure SQL Instância Gerenciada
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708851"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre o [Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Recursos compatíveis

**Onde posso encontrar uma lista de recursos com suporte no SQL Instância Gerenciada?**

Para obter uma lista dos recursos com suporte no SQL Instância Gerenciada, consulte [recursos do sql instância gerenciada do Azure](../database/features-comparison.md).

Para obter diferenças na sintaxe e no comportamento entre o Azure SQL Instância Gerenciada e o SQL Server, consulte [diferenças de T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Especificações técnicas e limites de recursos
 
**Onde posso encontrar características técnicas e limites de recursos para o SQL Instância Gerenciada?**

Para obter as características de geração de hardware disponíveis, consulte [diferenças técnicas em gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para as camadas de serviço disponíveis e suas características, consulte [diferenças técnicas entre as camadas de serviço](resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemas conhecidos e bugs

**Onde posso encontrar problemas conhecidos e bugs?**

Para bugs e problemas conhecidos, consulte [problemas conhecidos](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novos recursos

**Onde posso encontrar os recursos mais recentes e os recursos na visualização pública?**

Para obter recursos novos e de visualização, consulte [notas de versão](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo leva para criar ou atualizar a instância ou para restaurar um banco de dados?**

Tempo esperado para criar uma instância gerenciada ou alterar a camada de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas [operações de gerenciamento](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Convenções de nomenclatura

**Uma instância gerenciada pode ter o mesmo nome que uma instância local SQL Server?**

Não há suporte para a alteração de um nome de instância gerenciada.

A zona DNS padrão *. Database.Windows.net* para uma instância gerenciada pode ser alterada. 

Para usar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use CliConfig para definir um alias. A ferramenta é apenas um wrapper de configurações do registro, portanto, ela também pode ser feita usando a política de grupo ou um script.
- Use *CNAME* com a opção *TrustServerCertificate = true* .

## <a name="move-a-database-from-sql-managed-instance"></a>Mover um banco de dados do SQL Instância Gerenciada 

**Como posso mover um banco de dados do SQL Instância Gerenciada de volta para o SQL Server ou o banco de dados SQL do Azure?**

Você pode [exportar um banco de dados para BACPAC](../database/database-export.md) e, em seguida, [importar o arquivo BACPAC](../database/database-import.md). Essa é a abordagem recomendada caso o banco de dados seja menor que 100 GB.

A replicação transacional poderá ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

`COPY_ONLY`Backups nativos obtidos do sql instância gerenciada não podem ser restaurados para SQL Server porque o SQL instância gerenciada tem uma versão de banco de dados superior em comparação com SQL Server.

## <a name="migrate-an-instance-database"></a>Migrar um banco de dados de instância

**Como posso migrar meu banco de dados de instância para o banco de dados SQL do Azure?**

Uma opção é [exportar o banco de dados para um BACPAC](../database/database-export.md) e, em seguida, [importar o arquivo BACPAC](../database/database-import.md). 

Essa é a abordagem recomendada caso o banco de dados seja menor que 100 GB. A replicação transacional poderá ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Alternar geração de hardware 

**Posso mudar minha geração de hardware do SQL Instância Gerenciada entre Gen 4 e Gen 5 online?**

A alternância online automatizada entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região em que o SQL Instância Gerenciada for provisionado. Nesse caso, você pode verificar a [página Visão geral do modelo vCore](../database/service-tiers-vcore.md), que explica como alternar entre as gerações de hardware.

Essa é uma operação de execução longa, pois uma nova instância gerenciada será provisionada em segundo plano e os bancos de dados são transferidos automaticamente entre as instâncias antiga e nova, com um failover rápido no final do processo. 

**E se as duas gerações de hardware não tiverem suporte na mesma região?**

Se as duas gerações de hardware não tiverem suporte na mesma região, a alteração da geração de hardware é possível, mas deve ser feita manualmente. Isso exige que você provisione uma nova instância na região onde a geração de hardware desejada esteja disponível e faça backup e restaure manualmente os dados entre as instâncias novas e antigas.

**E se não houver endereços IP suficientes para executar a operação de atualização?**

Caso não haja endereços IP suficientes na sub-rede em que sua instância gerenciada é provisionada, você precisará criar uma nova sub-rede e uma nova instância gerenciada dentro dela. Também sugerimos que a nova sub-rede seja criada com mais endereços IP alocados para que operações de atualização futuras evitem situações semelhantes. (Para o tamanho de sub-rede apropriado, verifique [como determinar o tamanho de uma sub-rede VNet](vnet-subnet-determine-size.md).) Depois que a nova instância for provisionada, você poderá fazer backup e restaurar dados manualmente entre as instâncias novas e antigas ou executar a [restauração pontual](point-in-time-restore.md?tabs=azure-powershell)entre instâncias. 


## <a name="tune-performance"></a>Desempenho de ajuste

**Como fazer ajustar o desempenho do Instância Gerenciada SQL?**

O SQL Instância Gerenciada na camada de Uso Geral usa o armazenamento remoto, portanto, o tamanho dos arquivos de dados e de log é importante para o desempenho. Para obter mais informações, consulte [impacto do tamanho do arquivo de log no desempenho do uso geral SQL instância gerenciada](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se sua carga de trabalho consistir em muitas transações pequenas, considere alternar o tipo de conexão do proxy para o modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo de armazenamento

**Qual é o tamanho máximo de armazenamento para o SQL Instância Gerenciada?**

O tamanho do armazenamento para o SQL Instância Gerenciada depende da camada de serviço selecionada (Uso Geral ou Comercialmente Crítico). Para limitações de armazenamento dessas camadas de serviço, consulte [características da camada de serviço](../database/service-tiers-general-purpose-business-critical.md).

## <a name="backup-storage-cost"></a>Custo do armazenamento de backup 

**O armazenamento de backup foi deduzido do meu armazenamento do SQL Instância Gerenciada?**

Não, o armazenamento de backup não é deduzido do espaço de armazenamento do SQL Instância Gerenciada. O armazenamento de backup é independente do espaço de armazenamento da instância e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter o backup dos bancos de dados de instância, configuráveis de 7 a 35 dias. Para mais detalhes, consulte [Backups automatizados](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Rastrear cobrança

**Há uma maneira de acompanhar meu custo de cobrança para o SQL Instância Gerenciada?**

Você pode fazer isso usando a [Solução de Gerenciamento de Custos do Azure](/azure/cost-management/). Navegue até **Assinaturas** no [portal do Azure](https://portal.azure.com) e selecione **Análise de Custo**. 

Use a opção **Custos acumulados** e, em seguida, filtre pelo **Tipo de recurso** como `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Regras de NSG de entrada

**Como posso definir regras de NSG de entrada em portas de gerenciamento?**

O plano de controle do SQL Instância Gerenciada mantém as regras NSG que protegem as portas de gerenciamento.

Veja quais portas de gerenciamento são usadas para:

As portas 9000 e 9003 são usadas pela infraestrutura de Service Fabric do Azure. A Service Fabric função primária é manter o cluster virtual íntegro e manter o estado da meta em termos do número de réplicas do componente.

As portas 1438, 1440 e 1452 são usadas pelo agente de nó. O agente de nó é um aplicativo que é executado dentro do cluster e é usado pelo plano de controle para executar comandos de gerenciamento.

Além das regras NSG, o firewall interno protege a instância na camada de rede. Na camada de aplicativo, a comunicação é protegida com os certificados.

Para obter mais informações e saber como verificar o firewall interno, consulte [Azure SQL instância gerenciada firewall interno](management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Reduzir riscos de vazamento de dados  

**Como posso reduzir os riscos de vazamento de dados?**

Para reduzir os riscos de vazamento de dados, é recomendável que os clientes apliquem um conjunto de configurações e controles de segurança:

- Ative a [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todos os bancos de dados.
- Desative o CLR (Common Language Runtime). Isso também é recomendado no local.
- Use somente a autenticação Azure Active Directory (AD do Azure).
- Acesse a instância com uma conta de DBA com poucos privilégios.
- Configure o acesso Jumpbox JIT para a conta sysadmin.
- Ative a [auditoria do SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) e integre-a nos mecanismos de alerta.
- Ative a [detecção de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) do pacote do [ADS (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Casos de uso de economia de custos

**Onde posso encontrar casos de uso e economias de custos resultantes com o SQL Instância Gerenciada?**

Estudos de caso do SQL Instância Gerenciada:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Para entender melhor os benefícios, os custos e os riscos associados à implantação do Azure SQL Instância Gerenciada, há também um estudo da Forrester: [o impacto econômico total de Banco de Dados SQL do Microsoft Azure instância gerenciada](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização de DNS 

**Posso fazer a atualização de DNS?**

Atualmente, não fornecemos um recurso para atualizar a configuração do servidor DNS para o SQL Instância Gerenciada.

A configuração de DNS é, por fim, atualizada:

- Quando a concessão de DHCP expirar.
- Na atualização da plataforma.

Como alternativa, faça downgrade do SQL Instância Gerenciada para 4 vCores e atualize-o novamente depois. Isso tem um efeito colateral de atualizar a configuração de DNS.


## <a name="ip-address"></a>Endereço IP

**Posso me conectar ao SQL Instância Gerenciada usando um endereço IP?**

Não há suporte para a conexão com o SQL Instância Gerenciada usando um endereço IP. O nome do host do SQL Instância Gerenciada é mapeado para um balanceador de carga na frente do cluster virtual do SQL Instância Gerenciada. Como um cluster virtual pode hospedar várias instâncias gerenciadas, as conexões não podem ser roteadas para a instância gerenciada apropriada sem especificar o nome explicitamente.

Para obter mais informações sobre a arquitetura de cluster virtual do SQL Instância Gerenciada, consulte [arquitetura de conectividade do cluster virtual](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**O SQL Instância Gerenciada pode ter um endereço IP estático?**

Em situações raras, mas necessárias, talvez seja necessário fazer uma migração online do SQL Instância Gerenciada para um novo cluster virtual. Se necessário, essa migração ocorre devido a alterações em nossa pilha de tecnologia destinadas a melhorar a segurança e a confiabilidade do serviço. A migração para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome de host do SQL Instância Gerenciada. O serviço SQL Instância Gerenciada não alega suporte a endereço IP estático e reserva o direito de alterá-lo sem aviso como parte dos ciclos de manutenção regulares.

Por esse motivo, é altamente recomendável depender da imutabilidade do endereço IP, pois isso poderia causar um tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Alterar fuso horário

**Posso alterar o fuso horário de uma instância gerenciada existente?**

A configuração de fuso horário pode ser definida quando uma instância gerenciada é provisionada pela primeira vez. Não há suporte para a alteração do fuso horário de uma instância gerenciada existente. Para obter detalhes, consulte [limitações de fuso horário](timezones-overview.md#limitations).

As soluções alternativas incluem a criação de uma nova instância gerenciada com o fuso horário adequado e a execução de um backup e restauração manuais, ou o que recomendamos, executando uma [restauração pontual de instância cruzada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como fazer resolver problemas de desempenho com o SQL Instância Gerenciada?**

Para obter uma comparação de desempenho entre o SQL Instância Gerenciada e o SQL Server, um bom ponto de partida são [as práticas recomendadas para comparação de desempenho entre instância gerenciada e SQL Server do SQL do Azure](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

O carregamento de dados é geralmente mais lento no SQL Instância Gerenciada do que no SQL Server devido ao modelo de recuperação completa obrigatório e aos [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) de taxa de transferência de gravação do log de transações. Às vezes, isso pode ser solucionado carregando dados transitórios em tempdb em vez do banco de dados do usuário, ou usando tabelas columnstore clusterizadas ou com otimização de memória.


## <a name="restore-encrypted-backup"></a>Restaurar backup criptografado

**Posso restaurar meu banco de dados criptografado para o SQL Instância Gerenciada?**

Sim, você não precisa descriptografar seu banco de dados para restaurá-lo para o SQL Instância Gerenciada. Você precisa fornecer um certificado/chave usado como o protetor de chave de criptografia no sistema de origem para o SQL Instância Gerenciada para poder ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- *Carregar o protetor de certificado para o SQL instância gerenciada*. Isso só pode ser feito usando o PowerShell. O [script de exemplo](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Carregue o protetor de chave assimétrica em Azure Key Vault e aponte instância gerenciada SQL para ele*. Essa abordagem é semelhante ao BYOK (traga sua própria chave) TDE caso de uso que também usa a integração Key Vault para armazenar a chave de criptografia. Se você não quiser usar a chave como um protetor de chave de criptografia e apenas quiser disponibilizar a chave para o SQL Instância Gerenciada restaurar bancos de dados criptografados, siga as instruções para [Configurar BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e não marque a caixa de seleção **tornar a chave selecionada o protetor de TDE padrão**.

Depois de disponibilizar o protetor de criptografia para o SQL Instância Gerenciada, você pode prosseguir com o procedimento de restauração do banco de dados padrão.

## <a name="migrate-from-sql-database"></a>Migrar do banco de dados SQL 

**Como posso migrar do banco de dados SQL do Azure para o SQL Instância Gerenciada?**

O SQL Instância Gerenciada oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que o banco de dados SQL do Azure. Se você quiser consolidar dados em uma única instância ou simplesmente precisar de um recurso com suporte exclusivo no SQL Instância Gerenciada, poderá migrar seus dados usando a funcionalidade de exportação/importação (BACPAC).

## <a name="password-policy"></a>Política de senha 

**Quais políticas de senha são aplicadas para os logons do SQL Instância Gerenciada SQL?**

A política de senha do SQL Instância Gerenciada para logons SQL herda as políticas da plataforma do Azure que são aplicadas às VMs que formam o cluster virtual que mantém a instância gerenciada. No momento não é possível alterar essas configurações, pois essas configurações são definidas pelo Azure e herdadas pela instância gerenciada.

 > [!IMPORTANT]
 > A plataforma Azure pode alterar os requisitos de política sem notificar os serviços que dependem dessas políticas.

**Quais são as políticas atuais da plataforma Azure?**

Cada logon deve definir sua senha no logon e alterar sua senha depois que atingir a idade máxima.

| **Política** | **Configuração de segurança** |
| --- | --- |
| Duração máxima da senha | 42 dias |
| Duração mínima da senha | 1 dia |
| Comprimento mínimo da senha | 10 caracteres |
| A senha deve atender aos requisitos de complexidade | Habilitada |

**É possível desabilitar a complexidade e a expiração de senha no SQL Instância Gerenciada no nível de logon?**

Sim, é possível controlar CHECK_POLICY e CHECK_EXPIRATION campos no nível de logon. Você pode verificar as configurações atuais executando o seguinte comando T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Depois disso, você pode modificar as configurações de logon especificadas executando:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(substitua ' test ' pelo nome de logon desejado e ajuste a política e os valores de expiração)
