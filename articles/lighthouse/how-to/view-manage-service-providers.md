---
title: Exibir e gerenciar provedores de serviços
description: Os clientes podem usar a página Provedores de serviços no portal do Azure para exibir informações sobre provedores de serviços, ofertas de provedor de serviço e recursos delegados.
ms.date: 11/15/2019
ms.topic: conceptual
ms.openlocfilehash: 453f5d3ba4ed5c49b5e71c7530298bc0a03c73ee
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463803"
---
# <a name="view-and-manage-service-providers"></a>Exibir e gerenciar provedores de serviços

Os clientes podem usar a página **Provedores de serviços** no [portal do Azure](https://portal.azure.com) para exibir informações sobre provedores de serviços e ofertas de provedores de serviços, recursos específicos delegados por meio do [gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md) e comprar outras ofertas de provedores de serviços. Embora nos refiramos aqui aos provedores de serviços e clientes, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **Provedores de serviços** no portal do Azure, o cliente pode selecionar **Todos os serviços**, pesquisar **Provedores de serviços** e selecioná-lo. Ele também pode pesquisar isso inserindo “Provedores de serviço” na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a página **Provedores de serviço** mostra apenas informações sobre os provedores de serviços que têm acesso às assinaturas ou aos grupos de recursos do cliente por meio do gerenciamento de recursos delegados do Azure. Se um cliente trabalha com outros provedores de serviços que não usam o gerenciamento de recursos delegados do Azure para acessar os recursos do cliente, as informações sobre esses provedores de serviços não são mostradas aqui.

> [!NOTE]
> Os provedores de serviço podem exibir informações sobre seus clientes navegando até **Meus clientes** no portal do Azure. Para saber mais, confira [Exibir e gerenciar os clientes e recursos delegados](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Exibir detalhes do provedor de serviços

Para exibir informações sobre os provedores de serviços com os quais um cliente está trabalhando, ele pode **Ofertas de provedores** no lado esquerdo da página **Provedores de serviços**.

Para cada oferta de provedor de serviços, o cliente verá o nome do provedor de serviços e a oferta associada a ele, juntamente com o nome que o cliente inseriu durante o processo de integração.

Na coluna **Delegações**, o cliente vê quantas assinaturas e/ou grupos de recursos foram delegados ao provedor de serviços para essa oferta. O provedor de serviços poderá acessar e gerenciar essas assinaturas e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="delegate-resources"></a>Recursos delegados

Antes que um provedor de serviços possa acessar e gerenciar os recursos de um cliente, ele deve ser delegado. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, ele verá uma observação na parte superior da seção **Ofertas de provedores**. Isso permite que o cliente saiba que ele precisa executar uma ação antes que o provedor de serviços possa acessar os recursos de qualquer cliente.

Para delegar assinaturas ou grupos de recursos:

1. Marque a caixa para a linha que contém o provedor de serviços, a oferta e o nome. Em seguida, selecione **Recursos delegados** na parte superior da tela.
1. Na seção **Detalhes da oferta** da página **Recursos delegados**, examine os detalhes sobre o provedor de serviços e a oferta. Para examinar as atribuições de função da oferta, selecione **Clique aqui para ver os detalhes da oferta selecionada**.
1. Na seção **Delegar**, selecione **Delegar assinaturas** ou **Delegar grupos de recursos**.
1. Escolha as assinaturas e/ou grupos de recursos que você gostaria de delegar para essa oferta e selecione **Adicionar**.
1. Marque a caixa de seleção na parte inferior da página para confirmar que você deseja conceder a esse provedor de serviços acesso aos recursos que você selecionou e, em seguida, selecione **Delegar**.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas do provedor de serviços

Um cliente pode adicionar uma nova oferta de provedores de serviços na página **Ofertas de provedores** selecionando **Adicionar oferta**. O provedor de serviços deve ter publicado uma oferta para esse cliente. O cliente pode selecionar essa oferta na tela **Ofertas privadas** e, em seguida, selecionar **Criar**.

Se o cliente desejar remover uma oferta de provedor de serviços, poderá selecionar o ícone de lixeira na linha dessa oferta. Depois de confirmar a exclusão, esse provedor de serviços não terá mais acesso aos recursos do cliente que antes eram delegados para essa oferta.

## <a name="update-service-provider-offers"></a>Atualizar ofertas do provedor de serviços

Depois que um cliente tiver adicionado uma oferta, um provedor de serviços poderá publicar uma versão atualizada da mesma oferta no Azure Marketplace. Por exemplo, eles podem querer adicionar uma nova definição de função. Se uma nova versão da oferta tiver sido publicada, a página de **ofertas do provedor** mostrará um ícone de "atualização" na linha dessa oferta. O cliente pode selecionar esse ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de revisar as alterações, o cliente pode optar por atualizar para a nova versão. Depois disso, as autorizações e outras configurações especificadas na nova versão serão aplicadas a quaisquer assinaturas e/ou grupos de recursos que tenham sido delegados para essa oferta.

## <a name="view-delegations"></a>Exibir delegações

As delegações representam as atribuições de função que concedem permissões ao provedor de serviços para os recursos que um cliente delegou. Para exibir essas informações, selecione **Delegações** no lado esquerdo da página **Provedores de serviços**.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de delegação ou filtre por clientes, ofertas ou palavras-chave específicos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Lighthouse](../overview.md).
- Saiba como os provedores de serviço podem [exibir e gerenciar clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.