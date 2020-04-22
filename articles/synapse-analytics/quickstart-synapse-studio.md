---
title: Usar o Synapse Studio (versão prévia)
description: Neste início rápido, você verá e aprenderá como é fácil consultar vários tipos de arquivos usando o Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419810"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Início Rápido: Usando o Synapse Studio (versão prévia)

Neste guia de início rápido, você aprenderá a consultar arquivos usando o Synapse Studio.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

[Criar um workspace do Azure Synapse e a conta de armazenamento associada](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

No workspace do Azure Synapse, no portal do Azure, clique em **Iniciar o Synapse Studio**.

![Iniciar o Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Como alternativa, você pode iniciar o Synapse Studio clicando em [Azure Synapse Analytics](https://web.azuresynapse.net) e fornecendo os valores corretos de locatário, assinatura e workspace.

## <a name="browse-storage-accounts"></a>Procurar contas de armazenamento

Depois de abrir o Synapse Studio, navegue até **Dados** e, em seguida, expanda **Contas de armazenamento** para ver a conta de armazenamento no workspace.

![Procurar arquivos no armazenamento](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Você pode criar pastas e carregar arquivos usando os links na barra de ferramentas para organizar seus arquivos.

## <a name="query-files-on-storage-account"></a>Consultar arquivos na conta de armazenamento

> [!IMPORTANT]
> Você precisa ser um membro da função `Storage Blob Reader` no armazenamento subjacente para poder consultar os arquivos. Saiba como [atribuir as permissões RBAC **Leitor de Dados do Blob de Armazenamento** ou **Colaborador de Dados do Blob de Armazenamento** no Armazenamento do Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Carregue alguns arquivos `PARQUET`.
2. Selecione um ou mais arquivos e, em seguida, crie um script SQL ou um notebook Spark para ver o conteúdo dos arquivos. Se você quiser criar um notebook, precisará criar um [Pool do Apache Spark no workspace](spark/apache-spark-notebook-create-spark-use-sql.md).

   ![Consultar arquivos no armazenamento](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Execute a consulta ou o notebook gerado para ver o conteúdo do arquivo:

   ![Ver o conteúdo do arquivo](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Você pode alterar a consulta para filtrar e classificar os resultados. Encontre os recursos de linguagem que estão disponíveis no SQL sob demanda em [Visão geral dos recursos do SQL](sql/overview-features.md).

## <a name="next-steps"></a>Próximas etapas

- Permitir que os usuários do Azure AD consultem arquivos [atribuindo as permissões RBAC **Leitor de Dados do Blob de Armazenamento** ou **Colaborador de Dados do Blob de Armazenamento** no Armazenamento do Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Consultar arquivos no Armazenamento do Azure usando o SQL sob demanda](sql/on-demand-workspace-overview.md)
- [Criar Pool do Apache Spark](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Criar relatório do Power BI em arquivos armazenados no armazenamento do Azure](sql/tutorial-connect-power-bi-desktop.md)
