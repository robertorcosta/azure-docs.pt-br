---
title: 'Digite dados manualmente: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Enter Data Manualmente no Azure Machine Learning para criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367509"
---
# <a name="enter-data-manually-module"></a>Insira o módulo Dados Manualmente

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use o módulo **Enter Data Manualmente** para criar um pequeno conjunto de dados digitando valores. O conjunto de dados pode ter várias colunas.
  
Este módulo pode ser útil em cenários como:  
  
- Gerando um pequeno conjunto de valores para testes.  
- Criando uma pequena lista de rótulos.  
- Digitando uma lista de nomes de colunas para inserir em um conjunto de dados.

## <a name="create-a-dataset"></a>Criar um conjunto de dados 
  
1. Adicione o módulo [Enter Data Manualmente](./enter-data-manually.md) ao seu pipeline. Você pode encontrar este módulo na categoria **Entrada e Saída de Dados** no Azure Machine Learning. 
  
1. Em **Formato de Dados,** selecione uma das seguintes opções. Essas opções determinam como os dados fornecidos devem ser analisados. Os requisitos para cada formato diferem muito, por isso não deixe de ler os tópicos relacionados.  
  
   - **ARFF**: Formato de arquivo de relação de atributo usado por Weka.   
   - **CSV**: Formato de valores separados por comma. Para obter mais informações, consulte [Converter para CSV](./convert-to-csv.md).    
   - **SVMLight**: Formato usado pelo Vowpal Wabbit e outras estruturas de aprendizado de máquina.    
   - **TSV**: Formato de valores separados por guias.

   Se você escolher um formato e não fornecer dados que atendam às especificações do formato, um erro de tempo de execução ocorrerá.
  
1. Clique dentro da caixa de texto **Dados** para começar a inserir dados. Os seguintes formatos requerem atenção especial:  
  
   - **CSV**: Para criar várias colunas, cole em texto separado por comma ou digite várias colunas usando commas entre campos.
  
     Se você selecionar a opção **HasHeader,** poderá usar a primeira linha de valores como o título da coluna.  
  
     Se você desmarcar esta opção, os nomes da coluna (Col1, Col2 e assim por diante) serão usados. Você pode adicionar ou alterar nomes de colunas mais tarde usando [Edit Metadata](./edit-metadata.md).  
  
   - **TSV**: Para criar várias colunas, cole em texto separado por guias ou digite várias colunas usando guias entre campos.  
  
     Se você selecionar a opção **HasHeader,** poderá usar a primeira linha de valores como o título da coluna.  
  
     Se você desmarcar esta opção, os nomes da coluna (Col1, Col2 e assim por diante) serão usados. Você pode adicionar ou alterar nomes de colunas mais tarde usando [Edit Metadata](./edit-metadata.md).  
  
   - **ARFF**: Cole em um arquivo de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar os campos de cabeçalho opcional e os campos de atributos necessários no início dos dados. 

     Por exemplo, as seguintes linhas de cabeçae atributos podem ser adicionadas a uma lista simples. O título da `SampleText`coluna seria . Observe que o tipo String não é suportado.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Digite ou cole em valores usando o formato SVMLight.  
  
     Por exemplo, a amostra a seguir representa o primeiro par de linhas do conjunto de dados de doação de sangue, no formato SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Quando você executa o módulo [Enter Data Manualmente,](./enter-data-manually.md) essas linhas são convertidas em um conjunto de dados de colunas e valores de índice da seguinte forma:  
  
     |Col1|Col2|Col3|Col4|Rótulos|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Selecione a tecla Enter após cada linha para iniciar uma nova linha.      
     
   Se você selecionar Entrar várias vezes para adicionar várias linhas de arrasto vazias, as linhas vazias serão removidas ou aparadas.  
  
   Se você criar linhas com valores ausentes, você sempre pode filtrar-los mais tarde.  
  
1. Conecte a porta de saída a outros módulos e execute o pipeline.  
  
   Para visualizar o conjunto de dados, clique com o botão direito do mouse no módulo e selecione **Visualizar**.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 