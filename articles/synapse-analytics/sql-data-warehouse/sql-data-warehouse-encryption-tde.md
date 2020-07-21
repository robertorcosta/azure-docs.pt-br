---
title: Transparent Data Encryption (Portal)
description: Transparent Data Encryption (TDE) na análise de Synapse do Azure
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: b530b3a049f41a54ab98cc7d1454018cfc990f75
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495662"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-azure-synapse-analytics"></a>Introdução ao Transparent Data Encryption (TDE) no Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Permissões necessárias

Para habilitar a TDE (Transparent Data Encryption), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia

Para habilitar o TDE, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecionar as configurações do portal de opção de **Transparent Data Encryption** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione a **On** configuração em ![ configurações do portal em](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecione **salvar** 
    ![ configurações do portal salvar](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Desabilitando a criptografia

Para desabilitar o TDE, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecionar as configurações do portal de opção de **Transparent Data Encryption** ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione a **Off** configuração desativar ![ configurações do portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecione **salvar** 
    ![ configuração do portal salvar 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMVs de criptografia

A criptografia pode ser confirmada com as DMVs a seguir:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
