---
title: Provisão um gateway auto-hospedado no Azure API Management | Microsoft Docs
description: Saiba como prover um gateway auto-hospedado no Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075281"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Provisão de um gateway auto-hospedado no Azure API Management

O provisionamento de um recurso de gateway na instância de gerenciamento de API do Azure é um pré-requisito para implantar um gateway auto-hospedado. Este artigo percorre as etapas para prover um recurso gateway no Gerenciamento de API.

> [!NOTE]
> O recurso gateway auto-hospedado está em visualização. Durante a visualização, o gateway auto-hospedado está disponível apenas nos níveis Desenvolvedor e Premium sem nenhum custo adicional. O nível do desenvolvedor está limitado a uma única implantação de gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Provisionar um gateway auto-hospedado

1. Selecione os **Gateways** em **Configurações**.
2. Clique **+ Adicionar**.
3. Digite o **Nome** e **a Região** do gateway.
> [!TIP]
> **A região** especifica a localização pretendida dos nós de gateway que serão associados a este recurso de gateway. É semanticamente equivalente a uma propriedade semelhante associada a qualquer recurso do Azure, mas pode ser atribuído um valor de string arbitrário.

4. Opcionalmente, digite uma **descrição** do recurso gateway.
5. Opcionalmente, **+** selecione em **APIs** para associar uma ou mais APIs a este recurso de gateway.
> [!TIP]
> Você pode associar e remover uma API de um gateway na guia **Configurações** da API.

> [!IMPORTANT]
> Por padrão, nenhuma das APIs existentes será associada ao novo recurso de gateway. Portanto, as tentativas de invocá-los `404 Resource Not Found` através do novo gateway resultarão em respostas.

6. Clique em **Adicionar**.

Agora, o recurso gateway foi provisionado na instância de gerenciamento de API. Você pode continuar a implantar o gateway.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, consulte a visão geral do gateway autohospedado do [Azure API Management](self-hosted-gateway-overview.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
