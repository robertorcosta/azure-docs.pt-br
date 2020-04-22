---
title: Arquitetura de conectividade para uma instância gerenciada
description: Saiba mais sobre a arquitetura de comunicação de instância gerenciada pelo Azure SQL Database e a arquitetura de conectividade, bem como como os componentes direcionam o tráfego para a instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e4d6098b7b4de76461e924fc7d42d039046d7ce5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677163"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Arquitetura de conectividade para uma instância gerenciada no Banco de Dados SQL do Azure

Este artigo explica a comunicação em uma instância gerenciada do Azure SQL Database. Ele também descreve a arquitetura de conectividade e como os componentes direcionam o tráfego para a instância gerenciada.  

A instância gerenciada do Banco de Dados SQL é colocada dentro da rede virtual do Azure e da sub-rede dedicada às instâncias gerenciadas. Essa implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de conectar uma rede local a uma instância gerenciada.
- A capacidade de conectar uma instância gerenciada a um servidor vinculado ou a outro armazenamento de dados no local.
- A capacidade de conectar uma instância gerenciada aos recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama a seguir mostra entidades que se conectam a uma instância gerenciada. Também mostra os recursos que precisam se comunicar com a instância gerenciada. O processo de comunicação na parte inferior do diagrama representa aplicativos e ferramentas do cliente que se conectam à instância gerenciada como fontes de dados.  

![Entidades em arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Uma instância gerenciada é uma plataforma como uma oferta de serviço (PaaS). A Microsoft usa agentes automatizados (gerenciamento, implantação e manutenção) para gerenciar esse serviço com base em fluxos de dados de telemetria. Como a Microsoft é responsável pelo gerenciamento, os clientes não podem acessar as máquinas de cluster virtual de instância gerenciada através do Remote Desktop Protocol (RDP).

Algumas operações do SQL Server iniciadas por usuários finais ou aplicativos podem exigir instâncias gerenciadas para interagir com a plataforma. Um caso é a criação de um banco de dados de instâncias gerenciadas. Esse recurso é exposto através do portal Azure, PowerShell, Azure CLI e a API REST.

As instâncias gerenciadas dependem de serviços do Azure, como o Azure Storage para backups, o Azure Event Hubs para telemetria, o Azure Active Directory para autenticação, o Azure Key Vault for Transparent Data Encryption (TDE) e alguns serviços de plataforma Azure que fornecem recursos de segurança e suporte. As instâncias gerenciadas fazem conexões com esses serviços.

Todas as comunicações são criptografadas e assinadas usando certificados. Para verificar a confiabilidade das partes comunicativas, as instâncias gerenciadas verificam constantemente esses certificados através de listas de revogação de certificados. Se os certificados forem revogados, a instância gerenciada fechará as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um nível alto, uma instância gerenciada é um conjunto de componentes de serviço. Esses componentes estão hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede virtual do cliente. Essas máquinas formam um cluster virtual.

Um cluster virtual pode hospedar várias instâncias gerenciadas. Se necessário, o cluster expande ou contrata automaticamente quando o cliente altera o número de instâncias provisionadas na sub-rede.

Os aplicativos do cliente podem se conectar a instâncias gerenciadas e podem consultar e atualizar bancos de dados dentro da rede virtual, rede virtual com peered ou rede conectada por VPN ou Azure ExpressRoute. Esta rede deve usar um ponto final e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Os serviços de gerenciamento e implantação da Microsoft são executados fora da rede virtual. Uma instância gerenciada e os serviços da Microsoft se conectam sobre os pontos finais que possuem endereços IP públicos. Quando uma instância gerenciada cria uma conexão de saída, ao receber nat (Network Address Translation, tradução de endereço de rede) final faz com que a conexão pareça que vem desse endereço IP público.

O tráfego de gerenciamento flui através da rede virtual do cliente. Isso significa que elementos da infra-estrutura da rede virtual podem prejudicar o tráfego de gerenciamento, fazendo com que a instância falhe e se torne indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade de serviços, a Microsoft aplica uma política de intenção de rede em elementos de infra-estrutura de rede virtual do Azure. A política pode afetar o funcionamento da instância gerenciada. Este mecanismo de plataforma comunica de forma transparente os requisitos de rede aos usuários. O principal objetivo da política é evitar a configuração errada da rede e garantir operações normais de instância gerenciada. Quando você exclui uma instância gerenciada, a diretiva de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos dar um mergulho mais profundo na arquitetura de conectividade para instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam a uma instância gerenciada usando um nome host que tem o formulário `<mi_name>.<dns_zone>.database.windows.net`. Esse nome de host se resolve para um endereço IP privado, embora esteja registrado em uma zona pública de DNS (Domain Name System, sistema de nome de domínio público) e seja publicamente solucionável. O `zone-id` é gerado automaticamente quando você cria o cluster. Se um cluster recém-criado hospeda uma instância gerenciada secundária, ele compartilhará seu ID de região com o cluster principal. Para obter mais informações, consulte [Usar grupos de failover automáticos para permitir failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Este endereço IP privado pertence ao balanceador de carga interna da instância gerenciada. O balanceador de carga direciona o tráfego para o gateway da instância gerenciada. Como várias instâncias gerenciadas podem ser executadas dentro do mesmo cluster, o gateway usa o nome de host da instância gerenciada para redirecionar o tráfego para o serviço correto do mecanismo SQL.

Os serviços de gerenciamento e implantação se conectam a uma instância gerenciada usando um [ponto final de gerenciamento](#management-endpoint) que mapeia para um balanceador de carga externo. O tráfego é encaminhado para os nós somente se for recebido em um conjunto predefinido de portas que apenas os componentes de gerenciamento da instância gerenciada usam. Um firewall incorporado nos nós está configurado para permitir o tráfego apenas a partir de faixas IP da Microsoft. Os certificados autenticam mutuamente toda a comunicação entre os componentes de gestão e o plano de gestão.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

A Microsoft gerencia a instância gerenciada usando um ponto final de gerenciamento. Este ponto final está dentro do cluster virtual da instância. O ponto final do gerenciamento é protegido por um firewall incorporado no nível da rede. No nível de aplicação, ele é protegido por verificação de certificado mútuo. Para encontrar o endereço IP do ponto final, consulte [Determine o endereço IP do ponto final do gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as conexões começam dentro da instância gerenciada (como acontece com backups e registros de auditoria), o tráfego parece começar a partir do endereço IP público do ponto final do gerenciamento. Você pode limitar o acesso a serviços públicos a partir de uma instância gerenciada, definindo regras de firewall para permitir apenas o endereço IP da instância gerenciada. Para obter mais informações, [consulte Verificar o firewall incorporado da instância gerenciada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para os serviços do Azure que estão dentro da região da instância gerenciada é otimizado e, por essa razão, não o NATed para gerenciar endereço IP público de gerenciamento de instâncias gerenciadas. Por essa razão, se você precisar usar regras de firewall baseadas em IP, mais comumente para armazenamento, o serviço precisa estar em uma região diferente da instância gerenciada.

## <a name="service-aided-subnet-configuration"></a>Configuração de sub-rede auxiliada por serviço

Para atender aos requisitos de segurança e gerenciamento do cliente, a instância gerenciada está em transição da configuração manual para a sub-rede auxiliada pelo serviço.

Com a configuração de sub-rede auxiliada pelo serviço, o usuário está no controle total do tráfego de dados (TDS), enquanto a Instância Gerenciada assume a responsabilidade de garantir o fluxo ininterrupto do tráfego de gerenciamento para cumprir o SLA.

A configuração de sub-rede auxiliada por serviços se baseia no recurso de delegação de [sub-rede](../virtual-network/subnet-delegation-overview.md) virtual para fornecer gerenciamento automático de configuração de rede e habilitar pontos finais de serviço. Os pontos finais do serviço podem ser usados para configurar regras de firewall de rede virtual em contas de armazenamento que mantêm backups/logs de auditoria.

### <a name="network-requirements"></a>Requisitos de rede 

Implante uma instância gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter essas características:

- **Sub-rede dedicada:** A sub-rede da instância gerenciada não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas a instância gerenciada, e você não pode adicionar outros tipos de recursos posteriormente na sub-rede.
- **Delegação de sub-rede:** A sub-rede da instância gerenciada precisa `Microsoft.Sql/managedInstances` ser delegada ao provedor de recursos.
- **Grupo de segurança de rede (NSG):** Um NSG precisa ser associado à sub-rede da instância gerenciada. Você pode usar um NSG para controlar o acesso ao ponto final de dados da instância gerenciada filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando a instância gerenciada estiver configurada para redirecionar conexões. O serviço irá prover e manter automaticamente [as regras](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) atuais necessárias para permitir o fluxo ininterrupto de tráfego de gerenciamento.
- **Tabela rota definida pelo usuário (UDR):** Uma tabela UDR precisa ser associada à sub-rede da instância gerenciada. Você pode adicionar entradas à tabela de rotas para direcionar o tráfego que tem faixas ip privadas no local como destino através do gateway de rede virtual ou do dispositivo de rede virtual (NVA). O serviço irá provisionar e manter automaticamente [as entradas](#user-defined-routes-with-service-aided-subnet-configuration) atuais necessárias para permitir o fluxo ininterrupto do tráfego de gerenciamento.
- **Endereços IP suficientes:** A sub-rede de instância gerenciada deve ter pelo menos 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [Determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-la para satisfazer [os requisitos de rede para instâncias gerenciadas](#network-requirements). Caso contrário, crie uma [nova rede e uma sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações não conformes na configuração de rede. Depois que a última instância é removida da sub-rede, a diretiva de intenção de rede também é removida.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regras de segurança de entrada obrigatórias com configuração de sub-rede auxiliada por serviço 

| Nome       |Porta                        |Protocolo|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |SUB-REDE DA MI  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |SUB-REDE DA MI  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |SUB-REDE DA MI  |Allow |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE DA MI  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regras obrigatórias de segurança de saída com configuração de sub-rede auxiliada por serviço 

| Nome       |Porta          |Protocolo|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |443, 12000    |TCP     |SUB-REDE DA MI        |AzureCloud |Allow |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Rotas definidas pelo usuário com configuração de sub-rede auxiliada por serviço 

|Nome|Prefixo de endereço|Próximo Salto|
|----|--------------|-------|
|subnet-to-vnetlocal|SUB-REDE DA MI|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*MI SUBNET refere-se ao intervalo de endereçoIP para a sub-rede no formulário x.x.x.x/y. Você pode encontrar essas informações no portal Azure, em propriedades de sub-rede.

Além disso, você pode adicionar entradas à tabela de rotas para direcionar o tráfego que tem faixas de IP privadas no local como destino através do gateway de rede virtual ou do dispositivo de rede virtual (NVA).

Se a rede virtual incluir um DNS personalizado, o servidor DNS personalizado deve ser capaz de resolver registros dns públicos. O uso de recursos adicionais como o Azure AD Authentication pode exigir a resolução de FQDNs adicionais. Para obter mais informações, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Restrições de rede

**O TLS 1.2 é aplicado em conexões de saída**: Em janeiro de 2020, a Microsoft impôs o TLS 1.2 para tráfego intra-serviço em todos os serviços do Azure. Para a instância gerenciada do Azure SQL Database, isso resultou na aplicação do TLS 1.2 nas conexões de saída usadas para replicação e conexões de servidor vinculadas ao SQL Server. Se você estiver usando versões do SQL Server mais antigas do que 2016 com a Instância Gerenciada, certifique-se de que [as atualizações específicas do TLS 1.2 foram aplicadas.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Aseguir recursos de rede virtual não são suportados com instância gerenciada:
- **Peering microsoft**: Permitir que [a Microsoft espione](../expressroute/expressroute-faqs.md#microsoft-peering) em circuitos de rota expressa susceptíamos de forma direta ou transitória com a rede virtual onde a Instância Gerenciada reside afeta o fluxo de tráfego entre componentes de Instância Gerenciada dentro da rede virtual e os serviços que dependem de causar problemas de disponibilidade. Espera-se que as implantações de instâncias gerenciadas para rede virtual com o peering da Microsoft já habilitado falhem.
- **Peering de rede virtual**global : A conectividade [de peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) em todas as regiões do Azure não funciona para instância gerenciada devido a [restrições documentadas do balanceador de carga](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: O uso da [tag de serviço](../virtual-network/service-tags-overview.md) AzurePlatformDNS para bloquear a resolução dNS da plataforma tornaria a Instância Gerenciada indisponível. Embora a Instância Gerenciada suporte dns definido pelo cliente para resolução DNS dentro do motor, há uma dependência do DNS da plataforma para operações da plataforma.
- **Gateway NAT**: O uso do [NAT de rede virtual](../virtual-network/nat-overview.md) para controlar a conectividade de saída com endereço IP público específico tornaria a Instância Gerenciada indisponível. O serviço De instância gerenciada está atualmente limitado ao uso do balanceador de carga básico que não fornece coexistência de fluxos de entrada e saída com o NAT da Rede Virtual.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Preterido] Requisitos de rede sem configuração de sub-rede auxiliada por serviço

Implante uma instância gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter essas características:

- **Sub-rede dedicada:** A sub-rede da instância gerenciada não pode conter qualquer outro serviço de nuvem que esteja associado a ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter nenhum recurso, mas a instância gerenciada, e você não pode adicionar outros tipos de recursos posteriormente na sub-rede.
- **Grupo de segurança de rede (NSG):** Um NSG associado à rede virtual deve definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e de [saída](#mandatory-outbound-security-rules) antes de quaisquer outras regras. Você pode usar um NSG para controlar o acesso ao ponto final de dados da instância gerenciada filtrando o tráfego na porta 1433 e nas portas 11000-11999 quando a instância gerenciada estiver configurada para redirecionar conexões.
- **Tabela rota definida pelo usuário (UDR):** Uma tabela UDR associada à rede virtual deve incluir [entradas](#user-defined-routes)específicas .
- **Sem pontos finais de serviço:** Nenhum ponto final de serviço deve ser associado à sub-rede da instância gerenciada. Certifique-se de que a opção pontos finais de serviço esteja desativada quando você criar a rede virtual.
- **Endereços IP suficientes:** A sub-rede de instância gerenciada deve ter pelo menos 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [Determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de configurá-la para satisfazer [os requisitos de rede para instâncias gerenciadas](#network-requirements). Caso contrário, crie uma [nova rede e uma sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Você não pode implantar uma nova instância gerenciada se a sub-rede de destino não tiver essas características. Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações não conformes na configuração de rede. Depois que a última instância é removida da sub-rede, a diretiva de intenção de rede também é removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatórias

| Nome       |Porta                        |Protocolo|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |SUB-REDE DA MI  |Allow |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE DA MI  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórias

| Nome       |Porta          |Protocolo|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |443, 12000    |TCP     |SUB-REDE DA MI        |AzureCloud |Allow |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |Allow |

> [!IMPORTANT]
> Certifique-se de que há apenas uma regra de entrada para os portos 9000, 9003, 1438, 1440, 1452 e uma regra de saída para as portas 443, 12000. O provisionamento de instâncias gerenciadas através das implantações do Azure Resource Manager falhará se as regras de entrada e saída forem configuradas separadamente para cada porta. Se essas portas estiverem em regras separadas, a implantação falhará com o código de erro`VnetSubnetConflictWithIntendedPolicy`

\*MI SUBNET refere-se ao intervalo de endereçoIP para a sub-rede no formulário x.x.x.x/y. Você pode encontrar essas informações no portal Azure, em propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessárias permitam o tráfego de _qualquer_ fonte nas portas 9000, 9003, 1438, 1440 e 1452, essas portas são protegidas por um firewall incorporado. Para obter mais informações, consulte [Determine the management endpoint address](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se você usar a replicação transacional em uma instância gerenciada e se usar qualquer banco de dados de instância como editor ou distribuidor, abra a porta 445 (saída tcp) nas regras de segurança da sub-rede. Esta porta permitirá o acesso ao compartilhamento de arquivos do Azure.

### <a name="user-defined-routes"></a>rotas definidas pelo usuário

|Nome|Prefixo de endereço|Próximo Salto|
|----|--------------|-------|
|subnet_to_vnetlocal|SUB-REDE DA MI|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [a segurança avançada de dados do SQL Database](sql-database-managed-instance.md).
- Saiba como [configurar uma nova rede virtual Azure](sql-database-managed-instance-create-vnet-subnet.md) ou uma rede virtual [Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde você pode implantar instâncias gerenciadas.
- [Calcule o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde deseja implantar as instâncias gerenciadas.
- Saiba como criar uma instância gerenciada:
  - Do [portal Azure.](sql-database-managed-instance-get-started.md)
  - Usando [o PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando [um modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo do Azure Resource Manager (usando o JumpBox, com SSMS incluído)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
