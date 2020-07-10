---
title: Perguntas frequentes (FAQ)
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
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171152"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre o [Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Recursos compatíveis

**Onde posso encontrar uma lista de recursos com suporte no SQL Instância Gerenciada?**

Para obter uma lista dos recursos com suporte no SQL Instância Gerenciada, consulte [recursos do sql instância gerenciada do Azure](../database/features-comparison.md).

Para obter diferenças na sintaxe e no comportamento entre o Azure SQL Instância Gerenciada e o SQL Server, consulte [diferenças de T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Especificações técnicas, limites de recursos e outras limitações
 
**Onde posso encontrar características técnicas e limites de recursos para o SQL Instância Gerenciada?**

Para obter as características de geração de hardware disponíveis, consulte [diferenças técnicas em gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para as camadas de serviço disponíveis e suas características, consulte [diferenças técnicas entre as camadas de serviço](resource-limits.md#service-tier-characteristics).

**Para qual camada de serviço eu tenho direito?**

Qualquer cliente está qualificado para qualquer camada de serviço. No entanto, se você quiser trocar suas licenças existentes por tarifas com desconto no Azure SQL Instância Gerenciada usando [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), tenha em mente que os clientes do SQL Server Enterprise Edition com Software Assurance estão qualificados para os níveis de [uso geral](../database/service-tier-general-purpose.md) ou [comercialmente crítico](../database/service-tier-business-critical.md) de desempenho e os clientes do SQL Server Standard Edition com Software Assurance são elegíveis apenas para o nível de desempenho uso geral. Para obter mais detalhes, consulte [direitos específicos do AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Quais tipos de assinatura têm suporte para o SQL Instância Gerenciada?**

Para obter a lista de tipos de assinatura com suporte, consulte [tipos de assinatura com suporte](resource-limits.md#supported-subscription-types). 

**Quais regiões do Azure têm suporte?**

As instâncias gerenciadas podem ser criadas na maioria das regiões do Azure; consulte [regiões com suporte para o SQL instância gerenciada](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se você precisar de uma instância gerenciada em uma região que não tenha suporte atualmente, [envie uma solicitação de suporte por meio do portal do Azure](../database/quota-increase-request.md).

**Há limitações de cota para implantações do SQL Instância Gerenciada?**

A instância gerenciada tem dois limites padrão: limite no número de sub-redes que você pode usar e um limite no número de vCores que você pode provisionar. Os limites variam entre os tipos de assinatura e as regiões. Para obter a lista de limitações de recursos regionais por tipo de assinatura, consulte tabela de [limitação de recursos regionais](resource-limits.md#regional-resource-limitations). Esses são os limites flexíveis que podem ser aumentados sob demanda. Se você precisar provisionar instâncias mais gerenciadas em suas regiões atuais, envie uma solicitação de suporte para aumentar a cota usando o portal do Azure. Para obter mais informações, consulte [aumentos de cota de solicitação para o banco de dados SQL do Azure](../database/quota-increase-request.md).

**Posso aumentar o limite do número de bancos de dados (100) em minha instância gerenciada sob demanda?**

Não, e atualmente não há planos confirmados para aumentar o número de bancos de dados no SQL Instância Gerenciada. 

**Onde posso migrar se tiver mais de 8 TB de dados?**
Você pode considerar a migração para outros tipos do Azure que atendam à sua carga de trabalho: [hiperescala do banco de dados SQL do Azure](../database/service-tier-hyperscale.md) ou [SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Onde posso migrar se tiver requisitos de hardware específicos, como uma taxa de RAM maior para a proporção vCore ou mais CPUs?**
Você pode considerar a migração para [SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ou memória/CPU [do banco de dados SQL do Azure](../database/sql-database-paas-overview.md) otimizada.

## <a name="known-issues--bugs"></a>Problemas conhecidos e bugs

**Onde posso encontrar problemas conhecidos e bugs?**

Para bugs e problemas conhecidos, consulte [problemas conhecidos](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novos recursos

**Onde posso encontrar os recursos mais recentes e os recursos na visualização pública?**

Para obter recursos novos e de visualização, consulte [notas de versão](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Criar, atualizar, excluir ou mover o SQL Instância Gerenciada

**Como posso provisionar o SQL Instância Gerenciada?**

Você pode provisionar uma instância do [portal do Azure](instance-create-quickstart.md), do [PowerShell](scripts/create-configure-managed-instance-powershell.md), [CLI do Azure](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) e [modelos de ARM](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Posso provisionar instâncias gerenciadas em uma assinatura existente?**

Sim, você pode provisionar um Instância Gerenciada em uma assinatura existente se essa assinatura pertencer aos [tipos de assinatura com suporte](resource-limits.md#supported-subscription-types).

**Por que não foi possível provisionar um Instância Gerenciada na sub-rede cujo nome começa com um dígito?**

Essa é uma limitação atual do componente subjacente que verifica o nome da sub-rede em relação ao Regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Todos os nomes que passam o Regex e são nomes de sub-rede válidos atualmente têm suporte.

**Como posso dimensionar minha instância gerenciada?**

Você pode dimensionar sua instância gerenciada no [portal do Azure](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [CLI do Azure](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) ou [modelos ARM](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Posso mover meu Instância Gerenciada de uma região para outra?**

Sim, você pode. Para obter instruções, consulte [mover recursos entre regiões](../database/move-resources-across-regions.md).

**Como posso excluir meu Instância Gerenciada?**

Você pode excluir instâncias gerenciadas por meio do portal do Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0), [CLI do Azure](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) ou [APIs REST do Resource Manager](https://docs.microsoft.com/rest/api/sql/managedinstances/delete).

**Quanto tempo leva para criar ou atualizar uma instância, ou para restaurar um banco de dados?**

O tempo esperado para criar uma nova instância gerenciada ou para alterar as camadas de serviço (vCores, armazenamento) depende de vários fatores. Consulte [operações de gerenciamento](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Convenções de nomenclatura

**Uma instância gerenciada pode ter o mesmo nome que uma instância local SQL Server?**

Não há suporte para a alteração de um nome de instância gerenciada.

**Posso alterar o prefixo da zona DNS?**

Sim, Instância Gerenciada zona DNS padrão *. Database.Windows.net* pode ser alterado. 

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

  
## <a name="networking-requirements"></a>Requisitos de rede 

**Quais são as restrições atuais de NSG de entrada/saída na sub-rede Instância Gerenciada?**

As regras necessárias de NSG e UDR são documentadas [aqui](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)e definidas automaticamente pelo serviço.
Tenha em mente que essas regras são apenas aquelas que precisamos para manter o serviço. Para se conectar à instância gerenciada e usar recursos diferentes, você precisará definir regras adicionais específicas do recurso, que você precisa manter.

**Como posso definir regras de NSG de entrada em portas de gerenciamento?**

O SQL Instância Gerenciada é responsável por definir regras em portas de gerenciamento. Isso é obtido por meio da funcionalidade chamada [configuração de sub-rede auxiliada por serviço](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Isso é para garantir o fluxo ininterrupto do tráfego de gerenciamento a fim de atender a um SLA.

**Posso obter os intervalos de IP de origem que são usados para o tráfego de gerenciamento de entrada?**

Sim. Você pode analisar o tráfego proveniente do grupo de segurança de suas redes [Configurando logs de fluxo do observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

**Posso definir NSG para controlar o acesso ao ponto de extremidade de dados (porta 1433)?**

Sim. Depois que um Instância Gerenciada for provisionado, você poderá definir NSG que controla o acesso de entrada à porta 1433. É aconselhável restringir seu intervalo de IP o máximo possível.

**Posso definir o firewall local ou NVA para filtrar o tráfego de gerenciamento de saída com base em FQDNs?**

Não. Isso não é suportado por vários motivos:
-   O tráfego de roteamento que representa a resposta à solicitação de gerenciamento de entrada seria assimétrico e não pode funcionar.
-   O tráfego de roteamento que vai para o armazenamento seria afetado por restrições de taxa de transferência e latência, de modo que não será possível fornecer a qualidade de serviço esperada e a disponibilidade.
-   Com base na experiência, essas configurações são propensas a erros e não podem ser suportadas.

**Posso definir o NVA ou o firewall para o tráfego de não gerenciamento de saída?**

Sim. A maneira mais simples de conseguir isso é adicionar a regra 0/0 a um UDR associado à sub-rede da instância gerenciada para rotear o tráfego por meio do NVA.
 
**Quantos endereços IP preciso para um Instância Gerenciada?**

A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis. Para determinar o tamanho da sub-rede VNet para o SQL Instância Gerenciada, consulte [determinar o tamanho e o intervalo de sub-rede necessários para instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**E se não houver endereços IP suficientes para executar a operação de atualização de instância?**

Caso não haja [endereços IP](connectivity-architecture-overview.md#network-requirements) suficientes na sub-rede em que sua instância gerenciada é provisionada, você precisará criar uma nova sub-rede e uma nova instância gerenciada dentro dela. Também sugerimos que a nova sub-rede seja criada com mais endereços IP alocados para que operações de atualização futuras evitem situações semelhantes. Depois que a nova instância for provisionada, você poderá fazer backup e restaurar dados manualmente entre as instâncias novas e antigas ou executar a [restauração pontual](point-in-time-restore.md?tabs=azure-powershell)entre instâncias.

**Preciso de uma sub-rede vazia para criar uma Instância Gerenciada?**

Não. Você pode usar uma sub-rede vazia ou uma sub-rede que já contenha Instância Gerenciada (s). 

**Posso alterar o intervalo de endereços de sub-rede?**

Não se houver instâncias gerenciadas dentro do. Esta é uma limitação da infraestrutura de rede do Azure. Você só tem permissão para [adicionar mais espaço de endereço a uma sub-rede vazia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Posso mover minha instância gerenciada para outra sub-rede?**

Não. Essa é uma limitação de design de Instância Gerenciada atual. No entanto, você pode provisionar uma nova instância em outra sub-rede e fazer backup e restaurar dados manualmente entre a instância antiga e a nova ou executar a [restauração pontual](point-in-time-restore.md?tabs=azure-powershell)entre instâncias.

**Preciso de uma rede virtual vazia para criar uma Instância Gerenciada?**

Isso não é necessário. Você pode [criar uma rede virtual para o Azure sql instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) ou [Configurar uma rede virtual existente para o SQL instância gerenciada do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Posso inserir um Instância Gerenciada com outros serviços em uma sub-rede?**

Não. No momento, não há suporte para colocar Instância Gerenciada em uma sub-rede que já contenha outros tipos de recursos.


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


## <a name="dns"></a>DNS

**Posso configurar um DNS personalizado para o SQL Instância Gerenciada?**

Sim. Consulte [como configurar um DNS personalizado para o Azure SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

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


## <a name="security-and-database-encryption"></a>Segurança e criptografia de banco de dados

**A função de servidor sysadmin está disponível para o SQL Instância Gerenciada?**

Sim, os clientes podem criar logons que são membros da função sysadmin.  Os clientes que assumem o privilégio sysadmin também pressupõem a responsabilidade de operar a instância, o que pode afetar negativamente o compromisso do SLA. Para adicionar logon à função de servidor sysadmin, consulte [autenticação do Azure ad](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication).

**Transparent Data Encryption há suporte para Instância Gerenciada do SQL?**

Sim, há suporte para Transparent Data Encryption para SQL Instância Gerenciada. Para obter detalhes, consulte [Transparent Data Encryption para SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal).

**Posso aproveitar o modelo "Traga sua própria chave" para TDE?**

Sim, Azure Key Vault para o cenário BYOK está disponível para Instância Gerenciada do Azure SQL. Para obter detalhes, consulte [Transparent Data Encryption com chave gerenciada pelo cliente](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Posso migrar um banco de dados SQL Server criptografado?**

Sim, você pode. Para migrar um banco de dados SQL Server criptografado, você precisa exportar e importar seus certificados existentes para o Instância Gerenciada e, em seguida, fazer um backup completo do banco de dados e restaurá-lo no Instância Gerenciada. 

Você também pode usar o [serviço de migração de banco de dados do Azure](https://azure.microsoft.com/services/database-migration/) para migrar os bancos de TDE criptografados.

**Como posso configurar a rotação do protetor de TDE para o SQL Instância Gerenciada?**

Você pode girar o protetor de TDE para Instância Gerenciada usando Azure Cloud Shell. Para obter instruções, consulte [Transparent Data Encryption no SQL instância gerenciada usando sua própria chave de Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Posso restaurar meu banco de dados criptografado para o SQL Instância Gerenciada?**

Sim, você não precisa descriptografar seu banco de dados para restaurá-lo para o SQL Instância Gerenciada. Você precisa fornecer um certificado/chave usado como o protetor de chave de criptografia no sistema de origem para o SQL Instância Gerenciada para poder ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- *Carregar o protetor de certificado para o SQL instância gerenciada*. Isso só pode ser feito usando o PowerShell. O [script de exemplo](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Carregue o protetor de chave assimétrica em Azure Key Vault e aponte instância gerenciada SQL para ele*. Essa abordagem é semelhante ao BYOK (traga sua própria chave) TDE caso de uso que também usa a integração Key Vault para armazenar a chave de criptografia. Se você não quiser usar a chave como um protetor de chave de criptografia e apenas quiser disponibilizar a chave para o SQL Instância Gerenciada restaurar bancos de dados criptografados, siga as instruções para [Configurar BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e não marque a caixa de seleção **tornar a chave selecionada o protetor de TDE padrão**.

Depois de disponibilizar o protetor de criptografia para o SQL Instância Gerenciada, você pode prosseguir com o procedimento de restauração do banco de dados padrão.

## <a name="purchasing-models-and-benefits"></a>Modelos e benefícios de compra

**Quais modelos de compra estão disponíveis para o SQL Instância Gerenciada?**

O SQL Instância Gerenciada oferece um [modelo de compra baseado em vCore](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Quais benefícios de custo estão disponíveis para o SQL Instância Gerenciada?**

Você pode economizar custos com os benefícios do SQL Azure das seguintes maneiras:
-   Maximize os investimentos existentes em licenças locais e economize até 55 por cento com [benefício híbrido do Azure](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Confirme a uma reserva de recursos de computação e economize até 33 por cento com [benefício de instância reservada](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Combine isso com o benefício híbrido do Azure para economizar até 82%. 
-   Economize até 55 por cento versus preços de lista com o benefício de preço de desenvolvimento [/teste do Azure](https://azure.microsoft.com/pricing/dev-test/) que oferece tarifas com desconto para suas cargas de trabalho de desenvolvimento e teste em andamento.

**Quem está qualificado para o benefício da instância reservada?**

Para se qualificar para o benefício da instância reservada, o tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para obter mais informações sobre reservas, consulte [benefício da instância reservada](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**É possível cancelar, trocar ou reembolsar reservas?**

Você pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Cobrança para armazenamento de Instância Gerenciada e backup

**Quais são as opções de preços do SQL Instância Gerenciada?**

Para explorar Instância Gerenciada opções de preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Como rastrear o custo de cobrança da minha instância gerenciada?**

Você pode fazer isso usando a [Solução de Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/). Navegue até **Assinaturas** no [portal do Azure](https://portal.azure.com) e selecione **Análise de Custo**. 

Use a opção **Custos acumulados** e, em seguida, filtre pelo **Tipo de recurso** como `microsoft.sql/managedinstances`.

**Quanto custa os backups automatizados?**

Você Obtém a quantidade igual de espaço de armazenamento de backup livre que o espaço de armazenamento de dados reservado adquirido, independentemente do período de retenção de backup definido. Se o consumo de armazenamento de backup estiver dentro do espaço de armazenamento de backup livre alocado, os backups automatizados na instância gerenciada não terão nenhum custo adicional para você, portanto, serão gratuitos. Exceder o uso do armazenamento de backup acima do espaço livre resultará em custos de cerca de $0.20 a $0.24 por GB/mês em regiões dos EUA, ou consulte a página de preços para obter detalhes de sua região. Para obter mais detalhes, confira [consumo de armazenamento de backup explicado](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Como posso monitorar o custo de cobrança do meu consumo de armazenamento de backup?**

Você pode monitorar o custo de armazenamento de backup por meio do portal do Azure. Para obter instruções, consulte [monitorar custos para backups automatizados](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Como posso otimizar meus custos de armazenamento de backup na instância gerenciada?**

Para otimizar os custos de armazenamento de backup, consulte [ajuste de backup fino no SQL instância gerenciada](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

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

## <a name="azure-feedback-and-support"></a>Comentários e suporte do Azure

**Onde posso deixar minhas ideias para melhorias do SQL Instância Gerenciada?**

Você pode votar em um novo recurso de Instância Gerenciada ou em uma nova ideia de aperfeiçoamento sobre a votação no fórum de comentários sobre o [SQL instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance). Dessa forma, você pode contribuir para o desenvolvimento do produto e nos ajudar a priorizar nossas possíveis melhorias.

**Como posso criar uma solicitação de suporte do Azure?**

Para saber como criar uma solicitação de suporte do Azure, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

