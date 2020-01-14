---
title: Conectar-se a um repositório de conhecimento (versão prévia) com o Power BI
titleSuffix: Azure Cognitive Search
description: Conecte um repositório de conhecimento (versão prévia) do Azure Cognitive Search ao Power BI para análise e exploração.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/26/2019
ms.openlocfilehash: dec792dfd3a2640fa08ebccd9077c081ba9737bb
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563285"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Conectar um repositório de conhecimento com o Power BI

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

Neste artigo, você aprenderá a conectar e a explorar um repositório de conhecimento usando o Power Query no aplicativo Power BI Desktop. Você pode começar mais rápido com modelos ou criar um painel personalizado do zero.

+ Siga as etapas em [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md) ou [Criar um repositório de conhecimento do Azure Cognitive Search usando REST](knowledge-store-create-rest.md) para criar o repositório de conhecimento de exemplo usado neste passo a passo. Também será necessário o nome da conta de Armazenamento do Azure que você usou para criar o repositório de conhecimento, junto com sua chave de acesso do portal do Azure.

+ [Instalar o Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Modelo do Power BI de exemplo – somente para o portal do Azure

Ao criar um [repositório de conhecimento usando o portal do Azure](knowledge-store-create-portal.md), você tem a opção de baixar um [modelo do Power BI](https://github.com/Azure-Samples/cognitive-search-templates) na segunda página do assistente **Importar dados**. Esse modelo fornece várias visualizações, como WordCloud e Navegador de Rede, para conteúdo baseado em texto. 

Clique **Obter Modelo do Power BI** na página **Adicionar habilidades cognitivas** para recuperar e baixar o modelo de sua localização do GitHub público. O assistente modifica o modelo para adequar-se à forma de seus dados, conforme capturados nas projeções do repositório de conhecimento especificadas no assistente. Por esse motivo, o modelo baixado variará sempre que você executar o assistente, presumindo entradas de dados e seleções de habilidades diferentes.

![Exemplo de Modelo do Power BI do Azure Cognitive Search](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Exemplo de modelo do Power BI")

> [!NOTE]
> Embora o modelo seja baixado enquanto o assistente está sendo disponibilizado em versão piloto, você precisará esperar o repositório de conhecimento ter sido de fato criado no armazenamento de Tabela do Azure para poder usá-lo.

## <a name="connect-with-power-bi"></a>Conectar com Power BI

1. Inicie o Power BI Desktop e clique em **Obter dados**.

1. Na janela **Obter Dados**, selecione **Azure** e, em seguida, **Armazenamento de Tabelas do Azure**.

1. Clique em **Conectar**.

1. Em **Nome da Conta ou URL**, insira o nome de sua conta de Armazenamento do Azure (a URL completa será criada para você).

1. Se solicitado, insira a chave de conta de armazenamento.

1. Selecione as tabelas que contêm os dados de avaliações de hotel criados pelas instruções anteriores. 

   + Para obter instruções do portal, os nomes de tabela são *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases* e *hotelReviewsSsPages*. 
   
   + Para obter instruções do REST, os nomes de tabela são *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases* e *hotelReviewsSentiment*.

1. Clique em **Carregar**.

1. Na faixa de opções superior, clique em **Editar Consultas** para abrir o **Editor do Power Query**.

   ![Abra o Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Abrir o Power Query")

1. Selecione *hotelReviewsSsDocument* e remova as colunas *PartitionKey*, *RowKey* e *Timestamp*. 
   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tabelas")

1. Clique no ícone com setas opostas no lado superior direito da tabela para expandir o *Conteúdo*. Quando a lista de colunas for exibida, selecione todas elas e desmarque as colunas que começam com ‘metadata’. Clique em **OK** para mostrar as colunas selecionadas.

   ![Editar tabelas](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir conteúdo")

1. Altere o tipo de dados das seguintes colunas clicando no ícone ABC-123 na parte superior esquerda da coluna.

   + Para *content.latitude* e *Content.longitude*, selecione **Número Decimal**.
   + Para *Content.reviews_date* e *Content.reviews_dateAdded*, selecione **Data/Hora**.

   ![Alterar tipos de dados](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Alterar tipos de dados")

1. Selecione *hotelReviewsSsPages* e repita as etapas 9 e 10 para excluir as colunas e expanda o *Conteúdo*.
1. Altere o tipo de dados de *Content.SentimentScore* para **Decimal Number**.
1. Selecione *hotelReviewsSsKeyPhrases* e repita as etapas 9 e 10 para excluir as colunas e expanda o *Conteúdo*. Não há nenhuma modificação de tipo de dados para esta tabela.

1. Na barra de comandos, clique em **Fechar e Aplicar**.

1. Clique no bloco Modelo no painel de navegação esquerdo e confirme se o Power BI mostra relações entre todas as três tabelas.

   ![Validar relações](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relações")

1. Clique duas vezes em cada relação e verifique se a **Direção do filtro cruzado** foi definida como **Ambas**.  Isso permite que seus elementos visuais sejam atualizados quando um filtro é aplicado.

1. Clique no bloco Relatório no painel de navegação à esquerda para explorar dados por meio de visualizações. Para campos de texto, tabelas e cartões são visualizações úteis. Você pode escolher os campos de cada uma das três tabelas para preencher a tabela ou o cartão. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

Para saber como explorar esse repositório de conhecimento usando o Gerenciador de Armazenamento, confira o artigo a seguir.

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)