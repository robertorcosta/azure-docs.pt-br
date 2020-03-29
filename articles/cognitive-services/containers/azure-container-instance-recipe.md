---
title: Receita de Instância do Recipiente do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar contêineres de serviços cognitivos na instância de contêineres do Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717007"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implantar e executar em Instância de Contêiner do Azure

Com as etapas a seguir, dimensione facilmente as aplicações do Azure Cognitive Services na nuvem com [instâncias de contêiner do](https://docs.microsoft.com/azure/container-instances/)Azure . A containerização ajuda você a se concentrar na construção de seus aplicativos em vez de gerenciar a infra-estrutura. Para obter mais informações sobre o uso de contêineres, consulte [recursos e benefícios](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer recipiente de Serviços Cognitivos. O recurso serviço cognitivo deve ser criado no portal Azure antes de usar a receita. Cada Serviço Cognitivo que suporta contêineres tem um documento "Como instalar" especificamente para instalar e configurar o serviço para um contêiner. Alguns serviços exigem um arquivo ou conjunto de arquivos como entrada para o contêiner, é importante que você entenda e tenha usado o contêiner com sucesso antes de usar esta solução.

* Um recurso de Serviço Cognitivo, criado no portal Azure.
* URL **de ponto final** do Serviço Cognitivo - revise o "Como instalar" do seu serviço específico para o contêiner, para descobrir de onde a URL de ponto final é do portal do Azure e como é um exemplo correto da URL. O formato exato pode mudar de serviço para serviço.
* Tecla **key** Cognitive Service - as chaves estão na página **Chaves** para o recurso Do Zure. Você precisa apenas de uma das duas chaves. A chave é uma seqüência de 32 caracteres alfa-numéricos.
* Um único contêiner de serviços cognitivos em seu host local (seu computador). Certifique-se de que você pode:
  * Puxe a imagem `docker pull` com um comando.
  * Execute o contêiner local com sucesso com `docker run` todas as configurações necessárias com um comando.
  * Ligue para o ponto final do contêiner, recebendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis em `<>`suportes angulares, precisam ser substituídas por seus próprios valores. Esta substituição inclui os suportes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Use a instância do contêiner

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55`.
1. Abra uma nova guia do navegador e `http://<IP-address>:5000 (http://55.55.55.55:5000`use o endereço IP, por exemplo, ). Você verá a página inicial do contêiner, informando que o contêiner está em execução.

1. Selecione **API de serviço descrição** para exibir a página de swagger para o contêiner.

1. Selecione qualquer uma das APIs **post** e **selecione Experimente- a**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **Executar** para enviar a solicitação à sua instância de contêiner.

    Você criou e usou com sucesso contêineres de Serviços Cognitivos na Instância de Contêiner do Azure.
