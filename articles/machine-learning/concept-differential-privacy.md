---
title: Implementar a privacidade diferencial com o pacote WhiteNoise (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba o que é a privacidade diferencial e como o pacote WhiteNoise pode ajudar você a implementar sistemas privados diferenciais que preservam a privacidade dos dados.
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.openlocfilehash: 9728bf2c86c0629b09e2325650ce288cf9b3cc7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86199802"
---
# <a name="preserve-data-privacy-by-using-differential-privacy-and-the-whitenoise-package-preview"></a>Preserve a privacidade dos dados usando a privacidade diferencial e o pacote WhiteNoise (versão prévia)

Saiba o que é a privacidade diferencial e como o pacote WhiteNoise pode ajudar você a implementar sistemas com privacidade diferencial.

À medida que aumenta a quantidade de dados que uma organização coleta e usa para as análises, também aumentam as preocupações de privacidade e segurança. As análises exigem dados. Normalmente, quanto mais dados são usados para treinar modelos, mais precisos eles são. Quando informações pessoais são usadas para essas análises, é especialmente importante que os dados permaneçam privados durante todo o uso deles.

> [!NOTE]
> Observe que estamos renomeando o kit de ferramentas e vamos introduzir o novo nome nas próximas semanas. 

## <a name="how-differential-privacy-works"></a>Como funciona a privacidade diferencial

A privacidade diferencial é um conjunto de sistemas e práticas que ajuda a manter os dados de indivíduos seguros e privados.

> [!div class="mx-imgBorder"]
> ![Processo de privacidade diferencial](./media/concept-differential-privacy/differential-privacy-process.jpg)

Em cenários tradicionais, os dados brutos são armazenados em arquivos e bancos de dados. Quando os usuários analisam dados, eles normalmente usam os dados brutos. Essa é uma preocupação, pois pode infringir a privacidade de um indivíduo. A privacidade diferencial tenta lidar com esse problema adicionando "ruído" ou aleatoriedade aos dados, de modo que os usuários não possam identificar pontos de dados individuais. No mínimo, esse sistema fornece uma negação plausível.

Em sistemas com privacidade diferencial, os dados são compartilhados por meio de solicitações chamadas **consultas**. Quando um usuário envia uma consulta de dados, as operações conhecidas como **mecanismos de privacidade** adicionam ruído aos dados solicitados. Os mecanismos de privacidade retornam uma *aproximação dos dados* em vez dos dados brutos. Este resultado de preservação da privacidade é exibido em um **relatório**. Os relatórios consistem em duas partes, os dados reais computados e uma descrição de como os dados foram criados.

## <a name="differential-privacy-metrics"></a>Métricas de privacidade diferencial

A privacidade diferencial tenta fornecer proteção contra a possibilidade de que um usuário produza um número indefinido de relatórios para acabar revelando os dados confidenciais. Um valor conhecido como **épsilon** mede o nível de ruído ou de privacidade de um relatório. O épsilon tem uma relação inversa ao ruído ou à privacidade. Quanto menor o épsilon, mais ruído (e mais privacidade) os dados têm.

Os valores do épsilon não são negativos. Os valores abaixo de 1 fornecem uma negação plausível total. Qualquer valor acima de 1 apresenta um risco maior de exposição dos dados reais. À medida que você implementa sistemas com privacidade diferencial, o ideal é produzir relatórios com valores de épsilon entre 0 e 1.

Outro valor correlacionado diretamente ao épsilon é o **delta**. O delta é uma medida da probabilidade de que um relatório não seja totalmente privado. Quanto maior o delta, maior o épsilon. Como esses valores são correlacionados, o épsilon é usado com mais frequência.

## <a name="privacy-budget"></a>Orçamento de privacidade

Para garantir a privacidade em sistemas nos quais várias consultas são permitidas, a privacidade diferencial define um limite de taxa. Esse limite é conhecido como **orçamento de privacidade**. Os orçamentos de privacidade recebem um valor de épsilon, geralmente, entre 1 e 3 para limitar o risco de nova identificação. À medida que os relatórios são gerados, os orçamentos de privacidade controlam o valor de épsilon dos relatórios individuais, bem como a agregação de todos os relatórios. Depois que um orçamento de privacidade é gasto ou esgotado, os usuários não podem mais acessar os dados.  

## <a name="reliability-of-data"></a>Confiabilidade dos dados

Embora a preservação da privacidade deva ser a meta, há uma desvantagem quando se trata da usabilidade e da confiabilidade dos dados. Na análise de dados, é possível considerar a precisão como uma medida de incerteza introduzida por erros de amostragem. Essa incerteza tende a se enquadrar em determinados limites. A **precisão**, da perspectiva da privacidade diferencial, mede a confiabilidade dos dados, que é afetada pela incerteza introduzida pelos mecanismos de privacidade. Em resumo, um nível mais alto de ruído ou de privacidade se traduz em dados que têm um épsilon, uma precisão e uma confiabilidade menores. Embora os dados sejam mais privados, porque eles não são confiáveis, é menos provável que eles sejam usados.

## <a name="implementing-differentially-private-systems"></a>Como implementar sistemas com privacidade diferencial

É difícil implementar sistemas com privacidade diferencial. O WhiteNoise é um projeto de software livre que contém diferentes componentes para a criação de sistemas privados diferenciais globais. Ele é composto pelos seguintes componentes de nível superior:

- Núcleo
- Sistema

### <a name="core"></a>Núcleo

A biblioteca principal inclui os seguintes mecanismos de privacidade para implementar um sistema com privacidade diferencial:

|Componente  |Descrição  |
|---------|---------|
|Análise     | Uma descrição em grafo de cálculos arbitrários. |
|Validador     | Uma biblioteca Rust que contém um conjunto de ferramentas para verificar e obter as condições necessárias para que uma análise tenha privacidade diferencial.          |
|Runtime     | A média usada para executar a análise. O runtime de referência é escrito em Rust, mas os runtimes podem ser escritos com qualquer estrutura computacional, como o SQL e o Spark, dependendo das suas necessidades de dados.        |
|Associações     | Associações de linguagem e bibliotecas auxiliares para criar as análises. Atualmente, o WhiteNoise fornece associações do Python. |

### <a name="system"></a>Sistema

A biblioteca do sistema fornece as seguintes ferramentas e os seguintes serviços para trabalhar com os dados de tabela e relacionais:

|Componente  |Descrição  |
|---------|---------|
|Acesso aos Dados     | Biblioteca que intercepta e processa consultas SQL e produz relatórios. Esta biblioteca é implementada em Python e dá suporte às seguintes fontes de dados ODBC e DBAPI:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Serviço     | Serviço de execução que fornece um ponto de extremidade REST para atender a solicitações ou consultas em fontes de dados compartilhadas. O serviço foi projetado para permitir a composição de módulos de privacidade diferencial que operam em solicitações que contêm diferentes valores de delta e épsilon, também conhecidas como solicitações heterogêneas. Essa implementação de referência é responsável por um impacto adicional nas consultas em dados correlacionados. |
|Avaliador     | Avaliador aleatório que verifica violações de privacidade, precisão e desvio. O avaliador dá suporte aos seguintes testes: <ul><li>Teste de privacidade: determina se um relatório segue as condições de privacidade diferencial.</li><li>Teste de precisão: mede se a confiabilidade dos relatórios se enquadra nos limites superior e inferior, dado um nível de confiança de 95%.</li><li>Teste de utilitário: determina se os limites de confiança de um relatório estão próximos o suficiente dos dados e ainda maximizam a privacidade.</li><li>Teste de desvio: mede a distribuição de relatórios de consultas repetidas para garantir que elas não fiquem desequilibradas</li></ul> |

## <a name="next-steps"></a>Próximas etapas

[Preserve a privacidade de dados](how-to-differential-privacy.md) no Azure Machine Learning.

Para saber mais sobre os componentes do WhiteNoise, confira os repositórios do GitHub para o [pacote do WhiteNoise Core](https://github.com/opendifferentialprivacy/whitenoise-core), o [pacote do sistema WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-system) e os [exemplos do WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-samples).