---
title: Namespace hierárquico do Azure Data Lake Storage Gen2
description: Descreve o conceito de um namespace hierárquico para o Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153070"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Namespace hierárquico do Azure Data Lake Storage Gen2

Um mecanismo essencial que permite que o Azure Data Lake Storage Gen2 ofereça o desempenho do sistema de arquivos na escala e pelo preço do armazenamento de objetos é a adição de um **namespace hierárquico**. Isso permite que a coleta de objetos / arquivos em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados, da mesma forma que o sistema de arquivos do computador é organizado. Com um namespace hierárquico ativado, uma conta de armazenamento torna-se capaz de fornecer a escalabilidade e o custo-efetividade do armazenamento de objetos, com semântica do sistema de arquivos familiar aos mecanismos e frameworks de análise.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Os benefícios de um namespace hierárquico

Os benefícios a seguir estão associados a sistemas de arquivos que implementam um namespace hierárquico em dados de blob:

- **Manipulação de Diretório Atômico:** os armazenamentos de objetos aproximam uma hierarquia de diretórios ao adotar uma convenção de barras de incorporação (/) no nome do objeto para denotar segmentos de caminho. Embora essa convenção funcione para organizar objetos, a convenção não fornece assistência para ações como mover, renomear ou excluir diretórios. Sem diretórios reais, os aplicativos devem processar potencialmente milhões de blobs individuais para obter tarefas em nível de diretório. Em contraste, um namespace hierárquico processa essas tarefas atualizando uma única entrada (o diretório pai).

    Essa otimização dramática é especialmente significativa para muitas estruturas de análise de big data. Ferramentas como Hive, Spark, etc. geralmente gravam a saída em locais temporários e depois renomeiam a localização na conclusão da tarefa. Sem um namespace hierárquico, esse renome pode muitas vezes levar mais tempo do que o próprio processo de análise. Uma menor latência de trabalho é igual ao custo total de propriedade (TCO) mais baixo para cargas de trabalho de análise.

- **Estilo familiar de interface:** Os sistemas de arquivos são bem compreendidos pelos desenvolvedores e usuários. Não há necessidade de aprender um novo paradigma de armazenamento quando você migra para a nuvem, pois a interface do sistema de arquivos exposta pelo Data Lake Storage Gen2 é o mesmo paradigma usado pelos computadores, grandes e pequenos.

Um dos motivos pelos quais os repositórios de objetos nunca deram suporte a namespace hierárquico é que um namespace hierárquico limita a escala. No entanto, o namespace hierárquico do Data Lake Storage Gen2 é dimensionado linearmente e não prejudica a capacidade ou o desempenho dos dados.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decidindo se habilitar um namespace hierárquico

Depois de habilitar um namespace hierárquico na sua conta, você não pode revertê-lo de volta para um espaço de nome plano. Portanto, considere se faz sentido habilitar um namespace hierárquico com base na natureza das cargas de trabalho do seu armazenamento de objetos.

Algumas cargas de trabalho podem não obter nenhum benefício ao habilitar um namespace hierárquico. Alguns exemplos dessas cargas de trabalho incluem backups, armazenamento de imagens e outros aplicativos em que a organização de objetos é armazenada separadamente dos próprios objetos (por exemplo, em um banco de dados separado). 

Além disso, enquanto o suporte aos recursos de armazenamento Blob e ao ecossistema de serviços do Azure continua a crescer, ainda existem alguns recursos e serviços do Azure que ainda não são suportados em contas que têm um namespace hierárquico. Veja [Problemas conhecidos](data-lake-storage-known-issues.md). 

Em geral, recomendamos que você ative um namespace hierárquico para cargas de trabalho de armazenamento projetadas para sistemas de arquivos que manipulam diretórios. Isso inclui todas as cargas de trabalho que são principalmente para processamento de análise. Conjuntos de dados que requerem um alto grau de organização também se beneficiarão ao habilitar um namespace hierárquico.

As razões para habilitar um namespace hierárquico são determinadas por uma análise de TCO. De um modo geral, melhorias na latência da carga de trabalho devido à aceleração do armazenamento exigirão recursos de computação por menos tempo. A latência para muitas cargas de trabalho pode ser melhorada devido à manipulação do diretório atômico que é habilitada por um namespace hierárquico. Em muitas cargas de trabalho, o recurso de computação representa> 85% do custo total e, portanto, até mesmo uma redução modesta na latência da carga de trabalho equivale a uma quantidade significativa de economia de TCO. Mesmo nos casos em que a habilitação de um namespace hierárquico aumenta os custos de armazenamento, o TCO ainda é reduzido devido aos custos reduzidos de computação.

Para analisar as diferenças nos preços de armazenamento de dados, nos preços das transações e nos preços de reserva da capacidade de armazenamento entre contas que possuem um namespace hierárquico plano versus um namespace hierárquico, consulte [os preços do Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](./data-lake-storage-quickstart-create-account.md)
