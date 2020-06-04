---
title: Como configurar uma instância gerenciada
description: Saiba como configurar e gerenciar a Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 56d605a165281b980f7780c5649cc42ba95f2728
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773382"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Como usar uma Instância Gerenciada do Banco de Dados SQL do Azure

Neste artigo, é possível encontrar vários guias, scripts e explicações que podem ajudá-lo a gerenciar e configurar sua Instância Gerenciada.

## <a name="migration"></a>Migração

- [Migrar para uma Instância Gerenciada](sql-database-managed-instance-migrate.md) – saiba mais sobre o processo de migração recomendado e ferramentas para a migração para uma instância gerenciada.

- [Migrar certificados TDE para uma Instância Gerenciada](sql-database-managed-instance-migrate-tde-certificate.md) – se o banco de dados do SQL Server estiver protegido com a TDE (Transparent Data Encryption), será necessário migrar o certificado que uma Instância Gerenciada pode usar para descriptografar o backup que deseja restaurar no Azure.

## <a name="network-configuration"></a>Configuração de rede

- [Determinar o tamanho da sub-rede de uma Instância Gerenciada](sql-database-managed-instance-determine-size-vnet-subnet.md) – a Instância Gerenciada é colocada em sub-redes dedicadas que não podem ser redimensionadas após adicionar os recursos. Portanto, você precisa calcular qual intervalo de IP de endereços seria necessário para a sub-rede dependendo do número e dos tipos de instâncias que você deseja implantar na sub-rede.
- [Criar nova rede virtual e sub-rede para a Instância Gerenciada](sql-database-managed-instance-create-vnet-subnet.md) – a rede virtual e a sub-rede do Azure em que você deseja implantar suas Instâncias Gerenciadas devem ser configuradas acordo com os [requisitos de rede descritos aqui](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Neste guia, você pode encontrar a maneira mais fácil de criar sua nova rede virtual e sub-rede configuradas corretamente para Instâncias Gerenciadas.
- [Configurar a rede virtual e a sub-rede existentes para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md) – se deseja configurar sua rede virtual e sub-rede existentes para implantar as Instâncias Gerenciadas nelas, aqui você pode encontrar o script que verifica os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) e fazer as configurações na sua sub-rede de acordo com eles.
- [Configurar DNS personalizado](sql-database-managed-instance-custom-dns.md) – você precisa configurar o DNS personalizado caso deseje acessar recursos externos nos domínios personalizados da sua Instância Gerenciada por meio de um servidor vinculado de perfis de email de banco de dados.
- [Sincronizar configurações de rede](sql-database-managed-instance-sync-network-configuration.md) – pode acontecer que, embora tenha [integrado seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md), não seja possível estabelecer a conexão com uma Instância Gerenciada. Algo que você pode é tentar atualizar a configuração de rede para o plano de serviço.
- [Localizar o endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md) – A Instância Gerenciada usa o ponto de extremidade público para fins de gerenciamento. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento usando o script descrito aqui.
- [Verificar a proteção de firewall interno](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – a Instância Gerenciada é protegida com o firewall interno que permite o tráfego apenas nas portas necessárias. Você pode verificar as regras do firewall interno usando o script descrito neste guia.
- [Conectar aplicativos](sql-database-managed-instance-connect-app.md) – a Instância Gerenciada é colocada em sua própria rede virtual privada do Azure com um endereço IP privado. Saiba mais sobre padrões diferentes para conectar os aplicativos à sua Instância Gerenciada.

## <a name="feature-configuration"></a>Configuração de recurso

- A [replicação transacional](replication-with-sql-database-managed-instance.md) permite que você replique seus dados entre Instâncias Gerenciadas ou do SQL Server local para a Instância Gerenciada e vice-versa. Encontre mais informações sobre como usar e configurar a replicação de transação neste guia.
- [Configurar a detecção de ameaças](sql-database-managed-instance-threat-detection.md) – a [detecção de ameaças](sql-database-threat-detection-overview.md) é um recurso interno do Banco de Dados SQL do Azure que detecta vários ataques potenciais, como Injeção de SQL ou o acesso de locais suspeitos. Neste guia, você pode aprender como habilitar e configurar a [detecção de ameaças](sql-database-threat-detection-overview.md) para a Instância Gerenciada.
- A [Criação de alertas](sql-database-managed-instance-alerts.md) permite configurar alertas em métricas monitoradas, como utilização da CPU, consumo de espaço de armazenamento, IOPS e outros para instância gerenciada. Neste guia, você saberá como habilitar e configurar alertas para a instância gerenciada.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Guias de instruções para bancos de dados individuais](sql-database-howto-single-database.md)
