---
title: Scripts SQL no Azure Synapse Studio (versão prévia)
description: Introdução aos scripts SQL do Azure Synapse Studio (visualização)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431066"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Usando o script SQL no Azure Synapse Studio (versão prévia)

O Azure Synapse Studio (versão prévia) fornece uma interface da Web de script SQL para você criar consultas SQL. Você pode se conectar ao pool do SQL (visualização) ou ao SQL sob demanda (versão prévia). 

## <a name="begin-authoring-in-sql-script"></a>Iniciar a criação no script SQL 

Há várias maneiras de iniciar a experiência de criação no script SQL. Você pode criar um novo script SQL por meio de um dos métodos a seguir.

1. Selecione o ícone "+" e escolha script SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. No menu Ações, em desenvolver scripts SQL, escolha "novo script SQL" no menu "ações" em desenvolver scripts SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

ou 

3. Escolha "importar" no menu "ações" em desenvolver scripts SQL e selecione um script SQL existente do seu armazenamento local.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Criar seu script SQL

1. Escolha um nome para o script SQL selecionando o botão "Propriedade" e substituindo o nome padrão atribuído ao script SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Escolha o pool de SQL específico ou o SQL sob demanda no menu suspenso "conectar-se a". Ou, se necessário, escolha o banco de dados em "usar banco de dados".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Comece a criar seu script SQL usando o recurso IntelliSense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Executar o script SQL

Selecione o botão "executar" para executar o script SQL. Os resultados são exibidos por padrão em uma tabela.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportar seus resultados

Você pode exportar os resultados para o armazenamento local em formatos diferentes (incluindo CSV, Excel, JSON, XML) selecionando "exportar resultados" e escolhendo a extensão.

Você também pode visualizar os resultados do script SQL em um gráfico selecionando o botão "gráfico". Selecione o "tipo de gráfico" e "categoria coluna". Você pode exportar o gráfico para uma imagem selecionando "salvar como imagem". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Explorar dados de um arquivo parquet.

Você pode explorar os arquivos parquet em uma conta de armazenamento usando o script SQL para visualizar o conteúdo do arquivo. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabelas SQL, tabelas externas, exibições

Selecionando o menu "ações" em dados, você pode selecionar várias ações, como: "novo script SQL", "selecionar as primeiras 1000 linhas", "criar", "soltar e criar". Explore o gesto disponível clicando com o botão direito do mouse nos nós do pool SQL e SQL sob demanda.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como criar um script SQL, consulte [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).