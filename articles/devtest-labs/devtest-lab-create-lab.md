---
title: Criar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo orienta você pelo processo de criação de um laboratório usando o portal do Azure e Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 09999c5b0187f924f9cfbbc2afad8954adee0fd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481248"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar Laboratórios de Desenvolvimento/Teste do Azure
Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como Máquinas Virtuais (VMs), que permite gerenciar melhor esses recursos especificando limites e cotas. Este artigo explica o processo de criação de um laboratório usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, você precisa de:

* Uma assinatura do Azure. Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Você deve ser o proprietário da assinatura para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Etapas para criar um laboratório n o Azure DevTest Labs
As etapas a seguir ilustram como usar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. No menu principal à esquerda, selecione **Todos os Serviços** (na parte superior da lista). Selecione * (estrela) próximo a **DevTest Labs** na seção **DEVOPS**. Essa ação adicionará o **DevTest Labs** no menu de navegação esquerdo para que você possa acessá-lo facilmente da próxima vez. 

    ![Todos os serviços – selecionar DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Agora, selecione **DevTest Labs** no menu de navegação esquerdo. Selecione **Adicionar** na barra de ferramentas. 
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na página **Criar um DevTest Lab**, execute as seguintes ações: 
    1. Insira um **nome** para o laboratório.
    2. Selecione a **Assinatura** para associar ao laboratório.
    3. Insira um **nome para o grupo de recursos** do laboratório. 
    4. Selecione um **local** no qual o laboratório será armazenado.
    4. Selecione **Desligamento Automático** para especificar se você deseja ativar e definir os parâmetros de desligamento automático de todas as VMs do laboratório. O recurso de autodesligamento é principalmente um recurso de economia de custos no qual você pode especificar quando deseja que a VM seja desligada automaticamente. Você pode alterar as configurações de autodesligamento após criar o laboratório seguindo as etapas descritas no artigo [Gerenciar todas as políticas de um laboratório no Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Insira as informações de **NOME** e **VALOR** de **Marcas** se você quiser criar uma marcação personalizada que é adicionada a cada recurso que você criará no laboratório. As marcas são úteis para ajudá-lo a gerenciar e organizar recursos de laboratório por categoria. Para saber mais sobre marcas, incluindo como adicionar marcas depois de criar o ambiente de laboratório, veja [Adicionar marcas a um laboratório](devtest-lab-add-tag.md).
    6. Selecione **Opções de automação** para obter modelos do Azure Resource Manager para automação da configuração. 
    7. Selecione **Criar**. Você pode monitorar o status do processo de criação do laboratório vendo a área **Notificações**. 
    
        ![Criar uma seção de laboratório do DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Após a conclusão, selecione **Ir para o recurso** na notificação. Como alternativa, atualize a página **DevTest Labs** para ver o laboratório recém-criado na lista de laboratórios.  Selecione o laboratório na lista. Você verá a home page do seu laboratório. 

        ![Home page do laboratório](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de criar seu laboratório, aqui estão algumas das próximas etapas a serem consideradas:

* [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

