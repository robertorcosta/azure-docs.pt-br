---
title: Criar uma conta do Azure Remote Rendering
description: Descreve as etapas para criar uma conta para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681292"
---
# <a name="create-an-azure-remote-rendering-account"></a>Criar uma conta do Azure Remote Rendering

Este capítulo orienta você pelas etapas para criar uma conta para o serviço de **renderização remota do Azure** . Uma conta válida é obrigatória para a conclusão de qualquer um dos tutoriais ou guias de início rápido.

## <a name="create-an-account"></a>Criar uma conta

As etapas a seguir são necessárias para criar uma conta para o serviço de renderização remota do Azure:

1. Ir para a [página de visualização da realidade misturada](https://aka.ms/MixedRealityPrivatePreview)
1. Clique no botão ' criar um recurso '
1. No campo de pesquisa ("Pesquisar no Marketplace"), digite "renderização remota" e pressione ' Enter '.
1. Na lista de resultados, clique no bloco "renderização remota"
1. Na próxima tela, clique no botão "criar". Um formulário é aberto para criar uma nova conta de renderização remota:
    1. Defina ' nome do recurso ' como o nome da conta
    1. Atualizar "assinatura" se necessário
    1. Definir ' grupo de recursos ' para um grupo de recursos de sua escolha
1. Depois que a conta for criada, navegue até ela e:
    1. Na guia *visão geral* , observe a ' ID da conta '
    1. Na guia *configurações > chaves de acesso* , observe a ' chave primária '-essa é a chave de conta secreta da conta

### <a name="retrieve-the-account-information"></a>Recuperar as informações da conta

Os exemplos e os tutoriais exigem que você forneça a ID da conta e uma chave. Por exemplo, no arquivo **arrconfig. JSON** que é usado para os scripts de exemplo do PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Consulte a [lista de regiões disponíveis](../reference/regions.md) para preencher a opção de *região* .

Os valores de **`arrAccountId`** e **`arrAccountKey`** podem ser encontrados no portal, conforme descrito nas etapas a seguir:

* Vá para o [Portal do Azure](https://www.portal.azure.com)
* Localize sua **"conta de renderização remota"** -ela deve estar na lista **"recursos recentes"** . Você também pode procurá-lo na barra de pesquisa na parte superior. Nesse caso, verifique se a assinatura que você deseja usar está selecionada no filtro de assinatura padrão (ícone de filtro ao lado da barra de pesquisa):

![Filtro de assinatura](./media/azure-subscription-filter.png)

Clicar em sua conta lhe leva para esta tela, que mostra a **ID da conta** imediatamente:

![ID da conta do Azure](./media/azure-account-id.png)

Para a chave, selecione **chaves de acesso** no painel à esquerda. A próxima página mostra uma chave primária e uma secundária:

![Chaves de acesso do Azure](./media/azure-account-primary-key.png)

O valor de **`arrAccountKey`** pode ser a chave primária ou secundária.

## <a name="link-storage-accounts"></a>Vincular contas de armazenamento

Este parágrafo explica como vincular contas de armazenamento à sua conta de renderização remota. Quando uma conta de armazenamento é vinculada, não é necessário gerar um URI de SAS toda vez que você deseja interagir com os dados em sua conta, por exemplo, ao carregar um modelo. Em vez disso, você pode usar os nomes de conta de armazenamento diretamente, conforme descrito na [seção carregando um modelo](../concepts/models.md#loading-models).

As etapas neste parágrafo devem ser executadas para cada conta de armazenamento que deve usar esse método de acesso alternativo. Se ainda não tiver criado contas de armazenamento, você poderá percorrer a respectiva etapa no guia de [início rápido converter um modelo para renderização](../quickstarts/convert-model.md#storage-account-creation).

Agora, supõe-se que você tenha uma conta de armazenamento. Navegue até a conta de armazenamento no portal e vá para a guia **controle de acesso (iam)** para essa conta de armazenamento:

![IAM da conta de armazenamento](./media/azure-storage-account.png)

 Verifique se você tem permissões de proprietário nessa conta de armazenamento para garantir que você possa adicionar atribuições de função. Se você não tiver acesso, a opção **Adicionar uma atribuição de função** será desabilitada.

 Você precisa adicionar três funções distintas conforme descrito nas próximas etapas. Se você não fornecer todos os três níveis de acesso, haverá problemas de permissão ao tentar acessar a conta de armazenamento.

 Clique no botão **Adicionar** no bloco "adicionar uma atribuição de função" para adicionar a primeira função:

![IAM da conta de armazenamento](./media/azure-add-role-assignment.png)

* A primeira função a ser atribuída é **proprietário** , conforme mostrado na captura de tela acima. 
* Selecione **conta de renderização remota** na lista suspensa ***atribuir acesso ao** menu suspenso.
* Selecione sua assinatura e a conta de renderização remota nos últimos menus suspensos.

Repita a adição de novas funções mais duas vezes para as respectivas seleções na lista suspensa **função** :
* **Colaborador da Conta de Armazenamento**
* **Colaborador de dados de blob de armazenamento**

As outras listas suspensas são selecionadas como na primeira etapa.

Se você tiver adicionado todas as três funções, sua conta de renderização remota do Azure terá acesso à sua conta de armazenamento usando as identidades de serviço gerenciadas atribuídas pelo sistema.

## <a name="next-steps"></a>Próximas etapas

* [Autenticação](authentication.md)
* [Usando as APIs de front-end do Azure para autenticação](frontend-apis.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
