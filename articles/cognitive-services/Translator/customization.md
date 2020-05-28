---
title: Personalização de tradução-Tradutor
titleSuffix: Azure Cognitive Services
description: Use o Hub do Microsoft Translator para compilar seu próprio sistema de tradução automática usando seu estilo e sua terminologia preferenciais.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6db43300632ec5b2c4f6c18848442901a40561b0
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996991"
---
# <a name="customize-your-text-translations"></a>Personalizar suas traduções de texto

O tradutor personalizado é um recurso do serviço tradutor, que permite aos usuários personalizar a tradução automática do computador neural do Microsoft Translator ao traduzir texto usando o Tradutor (somente versão 3).

O recurso também pode ser usado para personalizar a tradução de fala quando usado a [Fala de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Tradutor personalizado

Com o Tradutor Personalizado, você pode compilar sistemas de tradução neural que entendam a terminologia usada na sua própria empresa e setor. O sistema de tradução personalizada então se integrará aos aplicativos, fluxos de trabalho e sites existentes.

### <a name="how-does-it-work"></a>Como funciona?

Use seus documentos traduzidos anteriormente (leaflets, páginas da Web, documentação, etc.) para criar um sistema de tradução que reflita sua terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os usuários podem fazer upload de documentos TMX XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados que são paralelos no nível do documento, mas ainda não estão alinhados no nível de frase. Se os usuários tiverem acesso às versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Tradutor Personalizado conseguirá criar automaticamente a correspondência de frases entre os documentos.  O sistema também pode usar dados monolíngues em um ou ambos os idiomas para complementar os dados de treinamento paralelos para melhorar as traduções.

Em seguida, o sistema personalizado está disponível por meio de uma chamada regular para o tradutor usando o parâmetro Category.

Dado o tipo e a quantidade apropriados de dados de treinamento, não é incomum esperar ganhos entre 5 e 10, ou até mais pontos BLEU na qualidade de tradução usando o Tradutor Personalizado.

Para obter mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis, veja o [Guia do usuário do Tradutor Personalizado](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub do Microsoft Translator

> [!NOTE]
> O Hub herdado do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Tradutor Personalizado versus Hub

|   | **Hub** | **Tradutor personalizado**|
|:-----|:----:|:----:|
|Status do recurso de personalização    | Disponibilidade geral    | Disponibilidade geral |
| Versão da API de texto    | Somente V2    | Somente V3 |
| Personalização de SMT    | Sim    | Não |
| Personalização de NMT    | Não    | Sim |
| Nova personalização de serviços de Fala unificados    | Não    | Sim |
| [Sem rastreamento](https://www.aka.ms/notrace) | Sim    | Sim |

## <a name="collaborative-translations-framework"></a>Estrutura de traduções colaborativas

> [!NOTE]
> A partir de 1º de fevereiro de 2018, addtranslation () e AddTranslationArray () não estão mais disponíveis para uso com o tradutor v 2.0. Esses métodos falharão e nada será gravado. O Translator v 3.0 não oferece suporte a esses métodos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um sistema de idioma personalizado usando o Tradutor Personalizado](https://aka.ms/CustomTranslatorDocs)
