---
title: Lidar com linhas de erro com fluxos de dados de mapeamento na fábrica de dados do Azure
description: Aprenda a lidar com erros de truncamento SQL na Fábrica de Dados Do Azure usando fluxos de dados de mapeamento.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732698"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Lidar com as linhas de erro de truncamento SQL nos fluxos de dados de mapeamento da Fábrica de Dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um cenário comum na Fábrica de Dados ao usar fluxos de dados de mapeamento, é escrever seus dados transformados em um banco de dados SQL do Azure. Neste cenário, uma condição de erro comum que você deve prevenir contra é possível truncação da coluna. Siga essas etapas para fornecer o registro de colunas que não se encaixam em uma coluna de string de destino, permitindo que seu fluxo de dados continue nesses cenários.

## <a name="scenario"></a>Cenário

1. Temos uma tabela de banco de dados Azure SQL que tem uma ```nvarchar(5)``` coluna chamada "nome".

2. Dentro do nosso fluxo de dados, queremos mapear títulos de filmes da nossa pia para aquela coluna de "nome" alvo.

    ![Fluxo de dados do filme 1](media/data-flow/error4.png)
    
3. O problema é que o título do filme não cabe em uma coluna de pia que só pode conter 5 caracteres. Quando você executar esse fluxo de dados, você receberá um erro como este:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Este vídeo percorre um exemplo da lógica de manipulação da linha de erro de configuração no seu fluxo de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Como projetar em torno desta condição

1. Neste cenário, o comprimento máximo da coluna "nome" é de cinco caracteres. Então, vamos adicionar uma transformação condicional dividida que nos permitirá registrar linhas com "títulos" que são maiores que cinco caracteres, ao mesmo tempo em que permite que o resto das linhas que podem caber nesse espaço para escrever no banco de dados.

    ![divisão condicional](media/data-flow/error1.png)

2. Esta transformação condicional de divisão define o comprimento máximo de "título" como sendo cinco. Qualquer linha menor ou igual a cinco ```GoodRows``` irá para o córrego. Qualquer linha maior que cinco irá ```BadRows``` para o córrego.

3. Agora precisamos registrar as linhas que falharam. Adicione uma transformação ```BadRows``` de pia ao fluxo para registro. Aqui, vamos "mapear automaticamente" todos os campos para que tenhamos registro do registro completo da transação. Esta é uma saída de arquivo CSV delimitada por texto para um único arquivo no Blob Storage. Chamaremos o arquivo de registro de "badrows.csv".

    ![Linhas ruins](media/data-flow/error3.png)
    
4. O fluxo de dados completo é mostrado abaixo. Agora podemos dividir as linhas de erro para evitar os erros de truncamento SQL e colocar essas entradas em um arquivo de log. Enquanto isso, linhas bem sucedidas podem continuar a escrever em nosso banco de dados de destino.

    ![fluxo completo de dados](media/data-flow/error2.png)

## <a name="next-steps"></a>Próximas etapas

* Construa o resto da lógica de fluxo de dados usando transformações de [fluxos](concepts-data-flow-overview.md)de dados de mapeamento .
