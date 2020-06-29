---
title: Vincular uma conta do Azure à ID de parceiro
description: Acompanhar os contratos com clientes do Azure por meio da vinculação de ID de parceiro à conta de usuário que você usa para gerenciar funcionalidades do cliente.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 05/04/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: 77abfcf300decb3a19da4268d7feb7de1f41f3b5
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743908"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Vincular ID de parceiro a suas contas do Azure

Os parceiros da Microsoft oferecem serviços que ajudam os clientes a alcançar objetivos de negócios e de missão usando produtos da Microsoft. Ao agir em nome do cliente que gerencia, configura e dá suporte aos serviços do Azure, os usuários do parceiro precisarão de acesso ao ambiente do cliente. Usando o PAL (Link de Partner Admin), os parceiros podem associar sua ID de rede de parceiro às credenciais usadas para entrega do serviço.

O PAL permite que a Microsoft identifique e reconheça os parceiros que impulsionam o sucesso do cliente do Azure. A Microsoft pode atribuir a influência e a receita consumida pelo Azure à sua organização com base nas permissões da conta (função RBAC) e no escopo (assinatura, grupo de recursos, recurso).

## <a name="get-access-from-your-customer"></a>Obter o acesso do cliente

Antes de vincular sua ID de parceiro, o cliente deve oferecer acesso a seus recursos do Azure, usando uma das seguintes opções:

- **Usuário convidado**: Seu cliente pode adicioná-lo como um usuário convidado e atribuir funções de controle de acesso baseado em função (RBAC). Para obter mais informações, consulte [Adicionar usuários convidados de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório**: Seu cliente pode criar uma conta de usuário para você no próprio diretório dele e atribuir qualquer função RBAC.

- **Entidade de serviço**: Seu cliente pode adicionar um aplicativo ou script da sua organização no diretório dele e atribuir qualquer função RBAC. A identidade do aplicativo ou script é conhecida como entidade de serviço.

- **Azure Lighthouse**: seu cliente pode delegar uma assinatura (ou um grupo de recursos) para que os usuários possam trabalhar nela no seu locatário. Para obter mais informações, confira [Gerenciamento de recursos delegados do Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).

## <a name="link-to-a-partner-id"></a>Vincular à uma ID de parceiro

Quando você tem acesso aos recursos do cliente, use o portal do Azure, o PowerShell ou a CLI do Azure para vincular sua ID da Microsoft Partner Network (ID da MPN) à sua ID de usuário ou entidade de serviço. Vincule a ID de parceiro em cada locatário do cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Use o portal do Azure para vincular a uma nova ID de parceiro

1. Vá para [Vincular a uma ID de parceiro](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) no portal do Azure.

2. Entre no portal do Azure.

3. Insira a ID de parceiro da Microsoft. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

   ![Captura de tela que mostra o vínculo a uma ID de parceiro](./media/link-partner-id/link-partner-id01.png)

4. Para vincular a ID de parceiro a outro cliente, alterne o diretório. Em **Mudar diretório**, selecione o seu diretório.

   ![Captura de tela que mostra Mudar diretório](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Use o PowerShell para vincular a uma nova ID de parceiro

1. Instale o módulo do PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com o PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Use a CLI do Azure para vincular a uma nova ID de parceiro
1. Instale a extensão de CLI do Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Entre no locatário do cliente com a conta de usuário ou a entidade de serviço. Para obter mais informações, veja [Entrar com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Vincular a nova ID de parceiro. A ID do parceiro é a ID do [Microsoft Partner Network](https://partner.microsoft.com/) da sua organização.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Atualizar a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Excluir a ID de parceiro vinculada
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Próximas etapas

Participe da discussão da [Comunidade de Parceiros da Microsoft](https://aka.ms/PALdiscussion) para receber atualizações ou enviar comentários.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quem pode vincular a ID de parceiro?**

Qualquer usuário da organização do parceiro que gerencia os recursos do cliente pode vincular a ID de parceiro à conta.

**Uma ID de parceiro pode ser alterada depois que estiver vinculada?**

Sim. Uma ID de parceiro vinculada pode ser alterada, adicionada ou removida.

**E se um usuário tiver uma conta em vários locatários do cliente?**

A vinculação entre a ID de parceiro e a conta é feita para cada locatário do cliente. Vincule a ID de parceiro em cada locatário do cliente.

**Outros parceiros ou clientes podem editar ou remover a vinculação à ID de parceiro?**

A vinculação está associada no nível da conta de usuário. Só você pode editar ou remover a vinculação à ID de parceiro. O cliente e outros parceiros não podem alterar a vinculação à ID de parceiro.


**Qual ID MPN deverei usar se minha empresa tiver várias?**

As Contas de Localização de Parceiro e as IDs MPN associadas devem ser usadas para vincular a ID do parceiro.  Saiba mais sobre [Contas de Parceiros](https://docs.microsoft.com/partner-center/account-structure)

**Onde posso encontrar relatórios de receita influenciados para a ID de parceiro vinculada?**

O relatório de Desempenho do Produto em Nuvem está disponível para parceiros no Partner Center no [painel Meus Insights](https://partner.microsoft.com/membership/reports/myinsights). É necessário selecionar o Link de Partner Admin como o tipo de associação de parceiro.

**Por que não consigo ver meu cliente nos relatórios?**

Não é possível ver o cliente nos relatórios pelos seguintes motivos

1. A conta de usuário vinculada não tem [Acesso Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/overview) em nenhuma assinatura ou recurso do Azure do cliente.

2. A assinatura do Azure em que o usuário tem acesso [Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/overview) não tem nenhum uso.

**A ID do parceiro de link funciona com Azure Stack?**

Sim, você pode vincular sua ID de parceiro para o Azure Stack.

**Como fazer para vincular minha ID de Parceiro se a minha empresa usar o [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) para acessar os recursos do cliente?**

Se você integrar clientes ao gerenciamento de recursos delegados do Azure [publicando uma oferta de serviços gerenciados no Azure Marketplace](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers), sua ID do MPN será associada automaticamente. Se você [integrar os clientes implantando modelos do Azure Resource Manager](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer), precisará associar sua ID do MPN (Microsoft Partner Network) a, pelo menos, uma conta de usuário que tenha acesso a cada uma das suas assinaturas integradas. Observe que você precisará fazer isso no seu locatário do provedor de serviços. Para simplificar, recomendamos criar uma conta da entidade de serviço no seu locatário que esteja associada à ID do MPN e permitindo a ela acesso de Leitor em todos os clientes integrados. Neste exemplo, a função de Leitor do RBAC é usada e é uma das funções que não está qualificada para o Crédito de Parceiro Obtido. Para obter mais informações sobre as funções, confira [Funções e permissões para crédito de parceiro obtido](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3QuW2).


**Como fazer para explicar o PAL (Link de Partner Admin) ao meu cliente?**

O PAL (link de Partner Admin) permite que a Microsoft identifique e reconheça os parceiros que estão ajudando os clientes a alcançar os objetivos de negócios e a perceber valor na nuvem. Os clientes precisam primeiro fornecer acesso ao recurso do Azure deles a um parceiro. Quando o acesso é concedido, a ID da MPN (ID da Microsoft Partner Network) do parceiro é associada. Essa associação ajuda a Microsoft a entender o ecossistema dos provedores de serviços de TI e a refinar as ferramentas e os programas necessários para dar um melhor suporte a nossos clientes comuns.

**Quais dados são coletados pelo PAL?**

A associação do PAL às credenciais existentes não fornece novos dados do cliente à Microsoft. Ela simplesmente fornece a telemetria à Microsoft na qual um parceiro está ativamente envolvido no ambiente do Azure de um cliente. A Microsoft pode atribuir a influência e a receita consumida do Azure do ambiente do parceiro à organização desse parceiro com base nas permissões da conta (função RBAC) e no escopo (assinatura, grupo de gerenciamento, grupo de recursos, recurso) fornecidos ao parceiro pelo cliente. 

**Isso afeta a segurança do ambiente do Azure de um cliente?**

A associação PAL somente adiciona a ID da MPN do parceiro à credencial já provisionada e não altera nenhuma permissão (função RBAC) nem fornece dados de serviço do Azure adicionais para o parceiro nem para a Microsoft. 

