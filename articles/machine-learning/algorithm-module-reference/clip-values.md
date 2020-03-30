---
title: Recortar valores
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Valors de clipe no Azure Machine Learning para detectar outliers e clipar ou substituir seus valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456600"
---
# <a name="clip-values"></a>Recortar valores

Este artigo descreve um módulo do azure Machine Learning designer (visualização).

Use o módulo Valors de clipe para identificar e substituir opcionalmente os valores de dados acima ou abaixo de um limite especificado por uma média, uma constante ou outro valor substituto.  

Você conecta o módulo a um conjunto de dados que tem os números que deseja cortar, escolhe as colunas para trabalhar e, em seguida, define um limiar ou intervalo de valores e um método de substituição. O módulo pode produzir apenas os resultados ou os valores alterados anexados ao conjunto de dados original.

## <a name="how-to-configure-clip-values"></a>Como configurar valores de clipe

Antes de começar, identifique as colunas que deseja cortar e o método a ser usado. Recomendamos que você teste qualquer método de recorte em um pequeno subconjunto de dados primeiro.

O módulo aplica os mesmos critérios e método de substituição a **todas as** colunas que você incluir na seleção. Portanto, certifique-se de excluir colunas que você não deseja alterar.

Se você precisar aplicar métodos de recorte ou critérios diferentes em algumas colunas, você deve usar uma nova instância de **Valores** de Clipe para cada conjunto de colunas semelhantes.

1.  Adicione o módulo **Valors de clipe** ao seu pipeline e conecte-o ao conjunto de dados que deseja modificar. Você pode encontrar este módulo em **Transformação de Dados,** na categoria **Escala e Redução.** 
  
1.  Em **Lista de colunas,** use o Seletor de colunas para escolher as colunas a que **os Valores de clipe** serão aplicados.  
  
1.  Para **Definir limites,** escolha uma das seguintes opções na lista de isto. Essas opções determinam como você define os limites superiore e inferior para valores aceitáveis versus valores que devem ser cortados.  
  
    - **ClipPeaks**: Quando você corta valores por picos, você especifica apenas um limite superior. Valores maiores que esse valor de limite são substituídos.
  
    -  **ClipSubpeaks**: Quando você corta valores por subpicos, você especifica apenas um limite inferior. Valores inferiores a esse valor de limite são substituídos.  
  
    - **ClipPeaksAndSubpeaks**: Quando você corta valores por picos e subpicos, você pode especificar os limites superior e inferior. Os valores que estão fora desse intervalo são substituídos. Valores que correspondem aos valores de limite não são alterados.
  
1.  Dependendo da seleção na etapa anterior, você pode definir os seguintes valores limiares: 

    + **Limiar inferior**: Exibido somente se você escolher **ClipSubPeaks**
    + **Limiar superior**: Exibido somente se você escolher **ClipPeaks**
    + **Limiar:** Exibido somente se você escolher **ClipPeaksAndSubPeaks**

    Para cada tipo de limiar, escolha **Constant** ou **Percentile**.

1. Se você selecionar **Constant,** digite o valor máximo ou mínimo na caixa de texto. Por exemplo, suponha que você sabe que o valor 999 foi usado como um valor de espaço reservado. Você pode escolher **Constant** para o limiar superior e digite 999 em **valor constante para o limiar superior**.
  
1. Se você escolher **Percentile,** você restringirá os valores da coluna a um intervalo de percentil. 

    Por exemplo, suponha que você deseja manter apenas os valores na faixa de 10-80 percentil, e substituir todos os outros. Você escolheria **Percentle**e digite 10 para **valor percentil para limite inferior**e digite 80 para valor **percentil para limiar superior**. 

    Consulte a seção em [percentis](#examples-for-clipping-using-percentiles) para alguns exemplos de como usar faixas de percentil.  
  
1.  Defina um valor substituto.

    Números que correspondem exatamente aos limites especificados são considerados dentro da faixa permitida de valores e, portanto, não são substituídos. Todos os números que estão fora da faixa especificada são substituídos pelo valor substituto. 
  
    + **Valor substituto para picos**: Define o valor a substituir todos os valores da coluna que são maiores que o limite especificado.  
    + **Valor substituto para subpicos**: Define o valor a ser usado como um substituto para todos os valores da coluna que são inferiores ao limite especificado.  
    + Se você usar a opção **ClipPeaksAndSubpeaks,** poderá especificar valores de substituição separados para os valores cortados superiore e inferior.  

    Os seguintes valores de substituição são suportados:  
  
    -   **Limiar**: Substitui valores cortados pelo valor limite especificado.  
  
    -   **Média**: Substitui valores cortados pela média dos valores da coluna. A média é calculada antes que os valores sejam cortados.  
  
    -   **Mediana**: Substitui os valores cortados pela mediana dos valores da coluna. A mediana é calculada antes que os valores sejam cortados.   
  
    -   **Faltando**. Substitui os valores cortados pelo valor faltante (vazio).  
  
1.  **Adicionar colunas indicadoras**: Selecione esta opção se quiser gerar uma nova coluna que informa se a operação de recorte especificada foi aplicada ou não aos dados nessa linha. Esta opção é útil quando você está testando um novo conjunto de valores de recorte e substituição.  
  
1. **Sinalizador de sobregravação**: Indique como deseja que os novos valores sejam gerados. Por padrão, **Os valores do clipe** constroem uma nova coluna com os valores de pico cortados ao limiar desejado. Novos valores sobreescrevem a coluna original.  
  
    Para manter a coluna original e adicionar uma nova coluna com os valores cortados, desmarque esta opção.  
  
1.  Envie o oleoduto.  
  
    Clique com o botão direito do mouse no módulo **Valors** de clipe e selecione **Visualizar** ou selecione o módulo e alterne para a guia **Saídas** no painel direito, clique no ícone de histograma nas **saídas da Porta,** para rever os valores e certificar-se de que a operação de recorte atendeu às suas expectativas.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exemplos para recortar usando percentis

Para entender como funciona o recorte de percentuais, considere um conjunto de dados com 10 linhas, que tenham uma instância cada dos valores de 1 a 10.  
  
- Se você estiver usando o percentil como o limite superior, o valor para o 90º percentil, 90 por cento de todos os valores no conjunto de dados deverá ser menor que esse valor.  
  
- Se você estiver usando o percentil como o limite inferior, o valor para o 10º percentil, 10 por cento de todos os valores no conjunto de dados deverá ser menor que esse valor.  
  
1.  Para **conjunto de limiares,** escolha **ClipPeaksAndSubPeaks**.  
  
1.  Para **Limite superior**, escolha **Percentil** e para **Número de percentil**, digite 90.  
  
1.  Para **Valor superior de substituição**, escolha **Valor Ausente**.  
  
1.  Para **Limite inferior**, escolha **Percentil** e para **Número de percentil**, digite 10.  
  
1.  Para **Valor inferior de substituição**, escolha **Valor Ausente**.  
  
1.  Desmarque a opção **Sinalizador de substituição** e selecione a opção **Adicionar coluna indicadora**.  
  
Agora tente o mesmo pipeline usando 60 como o limiar percentil superior e 30 como o limiar percentil inferior, e use o valor limiar como valor de substituição. A tabela a seguir compara esses dois resultados:  
  
1.  Substituir por falta; Limiar superior = 90; Limiar inferior = 20  
  
1.  Substituir por limiar; Percentil superior = 60; Percentil inferior = 40  
  
|Dados originais|Substituir por ausente|Substituir por limite|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSO<br /><br /> 4, FALSO<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, FALSO<br /><br /> 8, FALSO<br /><br /> 9, FALSO<br /><br /> TRUE|4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 4, VERDADE<br /><br /> 5, FALSO<br /><br /> 6, FALSO<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE<br /><br /> 7, VERDADE| 
 
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
