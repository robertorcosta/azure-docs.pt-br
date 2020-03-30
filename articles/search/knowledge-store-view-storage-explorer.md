---
title: Exibir uma loja de conhecimento (visualização) com o Storage Explorer
titleSuffix: Azure Cognitive Search
description: Exiba e analise um repositório de conhecimento da Pesquisa Cognitiva do Azure com o Gerenciador de Armazenamento do portal do Azure. loja de conhecimento está atualmente em visualização pública.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754072"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir um repositório de conhecimento com o Gerenciador de Armazenamento

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

Neste artigo, você aprenderá por exemplo como se conectar e explorar uma loja de conhecimento usando o Storage Explorer no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md) para criar o repositório de conhecimento de exemplo usado neste passo a passo.

+ Também será necessário o nome da conta de armazenamento do Azure que você usou para criar o repositório de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar uma loja de conhecimento no Storage Explorer

1. No portal Azure, [abra a conta armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) que você usou para criar o armazenamento de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de Armazenamento**.

1. Expanda a lista **TABELAS** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **Importação de Dados** em seus dados de exemplo de resenhas de hotéis.

Selecione qualquer tabela para visualizar os dados enriquecidos, incluindo frases-chave e pontuações de sentimento.

   ![Exibir tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de Armazenamento")

Para alterar o tipo de dados para qualquer valor de tabela ou para alterar valores individuais em sua tabela, clique em **Editar**. Quando você alterar o tipo de dados para qualquer coluna na linha de tabela, ele será aplicado a todas as linhas.

   ![Editar tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabelas no Gerenciador de Armazenamento")

Para executar consultas, clique em **Consultar** na barra de comandos e insira suas condições.  

   ![Consultar tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Consultar tabelas no Gerenciador de Armazenamento")

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

Conecte este armazenamento de conhecimento ao Power BI para análise mais profunda ou siga em frente com o código, usando a API REST e o Carteiro para criar um armazenamento de conhecimento diferente.

> [!div class="nextstepaction"]
> [Conecte-se com power bi](knowledge-store-connect-power-bi.md)
> [Criar uma loja de conhecimento em REST](knowledge-store-create-rest.md)
