---
title: Conectar-se ao Power BI Professional
description: Neste tutorial, percorreremos as etapas de como conectar o Power BI Desktop ao SQL sob demanda (versão prévia).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425325"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Conectar-se ao Synapse SQL com o Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Neste tutorial, percorreremos as etapas de como conectar o Power BI Desktop ao SQL sob demanda (versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

Ferramenta para emitir consultas:

- Cliente SQL de sua escolha:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop instalado

Parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do SQL sob demanda    | Será usado como o nome do servidor                                   |
| Região do ponto de extremidade de serviço do SQL sob demanda     | Será usada para determinar qual armazenamento será usado nas amostras |
| Nome de usuário e senha para acesso de ponto de extremidade | Será usado para acessar o ponto de extremidade                               |
| O banco de dados que você usará para criar exibições     | Esse banco de dados será usado como ponto de partida em exemplos       |

## <a name="first-time-setup"></a>Configuração inicial

Há duas etapas antes de usar exemplos:

1. Criar um banco de dados para suas exibições
2. Criar credenciais a serem usadas pelo SQL sob demanda para acessar arquivos no armazenamento

### <a name="create-database"></a>Criar banco de dados

Já que você usará o ambiente de demonstração, deverá criar seu banco de dados para fins de demonstração. O banco de dados é necessário para que seja possível criar exibições nele. Você usará esse banco de dados em algumas das consultas de exemplo nesta documentação.

> [!NOTE]
> Observe que os bancos de dados são usados apenas para os metadados de exibição, não para os dados reais.
>
> Anote o nome do banco de dados que você usar, você precisará dele mais tarde.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Criar credenciais

Precisaremos criar credenciais antes que você possa executar consultas. Esta credencial será usada pelo serviço de SQL sob demanda para acessar arquivos no armazenamento.

> [!NOTE]
> Observe que você precisará criar credenciais para acesso à conta de armazenamento. Embora o SQL sob demanda possa acessar armazenamentos de diferentes regiões, ter o armazenamento e o workspace do Azure Synapse na mesma região proporcionará uma experiência com melhor desempenho.

**Snippet de código sobre como criar credenciais para contêineres de dados do censo**, execute:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Como criar um relatório do Power BI Desktop

Abra o aplicativo Power BI Desktop e selecione a opção "Obter dados".
![Abra o aplicativo Power BI Desktop e selecione Obter dados](./media/get-started-power-bi-professional/step-0-open-powerbi.png).

### <a name="step-1---select-data-source"></a>Etapa 1 – Selecionar fonte de dados

Selecione "Azure" no menu e, em seguida, "Banco de Dados SQL do Azure".
![Selecione uma fonte de dados.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Etapa 2 – Selecionar banco de dados

Escreva a URL para o banco de dados e o nome do banco de dados em que a exibição reside no momento.
![Selecione o banco de dados no ponto de extremidade.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Próximas etapas

Avance para [Arquivos de armazenamento de consulta](get-started-azure-data-studio.md) para saber como se conectar ao SQL sob demanda usando o Azure Data Studio.
 