---
title: Novidades - Compreensão da Linguagem (LUIS)
description: Este artigo é regularmente atualizado com notícias sobre a API de compreensão da linguagem de serviços cognitivos do Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538011"
---
# <a name="whats-new-in-language-understanding"></a>O que há de novo na compreensão da linguagem

Conheça o que há de novo no serviço. Esses itens incluem notas de lançamento, vídeos, postagens de blog e outros tipos de informações. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="release-notes"></a>Notas de versão

### <a name="march-2020"></a>Março de 2020

* O TLS 1.2 agora é aplicado para todas as solicitações HTTP para este serviço. Para obter mais informações, consulte [a segurança dos Serviços Cognitivos do Azure](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 de novembro de 2019 - Ignite

* Vídeo - [Modelos avançados de compreensão da linguagem natural (NLU) utilizando luis e azure cognitive services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Melhor produtividade do desenvolvedor
    * Disponibilidade geral do nosso [ponto final de previsão V3](luis-migration-api-v3.md).
    * Capacidade de importar e exportar aplicativos com o formato[.luDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown). Isso abre caminho para um processo eficaz de CI/CD.
* Expansão da linguagem
    * [Árabe e hindi](luis-language-support.md) em pré-visualização pública.
* Modelos predefinidos
    * [Domínios pré-construídos](luis-reference-prebuilt-domains.md) agora estão geralmente disponíveis (GA)
    * Entidades [japonesas pré-construídas](luis-reference-prebuilt-entities.md#japanese-entity-support) - idade, moeda, número e porcentagem não são suportadas em V3.
    * Entidades [italianas pré-construídas](luis-reference-prebuilt-entities.md#italian-entity-support) - idade, moeda, dimensão, número e resolução percentual alteradas de V2.
* Experiência aprimorada do usuário em [preview.luis.ai portal](https://preview.luis.ai) - experiência de rotulagem renovada para permitir a construção e depuração de modelos complexos. Experimente os tutoriais do portal de pré-visualização:
    * [Apenas intenções](tutorial-intents-only.md)
    * [Entidade descomposável de aprendizagem de máquina](tutorial-machine-learned-entity.md)
* Recursos avançados de compreensão da linguagem - [construindo modelos de linguagem sofisticados](luis-concept-entity-types.md) com menos esforço.
* Defina os recursos de aprendizado de máquina no nível do modelo e permita que os modelos sejam usados como sinais para outros modelos, por exemplo, usando entidades como recursos para intenções e para outras entidades.
* Novos [limites](luis-boundaries.md) expandidos - máximo mais alto para listas de frases e frases totais, novo modelo como um limite de recursos
* Extrair informações do texto no formato de estrutura de hierarquia profunda, tornando os aplicativos de conversação mais poderosos.

    ![imagem de entidade aprendida por máquina](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de setembro de 2019

* Recurso de autoria do Azure - [migrar agora](luis-migration-authoring.md).
    * 500 aplicativos por recurso do Azure
    * 100 versões por aplicativo
* Apoio turco a entidades pré-construídas
* Suporte italiano para datetimeV2

### <a name="july-23-2019"></a>23 de julho de 2019

* Atualize o [Texto de Reconhecimento para](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 1.2.3
    * Reconhecimentos de idade, temperatura, dimensão e moeda em italiano.
    * Melhoria no reconhecimento de feriados em inglês para calcular corretamente as datas baseadas no Dia de Ação de Graças.
    * Melhorias no French DateTime para reduzir falsos positivos de entidades não datadas e não-tempo.
    * Suporte para calendário/escola/ano fiscal e siglas em Inglês DateRange.
    * Reconhecimento de número de telefone melhorado em chinês e japonês.
    * Suporte aprimorado para NumberRange em inglês.
    * Melhorias de desempenho.

### <a name="june-24-2019"></a>24 de junho de 2019

* [OrdinalV2 entidade pré-construída](luis-reference-prebuilt-ordinal-v2.md) para apoiar pedidos como o próximo, anterior e último. Só cultura inglesa.

### <a name="may-6-2019---build-conference"></a>6 de maio de 2019 - //Conferência de Construção

Os seguintes recursos foram lançados na Conferência Build 2019:

* [Pré-visualização do guia de migração da API V3](luis-migration-api-v3.md)
* [Painel de análise aprimorado](luis-how-to-use-dashboard.md)
* [Domínios pré-construídos aprimorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Ambas as estruturas](https://blog.botframework.com/)

## <a name="videos"></a>vídeos

### <a name="2019-ignite-videos"></a>Vídeos de Ignite 2019

[Modelos avançados de Compreensão da Linguagem Natural (NLU) utilizando serviços cognitivos LUIS e Azure | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Construa vídeos

[Como usar a IA do Azure Conversational para escalar seu negócio para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Atualizações de serviço

[Comunicados de atualização do Azure para os Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)
