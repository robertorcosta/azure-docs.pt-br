---
title: Eliminação de duplicação de linhas e localização de nulos usando trechos de fluxo de dados
description: Saiba como facilmente a eliminação de duplicação de linhas e a localização de nulos usando trechos de código em fluxos de dados
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 683d7ffe5549b86a587cd2dc3c9a86a36aee1bba
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637051"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Eliminação de duplicação de linhas e localização de nulos usando trechos de fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usando trechos de código no mapeamento de fluxos de dados, você pode facilmente executar tarefas comuns como eliminação de duplicação de dados e filtragem nula. Este guia de instruções explicará como adicionar essas funções a seus pipelines com muito facilidade usando trechos de script de fluxo de dados.

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+ novo pipeline** para criar um novo pipeline.

2. Adicione uma atividade de fluxo de dados.

3. Adicionar uma transformação de origem e conectá-la a um de seus conjuntos de os

    ![Trecho de código-fonte 2](media/data-flow/snippet-adf-2.png)

4. Os trechos de verificação de eliminação de duplicação e nulo usam padrões genéricos que aproveitam o descompasso de esquema de fluxo de dados para que funcionem com qualquer esquema do seu conjunto ou com conjuntos de dados que não têm nenhum esquema predefinido.

5. [Vá para a página de documentação do script de fluxo de dados e copie o trecho de código para linhas distintas.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Na interface do usuário do designer de fluxo de dados, clique no botão de script no canto superior direito para abrir o editor de script por trás do grafo de fluxo de dados.

    ![Trecho de código de origem 3](media/data-flow/snippet-adf-3.png)

7. Após a definição de ```source1``` no seu script, pressione Enter e cole no trecho de código.

8. Você conectará esse trecho de código colado à transformação de origem anterior criada no grafo digitando "origem1" na frente do código colado.

9. Como alternativa, você pode conectar a nova transformação no designer selecionando o fluxo de entrada do novo nó de transformação no grafo.

    ![Trecho de código-fonte 4](media/data-flow/snippet-adf-4.png)

10. Agora, seu fluxo de dados removerá linhas duplicadas da origem usando a transformação agregação que agrupa por todas as linhas usando um hash geral em todos os valores de coluna.
    
11. Em seguida, adicionaremos um trecho de código para dividir os dados em um fluxo que contém linhas com valores nulos e um fluxo que não tem nenhum nulo.

12. [Volte para a biblioteca de trechos e, desta vez, copie o código para as verificações nulas.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. No designer de fluxo de dados, clique novamente em script e cole esse novo código de transformação na parte inferior, conectando-o à transformação anterior digitando o nome da transformação na frente do trecho colado.

14. Seu grafo de fluxo de dados agora deve ser semelhante a este:

    ![Trecho de código-fonte 1](media/data-flow/snippet-adf-1.png)

  Agora você tem um fluxo de dados em funcionamento com deduping genéricas e verificações nulas usando trechos de código existentes da biblioteca de script de fluxo de dados e adicionando-os ao seu design existente.

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
