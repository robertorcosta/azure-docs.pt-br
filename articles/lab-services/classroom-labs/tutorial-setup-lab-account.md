---
title: Configurar uma conta de laboratório com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório com o Azure Lab Services, adicionar um criador de laboratório e especificar imagens do Marketplace a serem usadas pelos laboratórios na conta do laboratório.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 62ccda96563a53ebc77337bb2f89cfddd4410a26
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719034"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: Configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, uma conta de laboratório serve como a conta central na qual os laboratórios da sua organização são gerenciados. Em sua conta de laboratório, conceda permissão para outras pessoas criarem laboratórios e definir políticas que se aplicam a todos os laboratórios na conta do laboratório. Neste tutorial, saiba como criar uma conta de laboratório como um administrador de laboratório. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um usuário à função de criador de laboratório
> * Especificar as imagens do Marketplace disponíveis para proprietários de laboratório

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Serviços de Laboratório**, selecione **Adicionar** na barra de ferramentas. 

    ![Selecione Adicionar na página Contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na página **Conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada. 
    5. Selecione uma **galeria de imagens compartilhadas** existente ou crie uma. Você pode salvar a VM do modelo na galeria de imagens compartilhadas para que ela seja reutilizada por outros. Para obter informações detalhadas sobre galerias de imagens compartilhadas, confira [Usar uma galeria de imagens compartilhadas no Azure Lab Services](how-to-use-shared-image-gallery.md). 
    6. Para **Emparelhar rede virtual**, selecione uma VNet (rede virtual) de emparelhamento para a rede de laboratório. Os laboratórios criados nesta conta são conectados à VNet selecionada e têm acesso aos recursos na rede virtual selecionada. 
    7. Especifique um **intervalo de endereços** para VMs do laboratório. O intervalo de endereços deve estar na notação CIDR (Roteamento entre Domínios sem Classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços. Para obter mais informações, confira [Especificar um intervalo de endereços para as VMs do laboratório](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).
    8. Para o campo **Permitir que o criador de laboratório escolha a localização do laboratório**, especifique se deseja que os criadores de laboratório possam selecionar uma localização para o laboratório. Por padrão, a opção está desabilitada. Quando ela estiver desabilitada, os criadores de laboratório não poderão especificar uma localização para o laboratório que estão criando. Os laboratórios são criados na localização geográfica mais próxima da conta de laboratório. Quando ela estiver habilitada, um criador de laboratório poderá selecionar uma localização no momento da criação de um laboratório. 
    9. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**Notificações**), confirme se a implantação foi bem-sucedida e, em seguida, selecione **Ir para o recurso**. 

    Como alternativa, selecione **Atualizar** na página **Contas de laboratório** e, em seguida, selecione a conta de laboratório que você criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário e criar um laboratório de curso, siga estas etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** , selecione **+ Adicionar** na barra de ferramentas e, em seguida, selecione **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para os criadores de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

1. Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). É possível filtrar a lista para ver apenas as imagens habilitadas/desabilitadas, selecionando a opção **Somente habilitadas**/**Somente desabilitadas** na lista suspensa na parte superior. 
    
    ![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
        
    - Cria uma única VM
    - Usa o Azure Resource Manager para provisionar VMs
    - Não requer a compra de um plano de licenciamento extra
2. Para **desabilitar** uma imagem do Marketplace que tenha sido habilitada, execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Desabilitar imagem**. 

        ![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Desabilitar imagens selecionadas**. 

        ![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De modo semelhante, para **habilitar** uma imagem do Marketplace execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Habilitar imagem**. 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Habilitar imagens selecionadas**. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como professor, vá para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

