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
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135025"
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

## <a name="connectivity"></a>Conectividade 

**Posso me conectar à minha instância gerenciada usando o endereço IP?**

Não, isso não tem suporte. O nome de host de um Instância Gerenciada é mapeado para o balanceador de carga na frente do cluster virtual do Instância Gerenciada. Como um cluster virtual pode hospedar várias instâncias gerenciadas, uma conexão não pode ser roteada para o Instância Gerenciada apropriado sem especificar seu nome.
Para obter mais informações sobre a arquitetura de cluster virtual do SQL Instância Gerenciada, consulte [arquitetura de conectividade do cluster virtual](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Uma instância gerenciada pode ter um endereço IP estático?**

Não há suporte para esse recurso no momento.

Em situações raras, mas necessárias, talvez seja preciso fazer uma migração online de uma instância gerenciada para um novo cluster virtual. Se necessário, essa migração ocorre devido a alterações em nossa pilha de tecnologia destinadas a melhorar a segurança e a confiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome do host da instância gerenciada. O serviço de instância gerenciada não alega suporte a endereços IP estáticos e reserva o direito de alterá-lo sem aviso como parte dos ciclos de manutenção regulares.

Por esse motivo, é altamente recomendável depender da imutabilidade do endereço IP, pois isso poderia causar um tempo de inatividade desnecessário.

**Instância Gerenciada tem um ponto de extremidade público?**

Sim. Instância Gerenciada tem um ponto de extremidade público que é usado por padrão apenas para o gerenciamento de serviços, mas um cliente também pode habilitá-lo para acesso a dados. Para obter mais detalhes, consulte [usar o SQL instância gerenciada com pontos de extremidade públicos](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). Para configurar o ponto de extremidade público, vá para [Configurar ponto de extremidade público no SQL instância gerenciada](public-endpoint-configure.md).

**Como o Instância Gerenciada controlar o acesso ao ponto de extremidade público?**

Instância Gerenciada controla o acesso ao ponto de extremidade público no nível de rede e de aplicativo.

Os serviços de gerenciamento e implantação se conectam a uma instância gerenciada usando um [ponto de extremidade de gerenciamento](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) que é mapeado para um balanceador de carga externo. O tráfego será roteado para os nós somente se ele for recebido em um conjunto predefinido de portas que apenas os componentes de gerenciamento da instância gerenciada usam. Um firewall interno nos nós é configurado para permitir o tráfego somente de intervalos de IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gerenciamento e o plano de gerenciamento. Para obter mais detalhes, consulte [arquitetura de conectividade para o SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**Eu poderia usar o ponto de extremidade público para acessar os dados em bancos de Instância Gerenciada?**

Sim. O cliente precisará habilitar o acesso a dados de ponto de extremidade público no [portal do Azure](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM e configurar o NSG para bloquear o acesso à porta de dados (número da porta 3342). Para obter mais informações, consulte [Configurar o ponto de extremidade público no Azure sql instância gerenciada](public-endpoint-configure.md) e [usar o sql do Azure instância gerenciada com segurança com o ponto de extremidade público](public-endpoint-overview.md). 

**Posso especificar uma porta personalizada para os pontos de extremidade de dados SQL?**

Não, essa opção não está disponível.  Para o ponto de extremidade de dados privado, Instância Gerenciada usa o número de porta padrão 1433 e para o ponto de extremidade de dados públicos, Instância Gerenciada usa o número de porta padrão 3342.

**Qual é a maneira recomendada para conectar as instâncias gerenciadas colocadas em regiões diferentes?**

O emparelhamento de circuito de rota expressa é a maneira preferida de fazer isso. Isso não deve ser misturado com o emparelhamento de rede virtual entre regiões que não tem suporte devido à [restrição](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)relacionada ao balanceador de carga interno.

Se o emparelhamento de circuito de rota expressa não for possível, a única outra opção é criar uma conexão VPN site a site ([portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal), [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell) [CLI do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)).


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

