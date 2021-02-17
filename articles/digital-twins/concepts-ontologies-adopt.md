---
title: Adotando o ontologies padrão do setor
titleSuffix: Azure Digital Twins
description: Saiba mais sobre os ontologies do setor existentes que podem ser adotados para o gêmeos digital do Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561280"
---
# <a name="adopting-an-industry-ontology"></a>Adotando uma ontologia do setor

Como pode ser mais fácil começar com uma DTDL ontologia de software livre do que a partir de uma página em branco, a Microsoft está fazendo parcerias com especialistas em domínio para publicar ontologies, que representam convenções de setor amplamente aceitas e que dão suporte a uma variedade de casos de uso de clientes. 

O resultado é um conjunto de ontologies com base em DTDL de software livre, que aprende com, aproveitam, aprendem ou usam diretamente os padrões do setor. Os ontologies são projetados para atender às necessidades dos desenvolvedores de downstream, com o potencial de serem amplamente adotados e/ou estendidos pelo setor.

Neste momento, a Microsoft trabalhou com parceiros de imóveis para desenvolver um ontologia para prédios inteligentes, que fornece uma base comum para a modelagem de prédios inteligentes com base nos padrões do setor para evitar a reinvenção. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia de construção inteligente RealEstateCore

A Microsoft fez uma parceria com a [RealEstateCore](https://www.realestatecore.io/), um consórcio Sueco de proprietários de imóveis, fornecedores de software e instituições de pesquisa, para fornecer uma DTDL ontologia de código aberto para o setor de imóveis: [**DTDL RealEstateCore ontologia para prédios inteligentes**](https://github.com/Azure/opendigitaltwins-building).

Este ontologia de prédios inteligentes fornece um solo comum para a modelagem de prédios inteligentes, usando padrões do setor (como [esquema de Brick](https://brickschema.org/ontology/) ou ontologia de [topologia de construção W3C](https://w3c-lbd-cg.github.io/bot/index.html)) para evitar a reinvenção. O ontologia também vem com as práticas recomendadas de como consumir e estendê-lo corretamente. 

Para saber mais sobre a estrutura e as convenções de modelagem do ontologia, como usá-la, como estendê-la e como contribuir, visite o repositório do ontologia [no GitHub](https://github.com/Azure/opendigitaltwins-building). 

Você também pode ler mais sobre a parceria com RealEstateCore e metas desta iniciativa nesta postagem de blog e vídeo que acompanha: [*RealEstateCore, um ontologia de construção inteligente para o digital gêmeos, agora está disponível*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como estender ontologies padrão do setor para atender às suas especificações: [*conceitos: estendendo ontologies do setor*](concepts-ontologies-extend.md).

* Ou, continue no caminho para desenvolver modelos com base em ontologies: [*usando estratégias de ontologia em um caminho de desenvolvimento de modelo*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).