---
title: Scripts SQL no Azure Synapse Studio (pré-visualização)
description: Introdução Azure Synapse Studio (pré-visualização) scripts SQL
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431066"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Usando o script SQL no Azure Synapse Studio (visualização)

O Azure Synapse Studio (preview) fornece uma interface web de script SQL para você escrever consultas SQL. Você pode se conectar ao pool SQL (visualização) ou sql sob demanda (visualização). 

## <a name="begin-authoring-in-sql-script"></a>Comece a escrever no script SQL 

Existem várias maneiras de iniciar a experiência de autoria no script SQL. Você pode criar um novo script SQL através de um dos seguintes métodos.

1. Selecione o ícone "+" e escolha o script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. No menu Ações em Desenvolver scripts SQL Escolha "Novo script SQL" no menu "Ações" em Desenvolver scripts SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

ou 

3. Escolha "Importar" no menu "Ações" em Desenvolver scripts SQL e selecione um script SQL existente no armazenamento local.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Crie seu script SQL

1. Escolha um nome para o script SQL selecionando o botão "Propriedade" e substituindo o nome padrão atribuído ao script SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Escolha o pool SQL específico ou o SQL sob demanda no menu suspenso "Conectar-se". Ou, se necessário, escolha o banco de dados de "Usar banco de dados".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Comece a criar seu script SQL usando o recurso intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Execute seu script SQL

Selecione o botão "Executar" para executar seu script SQL. Os resultados são exibidos por padrão em uma tabela.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportar seus resultados

Você pode exportar os resultados para o seu armazenamento local em diferentes formatos (incluindo CSV, Excel, JSON, XML) selecionando "Resultados de exportação" e escolhendo a extensão.

Você também pode visualizar os resultados do script SQL em um gráfico selecionando o botão "Gráfico". Selecione a coluna "Gráfico" e "Categoria". Você pode exportar o gráfico para uma imagem selecionando "Salvar como imagem". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explorar dados de um arquivo parquet.

Você pode explorar arquivos Parquet em uma conta de armazenamento usando o script SQL para visualizar o conteúdo do arquivo. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelas SQL, tabelas externas, visualizações

Ao selecionar o menu "Ações" em dados, você pode selecionar várias ações como: "Novo script SQL", "Selecione top 1000 linhas", "CREATE", "DROP e CREATE". Explore o gesto disponível clicando com o botão direito do mouse nos nós do pool SQL e do SQL sob demanda.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a autoria de um script SQL, consulte [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).