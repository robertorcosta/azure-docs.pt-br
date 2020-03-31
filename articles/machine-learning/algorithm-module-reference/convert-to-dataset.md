---
title: 'Converter para conjunto de dados: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Converter para Dataset no Azure Machine Learning para converter a entrada de dados no formato interno do conjunto de dados usado pelo Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456583"
---
# <a name="convert-to-dataset"></a>Converter em conjunto de dados

Este artigo descreve como usar o módulo Convert to Dataset no Azure Machine Learning designer (preview) para converter quaisquer dados de um pipeline para o formato interno do designer.
  
A conversão não é necessária na maioria dos casos. O Azure Machine Learning converte implicitamente dados em seu formato de conjunto de dados nativo quando qualquer operação é realizada nos dados. 

Recomendamos a economia de dados no formato do conjunto de dados se você tiver realizado algum tipo de normalização ou limpeza em um conjunto de dados, e você deseja garantir que as alterações sejam usadas em outros pipelines.  
  
> [!NOTE]
> Converter para Dataset altera apenas o formato dos dados. Ele não salva uma nova cópia dos dados no espaço de trabalho. Para salvar o conjunto de dados, clique duas vezes na porta de saída, **selecione Salvar como conjunto de dados**e digite um novo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Como usar converter para conjunto de dados  

Recomendamos que você use o módulo [Editar metadados](edit-metadata.md) para preparar o conjunto de dados antes de usar Convert to Dataset. Você pode adicionar ou alterar nomes de colunas, ajustar tipos de dados e fazer outras alterações conforme necessário.

1.  Adicione o módulo Converter para Dataset ao seu pipeline. Você pode encontrar este módulo na categoria **transformação de dados** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   

    Enquanto os dados [forem tabulares,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)você pode convertê-los em um conjunto de dados. Isso inclui dados carregados através [de Dados de Importação,](import-data.md)dados criados através do [Enter Data Manualmente](enter-data-manually.md)ou conjuntos de dados transformados através [da Apply Transformation](apply-transformation.md).

3.  Na lista de parada **ação,** indique se deseja fazer qualquer limpeza nos dados antes de salvar o conjunto de dados:  
  
    - **Nenhum**: Use os dados como está.  
  
    - **SetMissingValue**: Defina um valor específico para um valor ausente no conjunto de dados. O espaço reservado padrão é o caractere ponto de interrogação (?), mas você pode usar a opção **Valor ausente personalizado** para inserir um valor diferente. Por exemplo, se você inserir taxi para valor ausente de **Taxi** **for Custom,** então todas as instâncias de **Taxi** no conjunto de dados serão alteradas para o valor perdido.
  
    - **Substituivalores**: Use esta opção para especificar um único valor exato a ser substituído por qualquer outro valor exato. Você pode substituir valores ausentes ou valores personalizados definindo o método **Substituir:**

      - **Falta :** Escolha esta opção para substituir os valores faltantes no conjunto de dados de entrada. Para **Novo Valor,** digite o valor para substituir os valores perdidos.
      - **Personalizado**: Escolha esta opção para substituir valores personalizados no conjunto de dados de entrada. Para **o valor personalizado,** digite o valor que deseja encontrar. Por exemplo, se seus `obs` dados contêm a seqüência `obs`usada como espaço reservado para valores ausentes, você insere . Para **novo valor,** digite o novo valor para substituir a seqüência original com.
  
    Observe que a **operação SubstituirValores** se aplica apenas às correspondências exatas. Por exemplo, essas cordas não `obs.`seriam `obsolete`afetadas: .  
 
  
5.  Envie o oleoduto.  

## <a name="results"></a>Resultados

+  Para salvar o conjunto de dados resultante com um novo nome, selecione no conjunto **de dados registro** de ícones na guia **Saídas** no painel direito do módulo.  
  
## <a name="technical-notes"></a>Observações técnicas  

-   Qualquer módulo que leve um conjunto de dados como entrada também pode levar dados no arquivo CSV ou no arquivo TSV. Antes de qualquer código do módulo ser executado, as entradas são pré-processadas. O pré-processamento é equivalente à execução do módulo Converter para Dataset na entrada.  
  
-   Você não pode converter do formato SVMLight para um conjunto de dados.  
  
-   Quando você está especificando uma operação de substituição personalizada, a operação de busca e substituição se aplica a valores completos. Partidas parciais não são permitidas. Por exemplo, você pode substituir um 3 por um -1 ou com 33, mas não pode substituir um 3 em um número de dois dígitos, como 35.  
  
-   Para operações de substituição personalizadas, a substituição falhará de modo silencioso se você usar como substituição de qualquer caractere que não esteja em conformidade com o tipo de dados atual da coluna.  

  
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
