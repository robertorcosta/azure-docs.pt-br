---
title: Tutorial – Configurar implantações canário para Máquinas Virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar o pipeline de CD (implantação contínua) que atualiza um grupo de máquinas virtuais do Azure usando a estratégia de implantação azul-verde
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120544"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar a estratégia de implantação Azul-Verde para Máquinas Virtuais do Linux do Azure


## <a name="iaas---configure-cicd"></a>IaaS – Configurar CI/CD 
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD que usa a estratégia azul-verde para fazer implantações em vários computadores. Você também pode dar uma olhada em outras estratégias, como [distribuída](https://aka.ms/AA7jlh8) e [canário](https://aka.ms/AA7jdrz), que são inatamente compatíveis por meio do portal do Azure. 

 
 **Configurar CI/CD em máquinas virtuais**

Máquinas virtuais podem ser adicionadas como destinos em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações em vários computadores. Após implantadas, a exibição do **Histórico de Implantação** dentro de um grupo de implantação fornece rastreabilidade da VM ao pipeline e, em seguida, até a confirmação. 
 
  
**Implantações Azul-Verde**: Uma implantação Azul-Verde reduz o tempo de inatividade por ter um ambiente de espera idêntico. Um dos ambientes sempre está em atividade. Ao se preparar para uma nova versão, você conclui o estágio final do teste no ambiente verde. Quando o software estiver funcionando no ambiente verde, mude o tráfego para que todas as solicitações de entrada vão para o ambiente verde – o ambiente azul agora está ocioso.
Você pode configurar implantações do tipo Azul e Verde em suas "**máquinas virtuais**" no portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo.

1. Entre no portal do Azure e navegue até uma máquina virtual 
2. No painel da VM à esquerda, acesse **Entrega contínua**. Clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em **Organização do Azure DevOps** para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino da implantação que representam os ambientes físicos; por exemplo, "Desenvolvimento", "Teste", "UAT" e "Produção". Você pode criar um grupo de implantação ou selecionar um existente. 
5. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. Observe que o pacote publicado deve ter um script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta `deployscripts` na raiz do pacote. Esse script de implantação será executado pelo pipeline do Azure DevOps em tempo de execução.
6. Selecione a estratégia de implantação de sua escolha. Selecione **Azul-Verde**.
7. Adicione uma tag "azul" ou "verde" às VMs que devem fazer parte das implantações do tipo Azul-Verde. Se a VM for para uma função em espera, você deverá marcá-la como "verde"; caso contrário, marque-a como "azul".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Clique em **OK** para configurar o pipeline de entrega contínua. Agora você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Clique em **Editar** pipeline de lançamento no Azure DevOps para ver a configuração do pipeline. O pipeline consiste em três fases. A primeira fase é uma fase de grupo de implantação e é implantada em VMs que são marcadas como _verde_ (VMs em espera). A segunda fase pausa o pipeline e aguarda a intervenção manual para retomar a execução. Depois que um usuário estiver satisfeito com a estabilidade da implantação, ele poderá redirecionar o tráfego para VMs _verdes_ e retomar a execução do pipeline que, em seguida, alternará as tags _azul_ e _verde_ nas VMs. Isso garante que as VMs com versão mais antiga do aplicativo sejam marcadas como _verdes_ e implantadas na próxima execução do pipeline.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. A tarefa Executar Script de Implantação executará, por padrão, o script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta `deployscripts` no diretório raiz do pacote publicado. Verifique se o pipeline de build selecionado publica isso na pasta raiz do pacote.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Outras estratégias de implantação
- [Configurar a estratégia de implantação distribuída](https://aka.ms/AA7jlh8)
- [Configurar a estratégia de implantação canário](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Projeto do Azure DevOps 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com o DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma do aplicativo, um runtime e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em Máquinas Virtuais do Azure usando um projeto do DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua do aplicativo em um Conjunto de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
