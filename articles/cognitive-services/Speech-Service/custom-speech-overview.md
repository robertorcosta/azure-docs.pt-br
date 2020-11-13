---
title: Visão geral do Fala Personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: Fala Personalizada é um conjunto de ferramentas online que permite que você avalie e aprimore nossa precisão de fala para texto para seus aplicativos, ferramentas e produtos. Tudo o que é necessário para começar são alguns arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de fala em texto Personalizada.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 4f65cc79c972a48f97e794b4c2870c3fb6e68d31
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557503"
---
# <a name="what-is-custom-speech"></a>O que é Fala Personalizada?

[Fala personalizada](https://aka.ms/customspeech) é um conjunto de ferramentas baseadas na interface do usuário que permitem avaliar e melhorar a precisão de fala a texto da Microsoft para seus aplicativos e produtos. Tudo o que é necessário para começar são alguns arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de fala em texto Personalizada.

## <a name="whats-in-custom-speech"></a>O que há de Fala Personalizada?

Para poder fazer qualquer coisa com Fala Personalizada, você precisará de uma conta do Azure e uma assinatura do serviço de fala. Depois de ter uma conta, você pode preparar seus dados, treinar e testar seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, por fim, implantar e usar o modelo de fala em texto personalizado.

Este diagrama realça as partes que compõem o [portal de fala personalizada](https://aka.ms/customspeech). Use os links abaixo para saber mais sobre cada etapa.

![Realça os diferentes componentes que compõem o portal de Fala Personalizada.](./media/custom-speech/custom-speech-overview.png)

1. [Assinar e criar um projeto](#set-up-your-azure-account) -crie uma conta do Azure e assine o serviço de fala. Essa assinatura unificada fornece acesso a conversão de fala, texto em fala, tradução de fala e o [portal de fala personalizada](https://speech.microsoft.com/customspeech). Em seguida, usando sua assinatura do serviço de fala, crie seu primeiro projeto de Fala Personalizada.

1. [Carregar dados de teste](how-to-custom-speech-test-data.md) – carregar dados de teste (arquivos de áudio) para avaliar a oferta de fala a texto da Microsoft para seus aplicativos, ferramentas e produtos.

1. [Inspecionar a qualidade do reconhecimento](how-to-custom-speech-inspect-data.md) -use o [portal de fala personalizada](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade do reconhecimento de fala de seus dados de teste. Para medições quantitativas, consulte [inspecionar dados](how-to-custom-speech-inspect-data.md).

1. [Avaliar e melhorar a precisão](how-to-custom-speech-evaluate-data.md) – avalie e melhore a precisão do modelo de conversão de fala em texto. O [portal de fala personalizada](https://speech.microsoft.com/customspeech) fornecerá uma *taxa de erros do Word* , que pode ser usada para determinar se o treinamento adicional é necessário. Se estiver satisfeito com a precisão, você poderá usar as APIs do serviço de fala diretamente. Se você quiser melhorar a precisão por uma média relativa de 5% a 20%, use a guia **treinamento** no portal para carregar dados de treinamento adicionais, como transcrições com rótulo humano e texto relacionado.

1. [Treinar e implantar um modelo](how-to-custom-speech-train-model.md) -melhore a precisão do seu modelo de fala para texto fornecendo transcrições por escrito (10 mil horas) e texto relacionado (<200 MB) junto com seus dados de teste de áudio. Esses dados ajudam a treinar o modelo de conversão de fala em texto. Após o treinamento, o teste e, se estiver satisfeito com o resultado, você poderá implantar seu modelo em um ponto de extremidade personalizado.

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Uma assinatura do serviço de conta e fala do Azure é necessária para que você possa usar o [portal de fala personalizada](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Certifique-se de criar assinaturas padrão (S0), não há suporte para assinaturas gratuitas (F0).

Depois de criar uma conta do Azure e uma assinatura do serviço de fala, você precisará entrar no [portal fala personalizada](https://speech.microsoft.com/customspeech) e conectar-se à sua assinatura.

1. Entre no [portal de fala personalizada](https://aka.ms/custom-speech).
1. Selecione a assinatura na qual você precisa trabalhar e crie um projeto de fala.
1. Se você quiser modificar sua assinatura, use o ícone **engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdo como dados, modelos, testes e pontos de extremidade são organizados em **projetos** no portal de [fala personalizada](https://speech.microsoft.com/customspeech). Cada projeto é específico de um domínio e país/idioma. Por exemplo, você pode criar um projeto para centros de chamadas que usam o inglês no Estados Unidos.

Para criar seu primeiro projeto, selecione a fala de **fala para texto/Personalizada** e clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você deverá ver quatro guias: **dados** , **teste** , **treinamento** e **implantação**. Use os links fornecidos nas [próximas etapas](#next-steps) para aprender a usar cada guia.

> [!IMPORTANT]
> O [portal de fala personalizada](https://aka.ms/custom-speech) foi atualizado recentemente! Se você criou dados, modelos, testes e pontos de extremidade publicados anteriores no portal do CRIS.ai ou com APIs, você precisa criar um novo projeto no novo portal para se conectar a essas entidades antigas.

## <a name="model-lifecycle"></a>Ciclo de vida do modelo

A fala Personalizada usa **modelos de base** e **modelos personalizados**. Cada idioma tem um ou mais **modelos de base**. Geralmente, quando um novo modelo de fala é liberado para o serviço de fala regular, ele também é importado para o serviço de Fala Personalizada como um novo **modelo de base**. Eles são atualizados a cada 3-6 meses, e os modelos mais antigos normalmente se tornam menos úteis ao longo do tempo, pois o modelo mais recente normalmente tem uma precisão significativamente maior.

Por outro lado, **modelos personalizados** são criados adaptando um modelo de base escolhido para um cenário de cliente específico. Você pode continuar usando um modelo personalizado específico por um longo período depois de ter chegado a um que atenda às suas necessidades ou manter o novo treinamento ao longo do tempo com dados adicionais. 

Outros termos principais relacionados ao ciclo de vida do modelo incluem:

* **Adaptação** : pegar um modelo base e personalizá-lo para seu domínio/cenário usando dados de texto e/ou dados de áudio
* **Decodificação** : usando um modelo e executando o reconhecimento de fala (decodificando áudio em texto)
* **Ponto de extremidade** : uma implantação específica do usuário de um modelo base ou de um modelo personalizado que *só* é acessível por um determinado usuário.

### <a name="expiration-timeline"></a>Linha do tempo de expiração

Alterações nos modelos de base para adicionar novas funcionalidades e melhorar o desempenho podem causar problemas com compatibilidade com versões anteriores para modelos mais antigos e também resultar em alterações na precisão observada com um determinado modelo em um conjunto de dados de teste específico. Para gerenciar o esforço de manutenção de modelos e pontos de extremidade, Confira as linhas de tempo a seguir para os prazos de expiração do modelo e do Endpoint.

**Modelos de base** 

* Adaptação: disponível por 1 ano. Depois que o modelo é importado, ele fica disponível por 1 ano para criar modelos personalizados. Após 1 ano, novos modelos personalizados devem ser criados a partir de uma versão mais recente do modelo base.  
* Decodificação: disponível por 2 anos após a importação. Isso significa que você pode criar um ponto de extremidade e usar a transcrição do lote por dois anos com esse modelo. 
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação

**Modelos personalizados**

* Decodificação: disponível por 2 anos após a criação do modelo. Isso significa que você pode usar o modelo personalizado por 2 anos (lote/tempo real/teste) depois que ele é criado. Depois de 2 anos, **você deve treinar novamente seu modelo** , porque com mais frequência o modelo base terá sido preterido para adaptação.  
* Pontos de extremidade: disponíveis na mesma linha do tempo que a decodificação

Quando um modelo de base ou modelo personalizado expira, ele sempre retorna para a **versão mais recente do modelo base**. Portanto, sua implementação nunca será interrompida, mas poderá se tornar menos precisa para *seus dados específicos se os* modelos personalizados atingirem a expiração. Você pode ver a expiração de um modelo nos seguintes locais no portal de Fala Personalizada:

* Resumo do treinamento de modelo
* Detalhes de treinamento do modelo
* Resumo de implantação
* Detalhes da implantação
 
## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar e melhorar a precisão do modelo](how-to-custom-speech-evaluate-data.md)
* [Treinar e implantar um modelo](how-to-custom-speech-train-model.md)
