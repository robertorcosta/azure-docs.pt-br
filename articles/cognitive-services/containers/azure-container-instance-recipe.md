---
title: Receita da instância de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar contêineres de serviços cognitivas na instância de contêiner do Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876650"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implantar e executar em Instância de Contêiner do Azure

Com as etapas a seguir, dimensione facilmente os aplicativos de serviços cognitivas do Azure na nuvem com as [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. A criação de contêineres ajuda você a se concentrar em criar seus aplicativos em vez de gerenciar a infraestrutura. Para obter mais informações sobre como usar contêineres, consulte [recursos e benefícios](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer contêiner de serviços cognitivas. O recurso de serviço cognitiva deve ser criado no portal do Azure antes de usar a receita. Cada serviço cognitiva que dá suporte a contêineres tem um documento "como instalar", especificamente para instalar e configurar o serviço para um contêiner. Alguns serviços exigem um arquivo ou conjunto de arquivos como entrada para o contêiner, é importante que você entenda e tenha usado o contêiner com êxito antes de usar essa solução.

* Um recurso de serviço cognitiva, criado em portal do Azure.
* URL do **ponto de extremidade** de serviço cognitiva-examine o "como instalar" do seu serviço específico para o contêiner, para descobrir onde a URL do ponto de extremidade está dentro do portal do Azure e a aparência do exemplo correto da URL. O formato exato pode mudar de serviço para serviço.
* **Chave** de serviço cognitiva-as chaves estão na página **chaves** do recurso do Azure. Você precisa apenas de uma das duas chaves. A chave é uma cadeia de caracteres de 32 alfanuméricos.
* Um único contêiner de serviços cognitivas no host local (seu computador). Verifique se você pode:
  * Puxe a imagem com um `docker pull` comando.
  * Execute o contêiner local com êxito com todas as definições de configuração necessárias com um `docker run` comando.
  * Chame o ponto de extremidade do contêiner, obtendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis entre colchetes angulares, `<>` precisam ser substituídas por seus próprios valores. Essa substituição inclui os colchetes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Usar a instância de contêiner

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55` .
1. Abra uma nova guia do navegador e use o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Você verá o home page do contêiner, informando que o contêiner está em execução.

1. Selecione **Descrição da API de serviço** para exibir a página do Swagger para o contêiner.

1. Selecione qualquer uma das APIs **post** e selecione **experimentar**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **executar** para enviar a solicitação para a instância de contêiner.

    Você criou e usou contêineres de serviços cognitivas na instância de contêiner do Azure.
