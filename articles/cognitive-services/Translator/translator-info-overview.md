---
title: O que é Tradutor? - Tradutor
titlesuffix: Azure Cognitive Services
description: Integre o Tradutor em seus aplicativos, sites, ferramentas e outras soluções para fornecer experiências do usuário em vários idiomas.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 5da9e5b38b88a5a148bd3d85664daa523153086b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589444"
---
# <a name="what-is-the-translator"></a>O que é Tradutor?

O Tradutor é fácil de integrar em seus aplicativos, sites, ferramentas e soluções. Ele permite que você adicione experiências de usuário com vários idiomas em [mais de 70 idiomas](languages.md) e pode ser usado em qualquer plataforma de hardware com qualquer sistema operacional para traduzir idioma de texto para texto.

O Tradutor faz parte da coleção [Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/?pivot=products&panel=ai) do aprendizado de máquina e dos algoritmos de IA na nuvem, e está pronto para ser consumido em seus projetos de desenvolvimento.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O Tradutor é um serviço de tradução automática baseado em nuvem. O serviço principal é o Tradutor, que possibilita muitos produtos e serviços da Microsoft, e é usado por milhares de empresas no mundo todo em seus aplicativos e fluxos de trabalho, o que permite que seu conteúdo alcance um público global.

A tradução de fala, possibilitado pelo Tradutor, também está disponível no [Serviço de Fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Ele combina a funcionalidade da API de Tradução de Fala e do Serviço de Fala Personalizado em um serviço unificado e totalmente personalizável. O Serviço de Fala está substituindo a API de Tradução de Fala, que será encerrado em 15 de outubro de 2019.

## <a name="language-support"></a>Suporte ao idioma

O Microsoft Translator dá suporte a vários idiomas para tradução, transliteração, detecção de idioma e dicionários. Confira [Suporte a idiomas](language-support.md) para obter uma lista completa, ou acesse a lista de forma programática com a [API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A NMT (Tradução automática neural) é o novo padrão para traduções automáticas de IA (inteligência artificial) de alta qualidade. Ela substitui a tecnologia herdada de SMT (tradução automática estatística), que atingiu um limite de qualidade em meados da década de 2010.

A NMT fornece traduções melhores que a SMT não apenas de um ponto de vista de pontuação de qualidade da tradução bruta, mas também porque ela soará mais fluente e humana. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras. A SMT usava apenas o contexto imediato de algumas palavras presentes antes e depois de cada palavra.

Os modelos de NMT são a essência da API e não são visíveis aos usuários finais. A única diferença notável é uma melhor qualidade da tradução, especialmente para idiomas como o chinês, o japonês e o árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Personalização de linguagem

Uma extensão do principal serviço Tradutor da Microsoft, o Tradutor Personalizado pode ser usado junto com o Tradutor para ajudá-lo a personalizar o sistema de tradução neural e aprimorar a tradução conforme sua terminologia e estilo específicos.

Com o Tradutor Personalizado, é possível compilar sistemas de tradução que controlem a terminologia usada na sua própria empresa ou setor. Assim, seu sistema de tradução personalizado será integrado facilmente nos aplicativos, nos fluxos de trabalho, nos sites existentes e em vários tipos de dispositivos, por meio do Tradutor regular, usando o parâmetro da categoria.

Saiba mais sobre a [personalização de idioma](customization.md)

## <a name="next-steps"></a>Próximas etapas

- [Inscreva-se](translator-text-how-to-signup.md) para obter uma chave de acesso.
- A [referência de API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.
- [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
