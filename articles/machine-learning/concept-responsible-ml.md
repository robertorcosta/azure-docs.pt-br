---
title: Machine Learning (ML) Responsável
titleSuffix: Azure Machine Learning
description: Saiba o que é o ML Responsável e como usá-lo no Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 09bb7710df19482e9625c1834d2646f70368fc1d
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920361"
---
# <a name="responsible-machine-learning-ml"></a>Machine Learning (ML) Responsável

Neste artigo, você aprenderá o que é ML Responsável e as maneiras que você pode colocá-lo em prática com o Azure Machine Learning.

Ao longo de todo o desenvolvimento e uso de sistemas de IA, a confiança deve ser o centro de tudo. Confie na plataforma, no processo e nos modelos. Na Microsoft, o ML Responsável abrange os seguintes valores e princípios:

- Entender modelos de machine learning
  - Interpretar e explicar o comportamento do modelo
  - Avaliar e mitigar a imparcialidade do modelo
- Proteger pessoas e seus dados
  - Evitar a exposição de dados com privacidade diferencial  
- Controlar o processo de machine learning de ponta a ponta
  - Documentar o ciclo de vida de machine learning com folhas de dados

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Pilares do ML Responsável":::

Conforme a inteligência artificial e os sistemas autônomos integram-se cada vez mais na sociedade, é importante fazer um esforço proativo para antecipar e mitigar as consequências indesejadas dessas tecnologias.

## <a name="interpret-and-explain-model-behavior"></a>Interpretar e explicar o comportamento do modelo

Os sistemas de caixa alta ou opaca podem ser problemáticos porque dificulta para os participantes como desenvolvedores de sistemas, reguladores, usuários e tomadores de decisões de negócios entenderem por que os sistemas tomam determinadas decisões. Alguns sistemas de IA são mais explicativos do que outros e, às vezes, há uma compensação entre um sistema com maior precisão e um que é mais explicativo.

Para criar sistemas de IA interpretáveis, use [InterpretML](https://github.com/interpretml/interpret), um pacote de software livre criado pela Microsoft. [O InterpretML pode ser usado dentro do Azure Machine Learning](how-to-machine-learning-interpretability.md) para [interpretar e explicar seus modelos de machine learning](how-to-machine-learning-interpretability-aml.md), incluindo [modelos de machine learning automatizado](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Avaliar e mitigar a imparcialidade do modelo

Conforme os sistemas de IA ficam mais envolvidos na tomada de decisões diárias da sociedade, é de extrema importância que esses sistemas funcionam bem para fornecer resultados justos para todos.

A parcialidade nos sistemas de IA pode resultar nas seguintes consequências indesejadas:

- Alguns indivíduos não recebem oportunidades, recursos ou informações.
- Tendenciosidade e estereótipos são reforçados.

Muitos aspectos da imparcialidade não podem ser capturados ou representados por métricas. Há ferramentas e práticas que podem melhorar a imparcialidade no design e no desenvolvimento de sistemas de IA.

Duas etapas principais na redução da parcialidade em sistemas de IA são a avaliação e a mitigação. Recomendamos [FairLearn](https://github.com/fairlearn/fairlearn), um pacote de software livre que pode avaliar e mitigar a possível parcialidade de sistemas de IA. Para saber mais sobre a imparcialidade e o pacote FairLearn, consulte o [artigo de Imparcialidade em ML](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Evitar a exposição de dados com privacidade diferencial

Quando os dados são usados para análise, é importante que os dados permaneçam privados e confidenciais durante todo o uso. A privacidade diferencial é um conjunto de sistemas e práticas que ajuda a manter os dados de indivíduos seguros e privados.

Em cenários tradicionais, os dados brutos são armazenados em arquivos e bancos de dados. Quando os usuários analisam dados, eles normalmente usam os dados brutos. Essa é uma preocupação, pois pode infringir a privacidade de um indivíduo. A privacidade diferencial tenta lidar com esse problema adicionando “ruído” ou aleatoriedade aos dados, de modo que os usuários não possam identificar pontos de dados individuais.

É difícil implementar sistemas com privacidade diferencial. O [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) é um projeto de software livre que contém diferentes componentes para a criação de sistemas privados diferenciais globais. Para saber mais sobre a privacidade diferencial e o projeto WhiteNoise, consulte o artigo [preservar a privacidade de dados usando a privacidade diferencial e WhiteNoise](./concept-differential-privacy.md).

> [!NOTE]
> Observe que estamos renomeando o kit de ferramentas e vamos introduzir o novo nome nas próximas semanas. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documentar o ciclo de vida de machine learning com folhas de dados

Documentar as informações certas no processo de machine learning é fundamental para tomar decisões responsáveis em cada estágio. As folhas de dados são uma maneira de documentar os ativos de machine learning que são usados e criados como parte do ciclo de vida do machine learning.

Os modelos tendem a ser considerados como "caixas opacas" e, muitas vezes, há poucas informações sobre eles. Como os sistemas de machine learning estão se tornando mais difundidos e são usados para tomada de decisões, o uso de folhas de dados é uma etapa para o desenvolvimento de sistemas de machine learning mais responsáveis.

Algumas informações do modelo que você pode querer documentar como parte de uma folha de dados:

- Uso pretendido
- Arquitetura do modelo
- Dados de treinamento usados
- Dados de avaliação usados
- Métricas de desempenho do modelo de treinamento
- Informações de imparcialidade.

Consulte o exemplo a seguir para saber como usar o SDK do Azure Machine Learning para implementar [folhas de dados para modelos](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Recursos adicionais

- Use a criptografia homomórficas para [implantar um serviço Web inferência criptografado](how-to-homomorphic-encryption-seal.md).
- Saiba mais sobre o conjunto de diretrizes em [Sobre o ML](https://www.partnershiponai.org/about-ml/) para ver a documentação do sistema de machine learning.
