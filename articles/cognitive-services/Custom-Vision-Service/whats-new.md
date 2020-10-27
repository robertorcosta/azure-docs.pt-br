---
title: Novidades na Visão Personalizada.
titleSuffix: Azure Cognitive Services
description: Este artigo contém notícias sobre a Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 4fbfbd1d578eb77d50d91413efb9f9fbf28e115f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146315"
---
# <a name="whats-new-in-custom-vision"></a>Novidades na Visão Personalizada

Conheça o que há de novo no serviço. Esses itens podem ser notas sobre a versão, vídeos, postagens no blog e outros tipos de informações. Marque esta página para manter-se atualizado quanto ao serviço.

## <a name="july-2020"></a>Julho de 2020

### <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

* A Visão Personalizada dá suporte ao RBAC (controle de acesso baseado em função) do Azure, um sistema de autorização para gerenciar o acesso individual aos recursos do Azure. Para saber como gerenciar o acesso aos seus projetos de Visão Personalizada, confira [Controle de acesso baseado em função do Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Treinamento de subconjunto

* Quando você estiver treinando um projeto de detecção de objetos, você poderá opcionalmente treinar apenas um subconjunto de suas marcas aplicadas. Talvez seja interessante fazer isso se você ainda não tiver aplicado um número suficiente de determinadas marcas, mas tiver outras delas. Siga o [Início rápido da biblioteca de clientes](./quickstarts/object-detection.md) para C# ou Python para saber mais.

### <a name="azure-storage-notifications"></a>Notificações de armazenamento do Azure

* Você pode integrar seu projeto de Visão Personalizada a uma fila de armazenamento de blobs do Azure para receber notificações por push de uma atividade de treinamento/exportação de projetos e cópias de backup de modelos publicados. Esse recurso é útil para evitar a sondagem contínua do serviço em busca de resultados quando operações longas estão em execução. Em vez disso, você pode integrar as notificações de fila de armazenamento ao seu fluxo de trabalho. Confira o guia [Integração de armazenamento](./storage-integration.md) para saber mais.

### <a name="copy-and-move-projects"></a>Copiar e mover projetos

* Agora você pode copiar projetos de uma conta de Visão Personalizada para outras. Talvez seja interessante mover um projeto de um ambiente de desenvolvimento para um de produção ou fazer backup de um projeto para uma conta em uma região do Azure diferente a fim de aumentar a segurança dos dados. Confira o guia [Copiar e mover projetos](./copy-move-projects.md) para saber mais.

## <a name="september-2019"></a>Setembro de 2019

### <a name="suggested-tags"></a>Marcas sugeridas

* A ferramenta Smart Labeler no [site da Visão Personalizada](https://www.customvision.ai/) gera marcas sugeridas para suas imagens de treinamento. Isso permite que você rotule um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada. Para obter instruções sobre como usar esse recurso, confira [Marcas sugeridas](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Atualizações dos Serviços Cognitivos

[Comunicados de atualização do Azure para os Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)