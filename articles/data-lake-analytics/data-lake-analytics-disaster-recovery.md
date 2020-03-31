---
title: Orientação de recuperação de desastres para a azure Data Lake Analytics
description: Saiba como planejar a recuperação de desastres para suas contas do Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889774"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Orientação de recuperação de desastres para a azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de análise sob demanda que simplifica big data. Em vez de implantar, configurar e ajustar o hardware, você escreve consultas para transformar seus dados e extrair informações importantes. O serviço de análise pode manipular trabalhos de qualquer escala de maneira instantânea, simplesmente configurando o controle para a quantidade de potência necessária. Você só paga pelo trabalho quando ele está em execução, o que o torna econômico. Este artigo fornece orientações sobre como proteger seus empregos contra raras paralisações em toda a região ou exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres

Ao usar o Azure Data Lake Analytics, é fundamental que você prepare seu próprio plano de recuperação de desastres. Este artigo ajuda a guiá-lo a construir um plano de recuperação de desastres. Existem recursos adicionais que podem ajudá-lo a criar seu próprio plano:
- [Recuperação de desastre e falha para aplicativos do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas de resiliência do Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Melhores práticas e orientação de cenários

Você pode executar um trabalho U-SQL recorrente em uma conta ADLA em uma região que lê e escreve tabelas U-SQL, bem como dados não estruturados.  Prepare-se para um desastre tomando essas medidas:

1. Crie contas ADLA e ADLS na região secundária que serão usadas durante uma paralisação.

   > [!NOTE]
   > Uma vez que os nomes das contas são globalmente únicos, use um esquema de nomeação consistente que indique qual conta é secundária.

2. Para dados não estruturados, consulte [a orientação de recuperação de desastres para dados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para dados estruturados armazenados em tabelas e bancos de dados ADLA, crie cópias dos artefatos de metadados, como bancos de dados, tabelas, funções avaliadas em tabelas e conjuntos. Você precisa ressincronizar periodicamente esses artefatos quando mudanças acontecem na produção. Por exemplo, os dados recém-inseridos devem ser replicados na região secundária copiando os dados e inserindo na tabela secundária.

   > [!NOTE]
   > Esses nomes de objetos são escopo para a conta secundária e não são globalmente únicos, de modo que eles podem ter os mesmos nomes que na conta de produção primária.

Durante uma paralisação, você precisa atualizar seus scripts para que os caminhos de entrada apontem para o ponto final secundário. Em seguida, os usuários enviam seus trabalhos para a conta ADLA na região secundária. A saída do trabalho será então escrita para a conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Próximas etapas

[Orientação de recuperação de desastre para dados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
