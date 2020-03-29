---
title: Aprenda conceitos de ferramentas de revisão - Moderador de conteúdo
titleSuffix: Azure Cognitive Services
description: Conheça a ferramenta Content Moderator Review, um site que coordena um esforço combinado de IA e moderação de revisão humana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169499"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de revisão de moderadores de conteúdo

O Azure Content Moderator fornece serviços para combinar moderação de conteúdo de aprendizado de máquina com comentários humanos, e o site da [ferramenta Derevisão](https://contentmoderator.cognitive.microsoft.com) é um front-end amigável que dá acesso detalhado a esses serviços.

![O painel de ferramentas de revisão em um navegador](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

A [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com)quando usada em conjunto com as APIs de moderação assistidas pela máquina, permite que você realize as seguintes tarefas no processo de moderação de conteúdo:

- Use um conjunto de ferramentas para moderar o conteúdo em vários formatos (texto, imagem e vídeo).
- Automatize a criação de [avaliações humanas](../review-api.md#reviews) quando os resultados da API de moderação chegarem.
- Atribuir ou escalar avaliações de conteúdo para várias equipes de revisão, organizadas por categoria de conteúdo ou nível de experiência.
- Use filtros lógicos padrão ou personalizados[(fluxos de trabalho)](../review-api.md#workflows)para classificar e rastrear conteúdo, sem escrever nenhum código.
- Use [conectores](./configure.md#connectors) para processar conteúdo com os serviços Microsoft PhotoDNA, Text Analytics e Face, além das APIs do Moderador de Conteúdo.
- Crie seu próprio conector para criar fluxos de trabalho para qualquer API ou processo de negócios.
- Obtenha métricas de desempenho chave em seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Painel de ferramentas de revisão

Na guia **Painel,** você pode ver as principais métricas para revisões de conteúdo feitas dentro da ferramenta. Veja o número de avaliações totais, completas e pendentes de conteúdo de imagem, texto e vídeo. Você também pode ver a divisão de usuários e equipes que completaram as avaliações, bem como as tags de moderação que foram aplicadas.

![Exibir painel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Revisar credenciais de ferramentas

Quando você se inscrever com a [ferramenta Revisão,](https://contentmoderator.cognitive.microsoft.com)você será solicitado a selecionar uma região Azure para sua conta. Isso porque a [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços do Azure Content Moderator; você precisará desta chave para acessar qualquer um dos serviços de uma chamada REST ou SDK do cliente. Você pode visualizar sua URL de ponto final de chave e API selecionando**Credenciais**de **configurações** > .

![Credenciais do Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Próximas etapas

Consulte [Configurar a ferramenta 'Revisar'](./configure.md) para saber como acessar os recursos da ferramenta Revisar e alterar as configurações.