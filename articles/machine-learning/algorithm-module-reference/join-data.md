---
title: 'Juntar dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Join Join Data no Azure Machine Learning para mesclar conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477571"
---
# <a name="join-data"></a>Unir dados

Este artigo descreve como usar o módulo **Desemparador de Sede** no azure Machine Learning (preview) para mesclar dois conjuntos de dados usando uma operação de adesão ao estilo de banco de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de compartilhamento

Para realizar uma adesão em dois conjuntos de dados, eles devem ser relacionados por uma coluna-chave. Chaves compostas usando várias colunas também são suportadas. 

1. Adicione os conjuntos de dados que deseja combinar e arraste o módulo **'Dados de adesão'** para o seu pipeline. 

    Você pode encontrar o módulo na categoria **Transformação de Dados,** em **Manipulação**.

1. Conecte os conjuntos de dados ao módulo **'Participar dados'.** 
 
1. Selecione **O seletor** de colunas Launch para escolher colunas de tecla). Lembre-se de escolher colunas para as entradas esquerda e direita.

    Para uma única chave:

    Selecione uma única coluna de tecla para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas-chave da entrada esquerda e da entrada direita na mesma ordem. O módulo **'Participar dados'** se juntará às tabelas quando todas as colunas principais coincidirem. Verifique a opção **Permitir duplicatas e preservar a ordem da coluna na seleção** se a ordem da coluna não for a mesma da tabela original. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selecione a opção **Caso 'Corresponder'** se quiser preservar a sensibilidade do caso em uma caquide de coluna de texto. 
   
1. Use a lista de saque do **tipo Desmembrar** para especificar como os conjuntos de dados devem ser combinados.  
  
    * **Inadedentro**: Uma *adesão interna* é a operação de adesão mais comum. Retorna as linhas combinadas somente quando os valores das colunas de chave correspondem.  
  
    * **Left Outer Join**: A *left outer join* returns joins for all rows from the left table. Quando uma linha na tabela esquerda não tem linhas correspondentes na tabela direita, a linha retornada contém valores ausentes para todas as colunas que vêm da mesa direita. Você também pode especificar um valor de substituição para valores ausentes.  
  
    * **Full Outer Join**: Uma *junta externa completa* retorna todas as linhas da mesa esquerda **(tabela1**) e da mesa direita **(tabela2**).  
  
         Para cada uma das linhas em cada tabela que não tenham linhas correspondentes na outra, o resultado inclui uma linha contendo valores ausentes.  
  
    * **Semi-Adesão à esquerda**: Uma *semi-adesão à esquerda* retorna apenas os valores da tabela esquerda quando os valores das colunas-chave correspondem.  

1. Para a opção Mantenha as **colunas de tecla direita na tabela junta:**

    * Selecione esta opção para visualizar as teclas de ambas as tabelas de entrada.
    * Desmarque para retornar apenas as colunas de tecla da entrada esquerda.

1. Envie o oleoduto.

1. Para visualizar os resultados, clique com o botão direito do mouse no **'Juntar dados'** e selecione **Visualizar**.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 