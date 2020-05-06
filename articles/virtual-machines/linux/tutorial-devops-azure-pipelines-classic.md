---
title: Tutorial – Configurar implantações sem interrupção para Máquinas Virtuais do Linux do Azure
description: Neste tutorial, você aprenderá a configurar o pipeline de CD (implantação contínua) que atualiza de modo incremental um grupo de Máquinas Virtuais do Linux do Azure usando a estratégia de implantação sem interrupção
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
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113469"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Tutorial – Configurar estratégia de implantação sem interrupção para Máquinas Virtuais do Linux do Azure

O Azure DevOps é um serviço interno do Azure que automatiza cada parte do processo de DevOps com integração contínua e entrega contínua para qualquer recurso do Azure.
Se seu aplicativo usa máquinas virtuais, aplicativos Web, Kubernetes ou qualquer outro recurso, você pode implementar infraestrutura como código, integração contínua, teste contínuo, entrega contínua e monitoramento contínuo com o Azure e o Azure DevOps.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS – Configurar CI/CD 
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para fazer implantações sem interrupção em vários computadores usando o portal do Azure. Você também pode dar uma olhada em outras estratégias, como [canário](https://aka.ms/AA7jdrz) e [azul-verde](https://aka.ms/AA83fwu), que são inatamente compatíveis por meio do portal do Azure. 


**Configurar CI/CD em máquinas virtuais**

Máquinas virtuais podem ser adicionadas como destinos em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações em vários computadores. Após implantado, o **Histórico de Implantação** dentro de um grupo de implantação fornece rastreabilidade da VM ao pipeline e, em seguida, até o commit. 
 

**Implantações Sem Interrupção**: uma implantação sem interrupção substitui as instâncias da versão anterior de um aplicativo por instâncias da nova versão do aplicativo em um conjunto fixo de computadores (conjunto sem interrupção) em cada iteração. Vejamos como você pode configurar uma atualização sem interrupção para máquinas virtuais.  
Você pode configurar atualizações sem interrupção em suas "**máquinas virtuais**" dentro do portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual. 
2. No painel da VM à esquerda, navegue até o menu de  **entrega contínua** . Em seguida, clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em "Organização do Azure DevOps" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino da implantação que representam os ambientes físicos; por exemplo, "Desenvolvimento", "Teste", "UAT" e "Produção". Você pode criar um grupo de implantação ou selecionar um existente. 
5. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. Observe que o pacote publicado deve ter um script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta `deployscripts` na raiz do pacote. Esse script de implantação será executado pelo pipeline do Azure DevOps em tempo de execução.
6. Selecione a estratégia de implantação de sua escolha. Nesse caso, vamos selecionar "Sem Interrupção".
7. Opcionalmente, você pode marcar o computador com a função. Por exemplo, "Web", "DB" etc. Isso ajuda você a direcionar as VMs que têm apenas uma função específica.
8. Clique em **OK** na caixa de diálogo para configurar o pipeline de entrega contínua. 
9. Depois de concluído, você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Você verá que a implantação na máquina virtual está em andamento. Você pode clicar no link para navegar até o pipeline. Clique em **Versão 1** para exibir a implantação. Ou você pode clicar em **Editar** para modificar a definição do pipeline de lançamento. 
11. Se tiver várias VMs a serem configuradas, repita as etapas 2 a 4 para que outras VMs sejam adicionadas ao grupo de implantação. Observe que, se você selecionar um Grupo de Implantação para o qual já exista uma execução de pipeline, a VM será adicionada ao grupo de implantação sem criar pipelines. 
12. Quando terminar, clique na definição do pipeline, navegue até a organização do Azure DevOps e clique em **Editar** pipeline de lançamento. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Clique no link **1 trabalho, 1 tarefa** na fase **desenvolvimento**. Clique na fase **Implantar**.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. No painel de configuração à direita, você pode especificar o número de computadores que deseja implantar em paralelo em cada iteração. Caso queira implantar em vários computadores de uma vez, você poderá especificá-los em termos de percentual, usando o controle deslizante.  

15. A tarefa Executar Script de Implantação executará, por padrão, o script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta "deployscripts" no diretório raiz do pacote publicado.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Outras estratégias de implantação

- [Configurar a estratégia de implantação canário](https://aka.ms/AA7jdrz)
- [Configurar estratégia de implantação azul-verde](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Projeto do Azure DevOps 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com o DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma do aplicativo, um runtime e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em Máquinas Virtuais do Azure usando um projeto do DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua do aplicativo em um Conjunto de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
