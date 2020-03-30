---
title: 'Dados de importação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Importar dados no Azure Machine Learning para carregar dados em um pipeline de aprendizado de máquina a partir de serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456192"
---
# <a name="import-data-module"></a>Módulo de dados de importação

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para carregar dados em um pipeline de aprendizado de máquina a partir de serviços de dados em nuvem existentes. 

> [!Note]
> Todas as funcionalidades fornecidas por este módulo podem ser feitas por **datastore** e **conjuntos de dados** na página de pouso worksapce. Recomendamos que você use **datastore** e **dataset** que inclui recursos adicionais, como monitoramento de dados. Para saber mais, veja [como acessar dados](../how-to-access-data.md) e como registrar [conjuntos de dados.](../how-to-create-register-datasets.md)
> Depois de registrar um conjunto de dados, você pode encontrá-lo na categoria **Conjuntos de** -> **dados Meus Conjuntos de Dados** na interface do designer. Este módulo é reservado para usuários do Studio (clássico) para uma experiência familiar. 
>

O módulo **de dados de importação** suporta dados de leitura das seguintes fontes:

- URL via HTTP
- Armazenamentoem em nuvem do Azure através [**de Datastores**](../how-to-access-data.md))
    - Recipiente Azure Blob
    - Compartilhamento de arquivos do Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Banco de Dados SQL do Azure
    - Azure PostgreSQL    

Antes de usar o armazenamento em nuvem, você precisa registrar um datastore no seu espaço de trabalho azure Machine Learning primeiro. Para obter mais informações, consulte [Como acessar dados](../how-to-access-data.md). 

Depois de definir os dados que deseja e se conectar à fonte, os **[Dados de Importação](./import-data.md)** inferem o tipo de dados de cada coluna com base nos valores que ela contém e carregam os dados em seu pipeline de designer. A saída de Dados de **Importação** é um conjunto de dados que pode ser usado com qualquer pipeline de designer.

Se os dados de origem forem alterados, você poderá atualizar o conjunto de dados e adicionar novos dados reexecutando [dados de importação](./import-data.md).

## <a name="how-to-configure-import-data"></a>Como configurar dados de importação

1. Adicione o módulo **Dados de Importação** ao seu pipeline. Você pode encontrar este módulo na categoria **Entrada e Saída de Dados** no designer.

1. Selecione o módulo para abrir o painel direito.

1. Selecione **A origem dos dados**e escolha o tipo de origem de dados. Pode ser HTTP ou datastore.

    Se você escolher o datastore, você pode selecionar os datastores existentes que já estão registrados no seu espaço de trabalho do Azure Machine Learning ou criar um novo datastore. Em seguida, defina o caminho dos dados para importar no datastore. Você pode navegar facilmente pelo caminho clicando em **Procurar caminho** ![de importação-data-path](media/module/import-data-path.png)

1. Selecione o esquema de visualização para filtrar as colunas que deseja incluir. Você também pode definir configurações avançadas como Delimitr em opções de Análise.

    ![visualização de dados de importação](media/module/import-data.png)

1. A caixa de seleção, **reger a saída,** decide se executa o módulo para regenerar a saída no tempo de execução. 

    É por padrão não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de execução. 

    Se for selecionado, o sistema executará o módulo novamente para regenerar a saída. Então selecione essa opção quando os dados subjacentes no armazenamento forem atualizados, ele pode ajudar a obter os dados mais recentes.


1. Envie o oleoduto.

    Quando o Import Data carrega os dados no designer, ele infere o tipo de dados de cada coluna com base nos valores que ele contém, seja numérico ou categórico.

    Se houver um cabeçalho, ele será usado para nomear as colunas do conjunto de dados de saída.

    Se não houver cabeçalhos de coluna existentes nos dados, novos nomes de coluna serão gerados usando o formato col1, col2,... , coln*.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com sucesso.

Se você quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados cada vez que o pipeline for executado, selecione o ícone **do conjunto de dados Registrar** na guia **Saídas** no painel direito do módulo. Escolha um nome para o conjunto de dados. O conjunto de dados salvo preserva os dados no momento da economia, o conjunto de dados não é atualizado quando o pipeline é reexecutado, mesmo que o conjunto de dados no pipeline mude. Isso pode ser útil para tirar instantâneos de dados.

Depois de importar os dados, pode precisar de algumas preparações adicionais para modelagem e análise:

- Use [Editar metadados](./edit-metadata.md) para alterar nomes de colunas, para lidar com uma coluna como um tipo de dados diferente ou para indicar que algumas colunas são rótulos ou recursos.

- Use [Selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) para selecionar um subconjunto de colunas para transformar ou usar na modelagem. As colunas transformadas ou removidas podem ser facilmente reunidos ao conjunto de dados original usando o módulo [Adicionar colunas.](./add-columns.md)  

- Use [Partição e Amostra](./partition-and-sample.md) para dividir o conjunto de dados, realizar amostragem ou obter as linhas n superiores.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
