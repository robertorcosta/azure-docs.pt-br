---
title: O que são documentos paralelos? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d397e481fcd11e534e4bb5cf98f8c402435e890e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997070"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
Não importa qual idioma está marcado como "origem" e qual idioma está marcado como "destino" – um documento paralelo pode ser usado para treinar um sistema de tradução em qualquer direção.

## <a name="requirements"></a>Requisitos

Você precisará de um mínimo de 10.000 sentenças paralelas alinhadas exclusivamente para treinar um sistema. Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham um vocabulário exclusivo suficiente para treinar com êxito um modelo de tradução. Como prática recomendada, adicione continuamente mais conteúdo paralelo e retreine para melhorar a qualidade do seu sistema de tradução. Consulte alinhamento de [frase](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

A Microsoft exige que os documentos carregados no Tradutor personalizado não violem as propriedades intelectuais ou de direitos autorais de terceiros. Para mais informações, consulte os [Termos de Uso](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
O upload de um documento usando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="use-of-parallel-documents"></a>Uso de documentos paralelos

Documentos paralelos são usados pelo sistema:

1.  Aprender como palavras, frases e frases são comumente mapeadas entre os dois idiomas.

2.  Aprender a processar o contexto apropriado, dependendo das frases vizinhas. Uma palavra nem sempre pode traduzir para a mesma palavra na outra língua.

Como prática recomendada, verifique se há uma correspondência de sentença 1:1 entre as versões de idioma de origem e de destino dos documentos.

Se o seu projeto é específico de domínio (categoria), seus documentos devem ser consistentes na terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de sentenças no conjunto de documentos e da qualidade das sentenças. Quanto mais exemplos seus documentos contiverem usos diversos para uma palavra específica da sua categoria, o melhor trabalho que o sistema pode fazer durante a tradução.

Os documentos enviados são privados para cada workspace e podem ser usados em quantos projetos ou treinamentos você desejar. Frases extraídas de seus documentos são armazenadas separadamente em seu repositório como arquivos de texto Unicode simples e estão disponíveis para exclusão. Não use o Custom Translator como repositório de documentos, você não poderá fazer o download dos documentos que você enviou no formato que você enviou.



## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.
