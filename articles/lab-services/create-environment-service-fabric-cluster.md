---
title: Crie um ambiente de cluster de malha de serviço no Azure DevTest Labs
description: Aprenda a criar um ambiente com um cluster de malha de serviço autônomo e inicie e interrompa o cluster usando horários.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170331"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Crie um ambiente com cluster de malha de serviço autônomo no Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster de malha de serviço independente no Azure DevTest Labs. 

## <a name="overview"></a>Visão geral
O DevTest Labs pode criar ambientes de teste independentes definidos pelos modelos de gerenciamento de recursos do Azure. Esses ambientes contêm recursos IaaS, como máquinas virtuais, e recursos PaaS, como o Service Fabric. O DevTest Labs permite gerenciar máquinas virtuais em um ambiente, fornecendo comandos para controlar as máquinas virtuais. Esses comandos lhe dão a capacidade de iniciar ou parar uma máquina virtual em um cronograma. Da mesma forma, o DevTest Labs também pode ajudá-lo a gerenciar clusters de malha de serviço em um ambiente. Você pode iniciar ou interromper um cluster de malha de serviço em um ambiente manualmente ou através de um cronograma.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Os clusters de malha de serviço são criados usando ambientes em DevTest Labs. Cada ambiente é definido por um modelo do Azure Resource Manager em um repositório Git. O [repositório público do Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para DevTest Labs contém o modelo Gerenciador de recursos para criar um cluster de malha de serviço na pasta [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Primeiro, crie um laboratório no Azure DevTest Labs usando instruções no seguinte artigo: [Crie um laboratório](devtest-lab-create-lab.md). Observe que a opção **ambientes públicos** está **on** por padrão. 
2. Confirme se o provedor de malha de serviço está registrado para sua assinatura seguindo estas etapas:
    1. Selecione **Assinaturas** no menu de navegação à esquerda e selecione sua **Assinatura**
    2. Na página **Assinatura,** selecione **Provedores de recursos** na seção **Configurações** no menu esquerdo. 
    3. Se **o Microsoft.ServiecFabric** não estiver registrado, selecione **Registrar**. 
3. Na página **DevTest Lab** para seu laboratório, selecione **+Adicionar** na barra de ferramentas. 
    
    ![Adicionar botão na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na **página Escolher uma página base,** selecione **Service Fabric Lab Cluster** na lista. 

    ![Selecione o Service Fabric Lab Cluster na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na página **Configuração configuração,** faça as seguintes etapas: 
    1. Especifique um **nome** para o **ambiente**de cluster . Este é o nome do grupo de recursos no Azure no qual o cluster Service Fabric será criado. 
    2. Selecione o **sistema operacional (OS)** para as máquinas virtuais de cluster. O valor padrão é: **Windows**.
    3. Especifique um nome para o **administrador** para o cluster. 
    4. Especifique uma **senha** para o administrador. 
    5. Para o **certificado,** digite as informações do certificado como uma seqüência codificada base64. Para criar um certificado, faça as seguintes etapas:
        1. Baixe o arquivo **Create-ClusterCertificate.ps1** no [repositório Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Alternativamente, clone o repositório em sua máquina. 
        2. Inicie o **PowerShell**. 
        3. Execute o **arquivo ps1** usando o comando `.\Create-ClusterCertificate.ps1`. Você vê um arquivo de texto aberto no bloco de notas com as informações necessárias para preencher os campos relacionados ao certificado nesta página. . 
    6. Digite a **senha do certificado**.
    7. Especifique a **impressão digital do** seu certificado.
    8. Selecione **Adicionar** na **página Configurar configurações.** 

        ![Definir as configurações de cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Depois que o cluster é criado, você verá um grupo de recursos com o nome do ambiente fornecido na etapa anterior. Quando você expande, você vê o cluster Service Fabric nele. Se o status do grupo de recursos estiver preso em **Criar,** **selecione Atualizar** na barra de ferramentas. O ambiente **de cluster Service Fabric** cria um cluster de 1 nó de 5 nós no Linux ou Windows.

    No exemplo a seguir, **mysfabricclusterrg** é o nome do grupo de recursos criado especificamente para o cluster Service Fabric. É importante notar que os ambientes de laboratório são auto-contidos dentro do grupo de recursos em que são criados. Isso significa que o modelo que define o ambiente, que só pode acessar recursos dentro do grupo de recursos recém-criado ou [redes virtuais configuradas para serem usadas pelo laboratório](devtest-lab-configure-vnet.md). Esta amostra acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Inicie ou pare o cluster
Você pode iniciar ou interromper o cluster a partir da própria página do DevTest Lab ou da página Service Fabric Cluster fornecida pelo DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Da página do DevTest Lab
Você pode iniciar ou interromper o cluster na página do DevTest Lab para o seu laboratório. 

1. Selecione **três outros (...)** para o cluster 'Malha de serviço', conforme mostrado na imagem a seguir:: 

    ![Inicie e pare comandos para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Você vê dois comandos no menu de contexto para **iniciar** e **parar** o cluster. O comando start inicia todos os nós em um cluster. O comando stop pára todos os nós em um cluster. Uma vez que um cluster é interrompido, o próprio cluster Service Fabric permanece em um estado pronto, mas nenhum nó está disponível até que o comando start seja reemitido no cluster no laboratório.

    Existem algumas considerações a serem anotadas ao usar um cluster Service Fabric em um ambiente de teste. Pode levar algum tempo para que o cluster Service Fabric se reidrate totalmente depois que os nódulos forem reiniciados. Para reter dados do desligamento para a inicialização, os dados devem ser salvos em um disco gerenciado conectado à máquina virtual. Há implicações de desempenho ao usar um disco gerenciado conectado, por isso é recomendado apenas para ambientes de teste. Se o disco usado para armazenamento de dados não for lastreado, os dados serão perdidos quando o comando stop for emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>A partir da página Service Fabric Cluster 
Há outra maneira de iniciar ou parar o cluster. 

1. Selecione o cluster Service Fabric na exibição de árvore na página DevTest Lab. 

    ![Selecione seu cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na página **Service Fabric Cluster** para o cluster, você vê comandos na barra de ferramentas para iniciar ou interromper o cluster. 

    ![Inicie ou pare comandos na página 'Cluster de malha de serviço'](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configure o cronograma de inicialização automática e desligamento automático
Os clusters de malha de serviço também podem ser iniciados ou interrompidos em um cronograma. Essa experiência é semelhante à experiência de máquinas virtuais em um laboratório. Para economizar dinheiro, por padrão, cada cluster criado em um laboratório é automaticamente desligado no momento definido pela [política de desligamento](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)do laboratório . Você pode substituir especificando se o cluster deve ser desligado ou especificando a hora em que o cluster é desligado. 

![Horários existentes para início automático e desligamento automático](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Opte pelo cronograma de início automático
Para optar pelo cronograma de inicialização, faça as seguintes etapas:

1. Selecione **Iniciar automaticamente** no menu esquerdo
2. Selecione **'Ligar'** para **Permitir que este cluster de malha de serviço seja agendado para inicialização automática**. Esta página só é habilitada se o proprietário do laboratório permitir que os usuários iniciem automaticamente suas máquinas virtuais ou clusters de malha de serviço.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Página da auto star](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Configure as configurações de desligamento automático 
Para alterar as configurações para desligamento, faça as seguintes etapas:

1. Selecione **Desligamento automático** no menu esquerdo. 
2. Nesta página, você pode desativar o desligamento automático selecionando **Desativar** para **Habilitado**. 
3. Se você tiver selecionado **Ativado** para **Ativado,** siga estas etapas:
    1. Especifique a **hora** de desligamento.
    2. Especifique o **fuso horário** para a hora. 
    3. Especifique se deseja que o DevTest Labs envie **notificações** antes do desligamento automático. 
    4. Se você selecionou **Sim** para a opção de notificação, especifique o URL do **Webhook** e/ou **endereço de e-mail** para enviar notificações. 
    5. Selecione **Salvar** na barra de ferramentas.

        ![Página de desligamento automático](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para exibir os nós no cluster Service Fabric
A página de cluster Service Fabric que você viu nas etapas anteriores é específica para a página DevTest Labs. Não mostra os nódulos no grupo. Para ver mais informações sobre o cluster, siga estas etapas:

1. Na página **Do devTest Lab** para seu laboratório, selecione o **grupo de recursos** na exibição de árvores na seção **Minhas máquinas virtuais.**

    ![Escolha o grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na página **Grupo de recursos,** você vê todos os recursos do grupo de recursos em uma lista. Selecione o **cluster Service Fabric** na lista. 

    ![Selecione seu cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Você vê a página **Service Fabric Cluster** para o seu cluster. Esta é a página que o Service Fabric fornece. Você vê todas as informações sobre os clusters, como nós, tipos de nós, etc.

    ![Página inicial do Service Fabric Cluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Próximas etapas
Veja os seguintes artigos para obter detalhes sobre ambientes: 

- [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
