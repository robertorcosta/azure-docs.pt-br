---
title: Conectar a Azure front door Premium à sua origem com o link privado
description: Saiba como conectar sua Azure front door Premium à sua origem com o serviço de vínculo privado usando o portal do Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098330"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Conectar a Azure front door Premium à sua origem com o link privado

Este artigo orientará você sobre como configurar o SKU Premium da porta frontal do Azure para se conectar aos seus aplicativos hospedados em uma rede virtual usando o serviço de link privado do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Crie um serviço de [vínculo privado](../../private-link/create-private-link-service-portal.md) para seus servidores Web de origem.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Habilitar ponto de extremidade privado no serviço de porta de recepção do Azure

Nesta seção, você mapeará o serviço de vínculo privado do Azure para um ponto de extremidade privado criado na rede privada do SKU Premium da porta do Azure. 

1. No perfil Premium da porta de recepção do Azure, em *configurações*, selecione **grupos de origem**.

1. Selecione o grupo de origem que contém a origem à qual você deseja habilitar o link privado.

1. Selecione **+ Adicionar uma origem** para adicionar uma nova origem ou selecione uma origem criada anteriormente na lista. Em seguida, marque a caixa de seleção para **habilitar o serviço de vínculo privado**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Captura de tela da habilitação do link privado na página Adicionar uma origem.":::

1. Para **selecionar um recurso do Azure**, selecione **em meu diretório**. Selecione ou digite a configuração a seguir para configurar o recurso ao qual você deseja que o Azure front door Premium se conecte com particular.
    
    | Configuração | Valor |
    | ------- | ----- |
    | Região | Selecione a região que é a mesma ou mais próxima de sua origem. |
    | Tipo de recurso | Selecione **Microsoft.Network/privateLinkServices**. |
    | Recurso | Selecione **myPrivateLinkService**. |
    | Sub-recurso de destino | Deixe esse campo em branco. |
    | Mensagem de solicitação | Personalize a mensagem ou escolha a mensagem padrão. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [link privado do Azure front door Premium](concept-private-link.md).
