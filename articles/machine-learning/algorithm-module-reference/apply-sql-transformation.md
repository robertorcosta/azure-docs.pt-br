---
title: Aplicar transformação de SQL
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Aplicar transformação SQL no Azure Machine Learning para executar uma consulta SQLite em conjuntos de dados de entrada para transformar os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314531"
---
# <a name="apply-sql-transformation"></a>Aplicar transformação de SQL

Este artigo descreve um módulo do azure Machine Learning designer (visualização).

Usando o módulo Aplicar transformação SQL, você pode:
  
-   Criar tabelas para resultados e salvar os conjuntos de dados em um banco de dados portátil.  
  
-   Realizar transformações personalizadas em tipos de dados ou criar agregações.  
  
-   Executar instruções de consulta SQL para filtrar ou alterar dados e retornar os resultados da consulta como uma tabela de dados.  

> [!IMPORTANT]
> O motor SQL usado neste módulo é **SQLite**. Para obter mais informações sobre a sintaxe SQLite, consulte [SQL como Entendido pelo SQLite](https://www.sqlite.org/index.html) para obter mais informações.  

## <a name="how-to-configure-apply-sql-transformation"></a>Como configurar aplicar a transformação SQL  

O módulo pode ter até três conjuntos de dados como entradas. Ao fazer referência aos conjuntos de dados conectados `t1`a `t2`cada `t3`porta de entrada, você deve usar os nomes e . O número da tabela indica o índice da porta de entrada.  
  
O parâmetro restante é uma consulta SQL, que usa a sintaxe do SQLite. Ao digitar várias linhas na caixa de texto **SQL Script,** use um ponto e vírgula para encerrar cada declaração. Caso contrário, as quebras de linha serão convertidas em espaços.  

Este módulo oferece suporte a todas as instruções padrão da sintaxe do SQLite. Para obter uma lista de declarações não suportadas, consulte a seção [Notas Técnicas.](#technical-notes)

##  <a name="technical-notes"></a>Observações técnicas  

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

-   Uma entrada é sempre necessária na porta 1.  
  
-   Para identificadores de coluna que contenham um espaço ou outros caracteres especiais, sempre coloque o identificador `SELECT` `WHERE` da coluna em colchetes quadrados ou aspas duplas ao se referir à coluna nas ou cláusulas.  
  
### <a name="unsupported-statements"></a>Declarações não suportadas  

Embora SQLite suporte a maior parte do padrão ANSI SQL, ele não inclui muitos recursos com suporte nos sistemas de banco de dados relacional comercial. Para obter mais informações, consulte [SQL como Entendido por SQLite](http://www.sqlite.org/lang.html). Além disso, esteja ciente das seguintes restrições ao criar instruções SQL:  
  
- O SQLite usa digitação dinâmica para valores, em vez de atribuir um tipo a uma coluna como na maioria dos sistemas de banco de dados relacional. Ele é digitado sem rigidez e permite a conversão implícita de tipos.  
  
- `LEFT OUTER JOIN`é implementado, `RIGHT OUTER JOIN` mas `FULL OUTER JOIN`não ou .  

- Você pode usar as instruções `RENAME TABLE` e `ADD COLUMN` com o comando `ALTER TABLE`, mas outras cláusulas não são suportadas, incluindo `DROP COLUMN`, `ALTER COLUMN` e `ADD CONSTRAINT`.  
  
- Você pode criar uma VIEW dentro do SQLite, mas depois disso as exibições serão somente leitura. Você não pode executar uma instrução `DELETE`, `INSERT` ou `UPDATE` em uma exibição. No entanto, você pode criar um disparador que é acionado em uma tentativa de `DELETE`, `INSERT` ou `UPDATE` em uma exibição e executar outras operações no corpo do gatilho.  
  

Além da lista de funções não suportadas fornecidas no site oficial do SQLite, a wiki a seguir fornece uma lista de outros recursos não suportados: [SQLite - SQL sem suporte](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
