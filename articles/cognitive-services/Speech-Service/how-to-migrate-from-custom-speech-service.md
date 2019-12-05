---
title: Migrar do serviço de fala personalizado para o serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de Fala Personalizada agora faz parte do serviço de fala. Alterne para o serviço de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805919"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do serviço de Fala Personalizada para o serviço de fala

Use este artigo para migrar seus aplicativos do serviço de Fala Personalizada para o serviço de fala.

O serviço de Fala Personalizada agora faz parte do serviço de fala. Alterne para o serviço de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.

## <a name="migration-for-new-customers"></a>Migração para novos clientes

O modelo de preços é mais simples, usando um modelo de preços baseado em hora para o serviço de fala.  

1. Crie um recurso do Azure em cada região onde seu aplicativo está disponível. O nome do recurso do Azure é **Fala**. Você pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de fala em texto
    * Fala em texto personalizada
    * Conversão de texto em fala
    * Tradução de fala

2. Faça o download do [Speech SDK](speech-sdk.md).

3. Siga os guias de início rápido e as amostras de SDK para usar as APIs corretas. Se você usar as APIs REST, também precisará usar os terminais e as chaves de recursos corretos.

4. Atualize o aplicativo cliente para usar o serviço de fala e as APIs.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre as chaves de recurso existentes para o serviço de fala no portal do serviço de fala. Use as seguintes etapas:

> [!NOTE]
> As chaves de recurso só podem ser migradas na mesma região.

1. Entre no portal [cris.ai](https://cris.ai/Home/CustomSpeech) e selecione a assinatura no menu superior direito.

2. Selecione **migrar a assinatura selecionada**.

3. Digite a chave de assinatura na caixa de texto e selecione **Migrar**.

## <a name="next-steps"></a>Próximos passos

* [Experimente o Speech Service gratuitamente](get-started.md).
* Saiba mais sobre conceitos de [conversão de fala em texto](./speech-to-text.md).

## <a name="see-also"></a>Consulte

* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](speech-sdk.md#get-the-sdk)
