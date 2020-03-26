---
title: Sincronizar dados do Banco de Dados SQL do Azure no Edge usando a Sincronização de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a sincronização de dados do Banco de Dados SQL do Azure no Edge usando a Sincronização de Dados SQL do Azure
keywords: banco de dados sql no edge,sincronização de dados no banco de dados sql no edge, sincronização de dados do banco de dados sql no edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384175"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sincronizar dados do Banco de Dados SQL no Edge para o Banco de Dados SQL do Azure usando a Sincronização de Dados SQL

Neste tutorial, você aprenderá a usar o *grupo de sincronização* da Sincronização de Dados SQL do Azure para sincronizar de forma incremental dados do Banco de Dados SQL do Azure no Edge para o Banco de Dados SQL do Azure. Sincronização de Dados SQL é um serviço baseado no Banco de Dados SQL do Azure que lhe permite sincronizar os dados que você selecionar bidirecionalmente em vários bancos de dados SQL e instâncias do SQL Server. Para obter mais informações sobre a Sincronização de Dados SQL, confira [Sincronização de Dados SQL do Azure](../sql-database/sql-database-sync-data.md).

Já que o Banco de Dados SQL no Edge é criado nas versões mais recentes do [Mecanismo de Banco de Dados do SQL Server](/sql/sql-server/sql-server-technical-documentation/), qualquer mecanismo de sincronização de dados aplicável a uma instância local do SQL Server também pode ser usado para sincronizar dados de ou para uma instância do Banco de Dados SQL no Edge em execução em um dispositivo de borda.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um computador Windows configurado com o [Data Sync Agent para a Sincronização de Dados SQL do Azure](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Antes de começar

* Criar um banco de dados SQL do Azure. Para saber como criar um Banco de Dados SQL do Azure usando o portal do Azure, confira [Criar um banco de dados individual no Banco de Dados SQL do Azure](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Crie as tabelas e outros objetos necessários na implantação do Banco de Dados SQL do Azure.

* Crie as tabelas e objetos necessários na implantação do Banco de Dados SQL do Azure no Edge. Para saber mais, confira [Como usar pacotes de DAC do Banco de Dados SQL com o Banco de Dados SQL no Edge](stream-analytics.md).

* Registre a instância do Banco de Dados SQL do Azure no Edge com o Data Sync Agent para a Sincronização de Dados SQL do Azure. Para saber mais, confira [Adicionar um Banco de dados do SQL Server local](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Sincronizar dados entre o Banco de Dados SQL do Azure e o Banco de Dados SQL no Edge

A configuração da sincronização entre um Banco de Dados SQL do Azure e uma instância do Banco de Dados SQL no Edge usando a Sincronização de Dados SQL envolve três etapas fundamentais:  

1. Use o portal do Azure para criar um grupo de sincronização. Para saber mais, confira [Criar um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Você pode usar um banco de dados de *hub* para criar vários grupos de sincronização para sincronizar dados de instâncias diferentes do Banco de Dados SQL no Edge para um ou mais Banco de Dados SQL no Azure.

2. Adicione membros de sincronização ao grupo de sincronização. Para obter mais informações, confira [Adicionar membros de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configure o grupo de sincronização para escolher as tabelas que farão parte da sincronização. Para obter mais informações, confira [Configurar um grupo de sincronização](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Depois de concluir as etapas acima, você terá um grupo de sincronização que incluirá um Banco de Dados SQL do Azure e uma instância do Banco de Dados SQL no Edge.

Para saber mais informações sobre a Sincronização de Dados SQL, confira estes artigos:

* [Agente de Sincronização de Dados para a Sincronização de Dados SQL do Azure](../sql-database/sql-database-data-sync-agent.md)

* [Práticas recomendadas](../sql-database/sql-database-best-practices-data-sync.md) e [Como solucionar problemas comuns com a Sincronização de Dados SQL do Azure](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorar a Sincronização de Dados SQL com logs do Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Atualizar o esquema de sincronização com o Transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou o [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um Banco de Dados SQL do Azure no Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Neste tutorial, substitua os detalhes do banco de dados `OnPremiseServer` pelos detalhes do Banco de Dados SQL do Azure no Edge.
