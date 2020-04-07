---
title: Criar uma conta de renderização remota do Azure
description: Descreve as etapas para criar uma conta para renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681292"
---
# <a name="create-an-azure-remote-rendering-account"></a>Criar uma conta de renderização remota do Azure

Este capítulo orienta você através das etapas para criar uma conta para o serviço de renderização remota do **Azure.** Uma conta válida é obrigatória para completar qualquer um dos quickstarts ou tutoriais.

## <a name="create-an-account"></a>Criar uma conta

As seguintes etapas são necessárias para criar uma conta para o serviço de renderização remota do Azure:

1. Vá para a [página De pré-visualização de realidade mista](https://aka.ms/MixedRealityPrivatePreview)
1. Clique no botão 'Criar um recurso'
1. No campo de pesquisa ("Pesquisar no mercado"), digite "Renderização remota" e aperte 'enter'.
1. Na lista de resultados, clique no azulejo "Renderização remota"
1. Na tela seguinte, clique no botão "Criar". Um formulário é aberto para criar uma nova conta de renderização remota:
    1. Defina 'Nome do recurso' no nome da conta
    1. Atualize 'Assinatura' se necessário
    1. Defina 'Grupo de recursos' para um grupo de recursos de sua escolha
1. Uma vez que a conta é criada, navegue até ela e:
    1. Na *guia Visão geral,* observe o 'ID da conta'
    1. Na guia *Configurações > Teclas de acesso,* anote a 'chave principal' - esta é a chave da conta secreta da conta

### <a name="retrieve-the-account-information"></a>Recupere as informações da conta

As amostras e tutoriais exigem que você forneça o ID da conta e uma chave. Por exemplo, no arquivo **arrconfig.json** que é usado para os scripts de amostra powershell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Veja a [lista de regiões disponíveis](../reference/regions.md) para preencher a opção da *região.*

Os valores para **`arrAccountId`** e **`arrAccountKey`** podem ser encontrados no portal conforme descrito nas seguintes etapas:

* Vá para [o portal Azure](https://www.portal.azure.com)
* Encontre sua **"Conta de Renderização Remota"** - ela deve estar na lista **"Recursos Recentes".** Você também pode procurá-lo na barra de pesquisa no topo. Nesse caso, certifique-se de que a assinatura desejada ser selecionada no filtro de assinatura Padrão (ícone de filtro ao lado da barra de pesquisa):

![Filtro de assinatura](./media/azure-subscription-filter.png)

Clicar em sua conta traz você para esta tela, que mostra o **ID** da conta imediatamente:

![ID da conta do Azure](./media/azure-account-id.png)

Para a tecla, selecione **Teclas de acesso** no painel à esquerda. A próxima página mostra uma chave primária e secundária:

![Chaves de acesso do Azure](./media/azure-account-primary-key.png)

O valor **`arrAccountKey`** pode ser primário ou secundário.

## <a name="link-storage-accounts"></a>Contas de armazenamento de link

Este parágrafo explica como vincular contas de armazenamento à sua conta de Renderização Remota. Quando uma conta de armazenamento é vinculada, não é necessário gerar um Uri SAS toda vez que você deseja interagir com os dados em sua conta, por exemplo, ao carregar um modelo. Em vez disso, você pode usar os nomes da conta de armazenamento diretamente conforme descrito na [seção de carregamento de um modelo](../concepts/models.md#loading-models).

As etapas deste parágrafo devem ser executadas para cada conta de armazenamento que deve usar este método de acesso alternativo. Se você ainda não criou contas de armazenamento, você pode caminhar pela respectiva etapa do [converter um modelo para renderização quickstart](../quickstarts/convert-model.md#storage-account-creation).

Agora presume-se que você tem uma conta de armazenamento. Navegue até a conta de armazenamento no portal e vá até a guia **Controle de Acesso (IAM)** para obter essa conta de armazenamento:

![Conta de armazenamento IAM](./media/azure-storage-account.png)

 Certifique-se de que você tem permissões de proprietário sobre esta conta de armazenamento para garantir que você possa adicionar atribuições de função. Se você não tiver acesso, a opção Adicionar uma função **de função** será desativada.

 Você precisa adicionar três funções distintas como descrito nos próximos passos. Se você não fornecer todos os três níveis de acesso, haverá problemas de permissão ao tentar acessar a conta de armazenamento.

 Clique no botão **Adicionar** no bloco "Adicionar uma função de função" para adicionar a primeira função:

![Conta de armazenamento IAM](./media/azure-add-role-assignment.png)

* A primeira função a atribuir é **Proprietário,** como mostrado na captura de tela acima. 
* Selecione **Conta de renderização remota** no * Atribuir acesso**à** estada.
* Selecione sua assinatura e conta de renderização remota nas últimas gotas.

Repita a adição de novas funções mais duas vezes para as respectivas seleções a partir do **dropdown de funções:**
* **Contribuinte de conta de armazenamento**
* **Colaborador de dados de blob de armazenamento**

As outras gotas são selecionadas como na primeira etapa.

Se você adicionou as três funções, sua conta de renderização remota Do Azure terá acesso à sua conta de armazenamento usando identidades de serviço gerenciadas pelo sistema.

## <a name="next-steps"></a>Próximas etapas

* [Autenticação](authentication.md)
* [Usando as APIs do Frontend do Azure para autenticação](frontend-apis.md)
* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
