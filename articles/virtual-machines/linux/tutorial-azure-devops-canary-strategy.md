---
title: Tutorial – Configurar implantações canário para Máquinas Virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar o pipeline de CD (implantação contínua) que atualiza um grupo de Máquinas Virtuais do Linux do Azure usando a estratégia de implantação canário
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120524"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar a estratégia de implantação canário para Máquinas Virtuais do Linux do Azure


## <a name="iaas---configure-cicd"></a>IaaS – Configurar CI/CD 
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD que usa a estratégia canário para fazer implantações em vários computadores. Você também pode dar uma olhada em outras estratégias, como [distribuída](https://aka.ms/AA7jlh8) e [azul-verde](https://aka.ms/AA83fwu), que são inatamente compatíveis por meio do portal do Azure. 


**Configurar CI/CD em máquinas virtuais**

Máquinas virtuais podem ser adicionadas como destinos em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações em vários computadores. Após implantado, o **Histórico de Implantação** dentro de um grupo de implantação fornece rastreabilidade da VM ao pipeline e, em seguida, até a confirmação. 
 
  
**Implantações Canário**: Uma implantação canário reduz o risco, distribuindo lentamente a alteração para um pequeno subconjunto de usuários. Conforme tiver mais confiança na nova versão, você pode começar a liberá-la para mais servidores na infraestrutura e a encaminhar mais usuários para ela. Você pode configurar implantações canário em suas máquinas virtuais com o portal do Azure usando a opção de entrega contínua. Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual 
2. No painel da VM à esquerda, navegue até o menu de  **Entrega contínua** . Clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em **Organização do Azure DevOps** para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino da implantação que representam os ambientes físicos; por exemplo, "Desenvolvimento", "Teste", "UAT" e "Produção". Você pode criar um grupo de implantação ou selecionar um existente. 
5. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. Observe que o pacote publicado deve ter um script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta `deployscripts` na raiz do pacote. Esse script de implantação será executado pelo pipeline do Azure DevOps em tempo de execução.
6. Selecione a estratégia de implantação de sua escolha. Selecione **Canário**.
7. Adicione uma tag "canário" às VMs que devem fazer parte de implantações canário e uma tag "prod" às VMs que fizerem parte das implantações após a implantação canário bem-sucedida. Isso ajuda você a direcionar às VMs que têm apenas uma função específica.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Clique em **OK** na caixa de diálogo para configurar o pipeline de entrega contínua. Agora você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Clique em **Editar** pipeline de lançamento no Azure DevOps para ver a configuração do pipeline. O pipeline consiste em três fases. A primeira fase é uma fase de grupo de implantação e é implantada em VMs que são marcadas como _canário_. A segunda fase pausa o pipeline e aguarda a intervenção manual para retomar a execução. Depois que um usuário estiver satisfeito com a estabilidade da implantação canário, ele poderá retomar a execução do pipeline, que executará a terceira fase que é implantada em VMs marcadas como _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Antes de retomar a execução do pipeline, verifique se pelo menos uma VM está marcada como _prod_. Na terceira fase do pipeline, o aplicativo será implantado somente para as VMs que têm a marca _prod_.

11. A tarefa Executar Script de Implantação executará, por padrão, o script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta "deployscripts" no diretório raiz do pacote publicado. Verifique se o pipeline de build selecionado publica isso na pasta raiz do pacote. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configurar a estratégia de implantação distribuída](https://aka.ms/AA7jlh8)
- [Configurar estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Projeto do Azure DevOps 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com o DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma do aplicativo, um runtime e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em Máquinas Virtuais do Azure usando um projeto do DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua do aplicativo em um Conjunto de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
