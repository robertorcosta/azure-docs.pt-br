---
title: Configurar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório após sua criação.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 1a1b1e662a2e9adedfc68f1818f868c0a5318652
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428981"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="connect-with-a-peer-virtual-network"></a>Conectar-se a uma rede virtual de mesmo nível
Para conectar uma rede virtual como uma rede de mesmo nível à rede virtual do laboratório, siga estas etapas:

1. Na página **conta do laboratório** , selecione **configuração de laboratórios** no menu à esquerda.

    ![Página de configuração de laboratórios](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Para **rede virtual de mesmo nível**, selecione **habilitado** ou **desabilitado**. O valor padrão é **desabilitado**. Para habilitar a rede virtual de mesmo nível, execute as seguintes etapas: 
    1. Selecione **Habilitado**.
    2. Selecione a **VNet** na lista suspensa. 
3. Selecione **Salvar** na barra de ferramentas. 

Os laboratórios criados nessa conta estão conectados à rede virtual selecionada. Eles podem acessar os recursos na rede virtual selecionada. Para obter mais informações, consulte [conectar a rede do seu laboratório a uma rede virtual de mesmo nível no Azure Lab Services](how-to-connect-peer-virtual-network.md).

Quando você seleciona uma rede virtual para o campo **rede virtual par** , a opção **permitir que o criador do laboratório selecione o local do laboratório** está desabilitada. É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles se conectem aos recursos na rede virtual do par. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Permitir que o criador do laboratório escolha o local do laboratório
Você pode permitir que o criador do laboratório crie laboratórios em um local diferente do local da conta do laboratório seguindo estas etapas: 

1. Na página **conta do laboratório** , selecione **configuração de laboratórios** no menu à esquerda.
2. Para **permitir que o criador do laboratório escolha o local do laboratório**, selecione **habilitado** se desejar que o criador do laboratório possa selecionar um local para o laboratório. Se ele estiver desabilitado, os laboratórios serão criados automaticamente no mesmo local em que a conta de laboratório existe. 
    
    Esse campo é desabilitado quando você seleciona uma rede virtual para o campo **rede virtual par** . É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles acessem os recursos na rede virtual do par. 
1. Selecione **Salvar** na barra de ferramentas. 

    ![Definir configuração de local do laboratório](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especificar um intervalo de endereços para VMs no laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo especificado anteriormente, o intervalo de endereços modificados se aplicará somente às VMs criadas depois que a alteração tiver sido feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual estiver emparelhada com a conta de laboratório, o intervalo de endereços fornecido não poderá se sobrepor ao intervalo de endereços da rede virtual emparelhada.

1. Na página **conta do laboratório** , selecione **configuração de laboratórios** no menu à esquerda.
2. Para o campo **intervalo de endereços** , especifique o intervalo de endereços para as VMs que serão criadas no laboratório. O intervalo de endereços deve estar na notação CIDR (roteamento entre domínios sem classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Configurar intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário e criar um laboratório de curso, siga estas etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 

    ![Controle de Acesso -> botão Adicionar atribuição de função](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página **Adicionar atribuição de função**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar criador de laboratório](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Se você estiver adicionando um usuário não conta Microsoft como um criador de laboratório, consulte a seção [Adicionar um usuário não conta Microsoft como um criador de laboratório](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

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

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Adicionar um usuário não conta Microsoft como um criador de laboratório
Para adicionar um usuário como um criador de laboratório, você usa suas contas de email. Os seguintes tipos de contas de email podem ser usados:

- Uma conta de email fornecida pelo AAD (Office 365 Azure Active Directory da sua universidade). 
- Uma conta de email da Microsoft, como `@outlook.com`, `@hotmail.com`, `@msn.com`ou `@live.com`.
- Uma conta de email que não seja da Microsoft, como uma fornecida pelo Yahoo ou pelo Google. No entanto, esses tipos de contas devem ser vinculados a um conta Microsoft.
- Uma conta do GitHub. Essa conta deve ser vinculada a um conta Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Usando uma conta de email que não seja da Microsoft
Os criadores/instrutores de laboratório podem usar contas de email que não são da Microsoft para se registrar e entrar em um laboratório de sala de aula.  No entanto, a entrada no portal de serviços de laboratório requer que os instrutores criem primeiro um conta Microsoft que esteja vinculado ao seu endereço de email que não seja da Microsoft.

Muitos instrutores podem já ter uma conta Microsoft vinculada a seus endereços de email que não são da Microsoft. Por exemplo, os instrutores já têm um conta Microsoft se usaram seu endereço de email com outros produtos ou serviços da Microsoft, como Office, Skype, OneDrive ou Windows.  

Quando os instrutores entram no portal dos serviços de laboratório, eles são solicitados a fornecer seu endereço de email e senha. Se o instrutor tentar entrar com um não conta Microsoft que não tenha uma conta Microsoft vinculada, o instrutor receberá a seguinte mensagem de erro: 

![Mensagem de erro](../media/how-to-configure-student-usage/cant-find-account.png)

Para se inscrever em um conta Microsoft, os instrutores devem ir para [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Usando uma conta do GitHub
Os instrutores também podem usar uma conta do GitHub existente para se registrar e entrar em um laboratório de sala de aula. Se o instrutor já tiver um conta Microsoft vinculado à sua conta do GitHub, ele poderá entrar e fornecer sua senha, conforme mostrado na seção anterior. Se eles ainda não tiverem vinculado sua conta do GitHub a um conta Microsoft, eles deverão selecionar **as opções de entrada**:

![Link de opções de entrada](../media/how-to-configure-student-usage/signin-options.png)

Na página **Opções de entrada** , selecione **entrar com o GitHub**.

![Entrar com o link do GitHub](../media/how-to-configure-student-usage/signin-github.png)

Por fim, eles são solicitados a criar um conta Microsoft que esteja vinculado à sua conta do GitHub. Ele ocorre automaticamente quando o instrutor seleciona **Avançar**.  O instrutor é imediatamente conectado e conectado ao laboratório da sala de aula.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Desligamento automático de VMs na desconexão
Você pode habilitar ou desabilitar o desligamento automático de VMs do laboratório do Windows (modelo ou aluno) depois que uma conexão de área de trabalho remota for desconectada. Você também pode especificar quanto tempo as VMs devem aguardar até que o usuário se reconecte antes de desligar automaticamente.

![Configuração de desligamento automático na conta do laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Essa configuração se aplica a todos os laboratórios criados na conta do laboratório. Um proprietário de laboratório pode substituir essa configuração no nível de laboratório. A alteração nessa configuração na conta do laboratório afetará apenas os laboratórios criados depois que a alteração for feita.

Para saber mais sobre como um proprietário de laboratório pode definir essa configuração no nível de laboratório, consulte [Este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Próximos passos
Veja os artigos a seguir:

- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
