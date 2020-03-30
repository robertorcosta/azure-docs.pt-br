---
title: Atualize uma oferta existente de Contêineres Azure | Mercado Azure
description: Como atualizar uma oferta de contêiner existente no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 74f97b082c07e17a59a1615c4b1245434c497ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279939"
---
# <a name="update-an-existing-container-offer"></a>Atualizar uma oferta de contêineres existente

Este artigo percorre os diferentes aspectos da atualização de sua oferta de contêiner no [Portal do Cloud Partner](https://cloudpartner.azure.com/).

Há várias razões por que você talvez queira atualizar sua oferta, tais como:

-  Adicionar uma nova versão de imagem de contêiner para SKUs existentes.
-  Adicionando novo SKUs.
-  Atualizar os metadados do mercado para a oferta ou SKUs individuais.

Para ajudá-lo nessas modificações, o portal fornece os recursos **Comparar** e **Histórico**.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Alterações não permitidas na oferta de contêiner ou SKU

Há atributos de uma oferta de contêiner ou SKU que não podem ser alterados depois que a oferta estiver ativa no Azure Marketplace. Você não pode alterar as configurações a seguir:

-  **ID da oferta** e **ID do Publicador** da oferta
-  **ID da SKU** de SKUs existentes
-  Marcas de versão, por exemplo: `1.0.1`
-  Alterações no modelo de cobrança/licença para SKUs existentes

## <a name="common-update-operations"></a>Operações comuns de atualização

As operações de inserção e atualização são comuns.

### <a name="update-container-image-version-for-a-sku"></a>Atualizar a versão da imagem de contêiner para um SKU

É comum que uma imagem de contêiner seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante. Nesse cenário, você deseja atualizar a imagem de contêiner que faz referência a sua SKU usando as seguintes etapas:

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Na guia **SKUs**, clique na SKU associada à imagem de contêiner para atualizar.
4. Em **Imagem de contêiner**, selecione **+ Nova versão da imagem** para adicionar uma nova imagem de contêiner.
5. Forneça as novas **versões de imagem** de contêiner. A versão da imagem precisa seguir as mesmas diretrizes de marcas como versões anteriores. As marcas de versão devem estar no formato X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que você fornecer seja maior que todas as versões anteriores.
6. Clique em **Publicar** para iniciar o fluxo de trabalho e publicar sua nova versão de imagem de contêiner para o Azure Marketplace.

### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar uma nova SKU para sua oferta existente:

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Na guia **SKUs**, selecione **Adicionar nova SKU** e forneça uma **ID DE SKU** na janela pop-up.
4. Republique o contêiner usando as etapas descritas em [Publicar oferta de contêiner](./cpp-publish-offer.md).
5. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova SKU.

### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do marketplace da oferta

Utilize os seguintes passos para atualizar os metadados do mercado associados à sua oferta. (por exemplo, nome da empresa, logotipos, etc.)

1. Entrar no [Portal do Cloud Partner](https://cloudpartner.azure.com/).
2. Em **Todas as ofertas**, localize a oferta que você gostaria de atualizar.
3. Vá para a guia **Marketplace.** Use as instruções na [oferta de oferta de contêiner Publicar](./cpp-publish-offer.md) para fazer alterações de metadados.
4. Selecione **Publicar** para iniciar o fluxo de trabalho para publicar suas alterações.

## <a name="compare-feature"></a>Recurso Comparar

Quando você faz alterações em uma oferta publicada, você pode usar o recurso **Compare** para auditar as alterações que você fez.

### <a name="to-use-the-compare-feature"></a>Para usar o recurso Comparar:

1. A qualquer momento do processo de edição, clique no botão Comparar para sua oferta.
2. Veja as versões lado a lado de metadados e ativos de marketing.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade de histórico de publicação, selecione a guia **Histórico** na barra de menu de navegação esquerda do Portal do Cloud Partner. Você pode ver as ações de data/hora executadas durante o tempo de vida das ofertas do Marketplace do Azure.
