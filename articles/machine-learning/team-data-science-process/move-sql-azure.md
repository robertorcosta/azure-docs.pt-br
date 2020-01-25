---
title: Mover dados para um Banco de Dados SQL do Azure - Processo de Ciência de Dados de Equipe
description: Mover dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados em um SQL Server local para um banco de dados SQL do Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722451"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover dados para um banco de dados SQL do Azure para Azure Machine Learning

Este artigo descreve as opções para mover dados de arquivos simples (formatos CSV ou TSV) ou de dados armazenados em um SQL Server local para um banco de dado SQL do Azure. Essas tarefas para movimentar dados para a nuvem fazem parte do Processo de Ciência de Dados de Equipe.

Para um tópico que descreve as opções para mover dados para um SQL Server local para o Machine Learning, consulte [Mover dados para o SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md).

A tabela a seguir resume as opções para mover dados para um Banco de Dados SQL do Azure.

| <b>FONTE</b> | <b>DESTINO: Banco de Dados SQL do Azure</b> |
| --- | --- |
| <b>Arquivo simples (CSV ou TSV formatado)</b> |[Consulta SQL de inserção em massa](#bulk-insert-sql-query) |
| <b>SQL Server local</b> |1.[Exportar para arquivo simples](#export-flat-file)<br> 2. [Assistente de migração de banco de dados SQL](#insert-tables-bcp)<br> 3. [backup e restauração de banco de dados](#db-migration)<br> 4. [Azure data Factory](#adf) |

## <a name="prereqs"></a>Pré-requisitos
O procedimento descrito aqui requer que você tenha:

* Uma **assinatura do Azure**. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md) . Depois de criar a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Acesso a um **Banco de dados SQL do Azure**. Se você precisa configurar um Banco de Dados SQL do Azure, o [Guia de Introdução ao Banco de Dados SQL do Microsoft Azure](../../sql-database/sql-database-get-started.md) fornece informações sobre como provisionar uma nova instância de um Banco de Dados SQL do Azure.
* **Azure PowerShell** instalado e configurado localmente. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

**Dados**: os processos de migração são demonstrados usando o [Conjunto de Dados de Táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/). O conjunto de dados de Táxis de NYC contém informações sobre dados de viagem e feiras e está disponível no armazenamento de blobs do Azure: [dados de táxi de NYC](https://www.andresmh.com/nyctaxitrips/). Um exemplo e uma descrição desses arquivos são fornecidos na [Descrição do Conjunto de Dados de Viagens de Táxi de NYC](sql-walkthrough.md#dataset).

Você pode adaptar os procedimentos descritos aqui para um conjunto de seus próprios dados ou seguir as etapas conforme descrito usando o conjunto de dados de Táxi de NYC. Para carregar o conjunto de dados de Táxi de NYC em seu banco de dados do SQL Server local, siga o procedimento descrito em [Importação de dados em massa para o Banco de Dados do SQL Server](sql-walkthrough.md#dbload). Essas instruções são para um SQL Server em uma máquina virtual do Azure, mas o procedimento para carregar o SQL Server local é o mesmo.

## <a name="file-to-azure-sql-database"></a>Movendo dados de uma fonte de arquivo simples para um banco de dados SQL do Azure
Os dados em arquivos simples (CSV ou TSV formatado) podem ser movidos para um banco de dado SQL do Azure usando uma consulta SQL de inserção em massa.

### <a name="bulk-insert-sql-query"></a> Consulta SQL de inserção em massa
As etapas para o procedimento usando a consulta SQL de inserção em massa são semelhantes às instruções para mover dados de uma fonte de arquivo simples para SQL Server em uma VM do Azure. Para obter detalhes, consulte [Consulta SQL de inserção em massa](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Movendo dados de SQL Server locais para um banco de dados SQL do Azure
Se os dados de origem forem armazenados em um SQL Server local, haverá várias possibilidades para mover os dados para um banco de dado SQL do Azure:

1. [Exportar para arquivo simples](#export-flat-file)
2. [Assistente de Migração de Banco de Dados SQL](#insert-tables-bcp)
3. [Backup e restauração de banco de dados](#db-migration)
4. [Azure Data Factory](#adf)

As etapas para os três primeiros são semelhantes às seções em [mover dados para SQL Server em uma máquina virtual do Azure](move-sql-server-virtual-machine.md) que abrange esses mesmos procedimentos. Os links para as seções apropriadas deste tópico são fornecidos nas instruções a seguir.

### <a name="export-flat-file"></a>Exportar para arquivo simples
As etapas para exportar para um arquivo simples são semelhantes às instruções abordadas em [exportar para arquivo simples](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Assistente de Migração de Banco de Dados SQL
As etapas para usar o SQL Database Migration Wizard são semelhantes às instruções abordadas no [SQL Database Migration Wizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Backup e restauração de banco de dados
As etapas para usar o backup e a restauração de banco de dados são semelhantes às instruções listadas em [backup e restauração de banco de dados](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Saiba como mover dados para um banco de dado SQL do Azure com Azure Data Factory (ADF) neste tópico, [mover dados de um SQL Server local para SQL Azure com Azure data Factory](move-sql-azure-adf.md). Este tópico mostra como usar o ADF para mover dados de um SQL Server local para um banco de dado SQL do Azure por meio do armazenamento de BLOBs do Azure.

Considere o uso do ADF quando os dados precisarem ser migrados continuamente com fontes híbridas locais e na nuvem.  O ADF também ajuda quando os dados precisam de transformações ou precisa de uma nova lógica de negócios durante a migração. O ADF permite o planejamento e monitoramento de trabalhos usando scripts simples de JSON que gerenciam a movimentação de dados em intervalos periódicos. O ADF também possui outros recursos, como suporte para operações complexas.
