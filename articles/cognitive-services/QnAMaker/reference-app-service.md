---
title: Configuração de serviço-QnA Maker
description: Entenda como e onde configurar os recursos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776707"
---
# <a name="service-configuration"></a>Configuração de serviço

O QnA Maker usa vários recursos do Azure (serviços), incluindo Pesquisa Cognitiva, serviço de aplicativo, plano do serviço de aplicativo e Application Insights.

Todas as personalizações para essas configurações com suporte pelo QnA Maker estão listadas abaixo.

## <a name="app-service"></a>Serviço de Aplicativo

QnA Maker usa o serviço de aplicativo para fornecer o tempo de execução de consulta usado pela [API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Essas configurações estão disponíveis no portal do Azure, para o serviço de aplicativo. As configurações estão disponíveis selecionando **configurações**e, em seguida, **configuração**.

Você pode definir uma configuração individual por meio da lista de configurações do aplicativo ou modificar várias configurações selecionando **edição avançada**.

|Recurso|Configuração|
|--|--|
|AzureSearchAdminKey|Pesquisa Cognitiva-usado para o armazenamento e o classificador do QnA Pair #1|
|AzureSearchName|Pesquisa Cognitiva-usado para o armazenamento e o classificador do QnA Pair #1|
|Defaultanswer|Texto de resposta quando nenhuma correspondência for encontrada|
|UserAppInsightsAppId|Log de chat e telemetria|
|UserAppInsightsKey|Log de chat e telemetria|
|UserAppInsightsName|Log de chat e telemetria|

Saiba [como adicionar a alteração do serviço de pesquisa cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) para o serviço.

Você precisa **reiniciar** o serviço na página **visão geral** do portal do Azure, quando terminar de fazer alterações.

## <a name="qna-maker-service"></a>Serviço QnA Maker

O serviço de QnA Maker fornece configuração para que os usuários a seguir colaborem em um único serviço de QnA Maker e todas as suas bases de dados de conhecimento.

Saiba [como adicionar colaboradores](./how-to/collaborate-knowledge-base.md) ao seu serviço.

## <a name="application-insights"></a>Application Insights

Application Insights não tem definições de configuração específicas para QnA Maker.

## <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

O plano do serviço de aplicativo não tem definições de configuração específicas para QnA Maker.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [formatos](reference-document-format-guidelines.md) de documentos e URLs que você deseja importar para uma base de dados de conhecimento.