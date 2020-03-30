---
title: SKUs privados e planos | Mercado Azure
description: Como usar SKUs privados para gerenciar a disponibilidade de oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280381"
---
<a name="private-skus-and-plans"></a>SKUs Privados e Planos
============

SKUs privados permitem restringir a disponibilidade de SKUs para clientes específicos. Quando um SKU é marcado como privado, ele não está disponível em nenhum catálogo público, incluindo no [Azure Marketplace](https://azuremarketplace.microsoft.com) e no [portal do Azure](https://portal.azure.com). No portal do Azure, somente os clientes com acesso ao SKU podem vê-lo. Além disso, eles também receberiam um aviso de que têm acesso a ofertas privadas.

>[!NOTE]
>SKUs privados devem ter novas IDs de plano/SKU exclusivas para evitar qualquer conflito com seus SKUs públicos.

Você pode usar SKUs privados para lidar com os seguintes cenários:

1.  Publique software que você deseja que fique disponível apenas publicamente para clientes específicos, mas não disponível publicamente.
2.  Publique as variações de software público a um preço personalizado para clientes específicos.
3.  Publique as variações de software público com uma descrição e termos personalizados (por meio de uma nova oferta).

Se quiser apenas alterar o preço, você poderá reutilizar os discos de outro SKU na mesma oferta. Com os SKUs privados, você não precisa reenviar discos entre SKUs.

<a name="mark-a-sku-private"></a>Marcar um SKU privado
---------------------

Para marcar uma SKU como privado, mude a opção perguntando se o SKU é privado:

![Marcar um SKU como privado](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Você pode reutilizar os discos em outro SKU e modificar a descrição ou o preço. Para reutilizar os discos, selecione **Sim** como resposta ao prompt "Será que este SKU reutiliza imagens de um SKU público".

Se o SKU está marcado como privado e a oferta tem outros SKUs com discos reutilizáveis, é necessário que você indique que o SKU reutiliza discos de outro SKU. Também é necessário que você especifique o público-alvo para o SKU privado.

>[!NOTE]
>Depois que ele é publicado, um SKU público não pode ser transformado em privado.

<a name="select-an-image"></a>Selecionar uma imagem
------------------

Você pode fornecer novos discos para o SKU privado ou reutilizar os mesmos discos já fornecidos em outro SKU, somente modificando os preços ou a descrição. Para reutilizar os discos, selecione **Sim** como resposta ao prompt "Será que esta imagem de reutilização do SKU de um SKU público" solicita.

![Indicar reutilização de imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Depois de confirmar se o SKU reutiliza imagens, selecione a origem ou o SKU *base* para as imagens:

![Selecionar uma imagem](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Quando você publica a oferta, as imagens do SKU selecionado seriam disponibilizados sob a ID do SKU privado com as taxas/termos personalizados. O SKU privado só estaria visível para o público-alvo.

Para atualizações de imagem, seria necessário apenas que você atualizasse a imagem do SKU subjacente. Nos bastidores, a imagem para o SKU privado será também atualizada automaticamente. Da mesma forma, se você excluir a imagem do SKU subjacente, a imagem também será removida do SKU privado.

<a name="restricting-the-audience"></a>Restringir o público-alvo
------------------------

Ofertas privadas podem ser localizadas e implantadas somente pelos usuários de destino.
Atualmente, damos suporte ao direcionamento a usuários usando IDs de assinatura.

Essas assinaturas podem ser inseridas por meio de um formulário de entrada manual **para até 10 assinaturas**, ou pelo carregamento de um arquivo CSV, que permite **até 20.000 assinaturas**.

Entrada manual para público restrito:

![Restringir o público manualmente](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Upload de CSV para público restrito:

![Usar CSV para restringir o público](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Conteúdo do arquivo CSV de exemplo:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Quando você alternar da exibição de entrada manual para a de upload de CSV ou vice-versa, a lista antiga de IDs de assinatura com acesso ao SKU não é mantida. Um aviso é exibido e a lista é substituída apenas ao salvar a oferta.

<a name="managing-private-audiences"></a>Gestão de audiências privadas
-------------------------

**A fim de atualizar o público sem republicar toda a oferta, você faz as alterações de audiência desejadas (usando a UI ou a API) e, em seguida, iniciar a ação "Sincronizar audiências privadas".**

Se o seu público for de 10 ou menos assinaturas, você pode gerenciá-lo inteiramente usando a UI CPP.

Se o seu público for mais de 10 assinaturas, você pode gerenciá-lo usando um arquivo CSV que você pode carregar para a UI CPP ou usando a API.

Se você estiver usando a API e não quiser manter um arquivo CSV, você pode gerenciar o público diretamente usando a API de acordo com as instruções abaixo.

> [!NOTE]
> Use o ID de assinatura do Azure (Planos e SKUs) ou ID de inquilino (somente planos) para adicionar uma audiência à sua oferta privada.

###  <a name="managing-subscriptions-with-the-api"></a>Gerenciamento de assinaturas com a API

Você pode usar a API para carregar um CSV ou gerenciar seu público diretamente (sem usar um CSV). Em geral, você simplesmente precisa recuperar `restrictedAudience` sua oferta, atualizar o objeto e, em seguida, enviar essas alterações de volta à sua oferta, a fim de adicionar ou remover membros da audiência.

Veja como atualizar programáticamente sua lista de audiência:

1. [Recupere os](cloud-partner-portal-api-retrieve-specific-offer.md) dados da sua oferta:

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Encontre objetos de audiência restritos em cada SKU da oferta usando esta consulta JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Atualize os objetos de audiência restritos para sua oferta.

    **Se você fez o upload original da lista de assinaturas para sua oferta privada a partir do arquivo CSV:**

    Seus *objetos de audiência restrita* serão assim.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Para cada objeto de audiência restrita:

    a. Baixe o `restrictedAudience.uploadedCsvUri`conteúdo de . O conteúdo é simplesmente um arquivo CSV com cabeçalhos. Por exemplo: 

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. Adicione ou exclua assinaturas no arquivo CSV baixado conforme necessário.

    c. Carregue o arquivo CSV atualizado para um local, como [o armazenamento Azure Blob](../../storage/blobs/storage-blobs-overview.md) ou [oneDrive,](https://onedrive.live.com)e crie um link somente de leitura para o seu arquivo. Este será o seu novo *SasUrl.*

    d. Atualize `restrictedAudience.uploadedCsvUri` a chave com o seu novo *SasUrl*.

    **Se você inseriu manualmente a lista original de assinaturas para sua oferta privada no Portal de Parceiros na Nuvem:**

    Seu objeto *(s) restritode Audience* será algo parecido com isso:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Para cada objeto de audiência restrita, `restrictedAudience.manualEntries` adicione ou exclua entradas na lista conforme necessário.

4. Quando terminar de atualizar todos os objetos *restritosDe audiência* para cada SKU de sua oferta privada, [atualize a oferta](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Com isso, sua lista de audiência atualizada está agora em vigor.

<a name="previewing-private-offers"></a>Visualização de ofertas privadas
-------------------------

Durante a etapa de preparo/versão prévia, somente as assinaturas de versão prévia em nível de oferta serão capazes de acessar o SKU. Nesta fase de teste, você pode visualizar a oferta como ela apareceria para seus clientes-alvo.

Ofereça assinaturas de versão prévia no nível de oferta para acessar ofertas em preparo:

![Visualizar IDs de assinatura](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Depois que a oferta estiver ativa, somente as assinaturas de público restrito (inseridas por meio de entrada manual ou de CSV) poderão exibir e implantar o SKU privado. É recomendável que você **sempre inclua suas próprias assinaturas no público restrito** para o SKU privado para fins de validação.

>[!NOTE]
>Para fins de depuração, o suporte da Microsoft e as equipes de engenharia também terão acesso a essas ofertas privadas.
