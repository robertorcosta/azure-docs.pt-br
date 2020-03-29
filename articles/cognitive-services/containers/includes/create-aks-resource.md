---
title: Crie um recurso de cluster do Azure Kubernetes Service
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383485"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Crie um recurso de cluster do Azure Kubernetes Service

1. Vá para [o Serviço Azure Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks)e selecione **Criar**.

1. Na guia **Noções Básicas,** digite as seguintes informações:

    |Configuração|Valor|
    |--|--|
    |Subscription|Selecione a assinatura apropriada.|
    |Resource group|Selecione um grupo de recursos disponível.|
    |Nome do cluster kubernetes|Digite um nome (minúscula).|
    |Região|Selecione um local próximo.|
    |Versão Kubernetes|Qualquer valor marcado como **(padrão)**.|
    |Prefixo de nome DNS|Criado automaticamente, mas você pode substituir.|
    |Tamanho do nó|Padrão DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Contagem de nodos|Deixe o controle deslizante no valor padrão.|

1. Na guia **Escala,** deixe os **nós virtuais** e os **conjuntos de escala vm definidos** como valores padrão.
1. Na guia **Autenticação,** deixe o **diretor do serviço** e a modo **RBAC** definido como valores padrão.
1. Na guia **Rede, digite** as seguintes seleções:

    |Configuração|Valor|
    |--|--|
    |Roteamento de aplicativo HTTP|Não|
    |Configuração de rede|Basic|

1. Na guia **Monitoramento,** **certifique-se de** que o monitoramento de contêiner enable está definido como **Sim**e deixe o **espaço de trabalho do Log Analytics** como o valor padrão.
1. Na guia **Tags,** deixe os pares nome/valor em branco por enquanto.
1. Selecione **Revisar e Criar**.
1. Após a validação passar, selecione **Criar**.

> [!NOTE]
> Se a validação falhar, pode ser por causa de um erro "Service principal". Volte para a guia **Autenticação** e, em seguida, volte para **Resenha + criar,** onde a validação deve ser executada e depois passar.
