---
title: Criptografia de dados transparente (Portal)
description: Criptografia de dados transparente (TDE) no Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745234"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Introdução ao Transparent Data Encryption (TDE)

> [!div class="op_single_selector"]
>
> * [Visão geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Permissões necessárias

Para habilitar a TDE (Transparent Data Encryption), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia

Para habilitar o TDE, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione as configurações do portal **de opção** ![de criptografia de dados transparente](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione as ![ **configurações** do portal Em configuração em](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecione **Salvar**
   ![configurações do portal salvar](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Desabilitando a criptografia

Para desativar o TDE, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione as configurações do portal **de opção** ![de criptografia de dados transparente](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione as ![configurações do portal **Desaver**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecione **Salvar**
   ![configuração do portal salvar 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMVs de criptografia

A criptografia pode ser confirmada com as DMVs a seguir:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
