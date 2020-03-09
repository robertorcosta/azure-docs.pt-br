---
title: Exibir e gerenciar provedores de serviços
description: Os clientes podem usar a página Provedores de serviços no portal do Azure para exibir informações sobre provedores de serviços, ofertas de provedor de serviço e recursos delegados.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394006"
---
# <a name="view-and-manage-service-providers"></a>Exibir e gerenciar provedores de serviços

Os clientes podem usar a página **provedores de serviço** no [portal do Azure](https://portal.azure.com) para exibir informações sobre provedores de serviço e ofertas de provedor de serviço, delegar recursos específicos por meio [do gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md)e comprar novas ofertas de provedor de serviços. Embora nos refiramos aqui aos provedores de serviços e clientes, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **Provedores de serviços** no portal do Azure, o cliente pode selecionar **Todos os serviços**, pesquisar **Provedores de serviços** e selecioná-lo. Ele também pode pesquisar isso inserindo “Provedores de serviço” na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a página **provedores de serviço** mostra apenas informações sobre os provedores de serviço que têm acesso às assinaturas ou aos grupos de recursos do cliente por meio do gerenciamento de recursos delegado do Azure. Se um cliente trabalha com outros provedores de serviços que não usam o gerenciamento de recursos delegados do Azure para acessar os recursos do cliente, as informações sobre esses provedores de serviços não são mostradas aqui.

> [!NOTE]
> Os provedores de serviço podem exibir informações sobre seus clientes navegando até **Meus clientes** no portal do Azure. Para saber mais, confira [Exibir e gerenciar os clientes e recursos delegados](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Exibir detalhes do provedor de serviços

Para exibir informações sobre provedores de serviço, o cliente pode selecionar **ofertas de provedor** no lado esquerdo da página provedores de **serviço** .

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

## <a name="update-service-provider-offers"></a>Atualizar as ofertas do provedor de serviços

Após um cliente adicionar uma oferta, um provedor de serviços poderá publicar uma versão atualizada da mesma oferta no Azure Marketplace. Por exemplo, talvez eles queiram adicionar uma nova definição de função. Se uma nova versão da oferta tiver sido publicada, a página **Ofertas do provedor** mostrará um ícone de "atualização" na linha dessa oferta. O cliente pode selecionar esse ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de revisar as alterações, o cliente pode optar por atualizar para a nova versão. Assim que fizer isso, as autorizações e outras configurações especificadas na nova versão serão aplicadas a quaisquer assinaturas e/ou grupos de recursos que tenham sido delegados a essa oferta.

## <a name="view-delegations"></a>Exibir delegações

As delegações representam as atribuições de função que concedem permissões ao provedor de serviços para os recursos que um cliente delegou. Para exibir essas informações, selecione **Delegações** no lado esquerdo da página **Provedores de serviços**.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de delegação. Você também pode filtrar por clientes, ofertas ou palavras-chave específicas.

> [!NOTE]
> Os clientes não verão essas atribuições de função ou todos os usuários do locatário do provedor de serviços que receberam essas funções, ao [Exibir informações de atribuição de função para o escopo delegado no portal do Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ou via APIs.

## <a name="audit-delegations-in-your-environment"></a>Auditar delegações em seu ambiente

Os clientes talvez queiram obter visibilidade das assinaturas e/ou dos grupos de recursos que foram delegados aos provedores de serviços para o [Gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md). Isso é especialmente útil para os clientes com um grande número de assinaturas, ou que têm muitos usuários que executam tarefas de gerenciamento.

Fornecemos uma [definição de política interna Azure Policy](../../governance/policy/samples/built-in-policies.md#lighthouse) para auditar a delegação de escopos para um locatário de gerenciamento. Você pode atribuir essa política a um grupo de gerenciamento que inclui todas as assinaturas que você deseja auditar. Quando você verifica a conformidade com essa política, quaisquer assinaturas delegadas e/ou grupos de recursos (dentro do grupo de gerenciamento ao qual a política é atribuída) serão mostrados em um estado não compatível. Em seguida, você pode examinar os resultados e confirmar se não há delegações inesperadas.

Para obter mais informações sobre como atribuir uma política e exibir os resultados do estado de conformidade, consulte [início rápido: criar uma atribuição de política](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
 
- Saiba mais sobre o [Azure Lighthouse](../overview.md).
- Saiba como os provedores de serviço podem [exibir e gerenciar clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.