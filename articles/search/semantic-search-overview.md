---
title: Pesquisa semântica
titleSuffix: Azure Cognitive Search
description: Saiba como Pesquisa Cognitiva está usando modelos de pesquisa semântica de aprendizado profundo do Bing para tornar os resultados da pesquisa mais intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203225"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Pesquisa semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A pesquisa semântica é uma coleção de recursos relacionados à consulta que dão suporte a uma experiência de consulta mais natural e de alta qualidade. Os recursos incluem a reclassificação semântica dos resultados da pesquisa, bem como as legendas e a geração de respostas com o realce semântico. Os primeiros 50 resultados retornados do [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) são reclassificados para localizar as correspondências mais relevantes.

A tecnologia subjacente aproveita os investimentos do Bing e do Microsoft Research e é integrada à infraestrutura de Pesquisa Cognitiva. Para usá-lo, você precisará de pequenas modificações na solicitação de pesquisa, mas nenhuma configuração extra ou reindexação será necessária.

Os recursos de visualização pública incluem:

+ Modelo de classificação semântica que pontua resultados com base no contexto ou significado semântico dos termos da consulta de pesquisa
+ Legendas semânticas que realçam passagens relevantes
+ Respostas semânticas para a consulta, também formuladas a partir dos resultados
+ Verificação ortográfica que corrige erros antes que os termos da consulta alcancem o mecanismo de pesquisa

## <a name="semantic-search-architecture"></a>Arquitetura de pesquisa semântica

Os componentes da pesquisa semântica são dispostos em camadas sobre o pipeline de execução de consulta existente. A correção ortográfica (não mostrada no diagrama) melhora a RECALL corrigindo erros de digitação em termos de consulta individuais. Após a conclusão da análise e da análise, o mecanismo de pesquisa recupera os documentos que corresponderam à consulta e os classifica usando o [algoritmo de Pontuação padrão](index-similarity-and-scoring.md#similarity-ranking-algorithms), BM25 ou clássico, dependendo de quando o serviço foi criado. Os perfis de Pontuação também são aplicados neste estágio. 

Tendo recebido as principais correspondências de 50, o [algoritmo de classificação semântica](semantic-how-to-query-response.md) reavalia o documento corpus. Os resultados podem incluir mais de 50 correspondências, mas apenas a primeira 50 será reclassificada. Para classificação, o algoritmo usa o aprendizado de máquina e o aprendizado de transferência para pontuar novamente os documentos com base em quão bem cada um corresponde à intenção da consulta.

Para criar legendas e respostas, ele usa modelos de representação de idioma. Dentro de uma legenda, o algoritmo usa um modelo de linguagem desenvolvido pelo Bing para extrair legendas com destaques que melhor resumem os resultados da consulta. Se a consulta de pesquisa for uma pergunta e as respostas forem solicitadas, um modelo de linguagem semelhante identificará as passagens de texto que melhor respondem à pergunta, conforme expresso pela consulta de pesquisa.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Componentes semânticos em um pipeline de consulta" border="true":::

## <a name="availability-and-pricing"></a>Disponibilidade e preços

A classificação semântica está disponível por meio do [registro de inscrição](https://aka.ms/SemanticSearchPreviewSignup), nos serviços de pesquisa criados em uma camada Standard (S1, S2, S3), localizada em uma destas regiões: Norte EUA Central, oeste dos EUA, oeste dos EUA 2, leste dos EUA 2, Europa setentrional Europa Ocidental. Um serviço de pesquisa existente em S1 ou superior nas regiões declaradas é qualificado para a versão prévia (sem necessidade de criar um novo serviço).

A correção ortográfica está disponível nas mesmas regiões, mas não tem nenhuma restrição de camada e nenhum requisito de inscrição. 

Entre a inicialização da visualização em 2 de março até 1º de abril, a correção ortográfica e a classificação semântica são oferecidas gratuitamente. Após 1º de abril, os custos computacionais da execução dessa funcionalidade se tornarão um evento faturável. O custo esperado é de cerca de USD $500/mês para consultas de 250.000. Você pode encontrar informações detalhadas de custo documentadas na [página de preços pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/) e em [estimar e gerenciar custos](search-sku-manage-costs.md).

## <a name="next-steps"></a>Próximas etapas

Um novo tipo de consulta permite a classificação de relevância e as estruturas de resposta da pesquisa semântica. [Crie uma consulta semântica](semantic-how-to-query-request.md) para começar. Ou então, examine qualquer um dos artigos a seguir para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos da consulta](speller-how-to-add.md)
+ [Classificação semântica e respostas](semantic-how-to-query-response.md)