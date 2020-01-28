---
title: Tutorial – DevOps Integrado para IaaS e PaaS no Azure
description: Neste tutorial, você aprende a configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo para VMs do Azure usando o Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: eba793a761bd9f96b1a4ec5d4730f08187a758ef
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515253"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutorial: DevOps Integrado para IaaS e PaaS no Azure

Com soluções de ponta a ponta no Azure, as equipes podem implementar práticas de DevOps em cada uma das fases do ciclo de vida do aplicativo: planejar, desenvolver, entregar e operar. 

Veja abaixo alguns dos Serviços do Azure que simplificam as cargas de trabalho de nuvem e podem ser combinados para permitir cenários incrivelmente poderosos.
Essas tecnologias, combinadas com pessoas e processos, permitem às equipes fornecer valor aos clientes continuamente. 

- Azure: https://portal.azure.com – Portal para a criação de cargas de trabalho de nuvem. Gerencie e monitore tudo, desde aplicativos Web simples a aplicativos em nuvem complexos 
- Azure DevOps: https://dev.azure.com – Planeje de maneira mais inteligente, colabore melhor e envie mais rapidamente com um conjunto de serviços de desenvolvimento modernos 
- Azure Machine Learning Studio: https://ml.azure.com – Prepare dados, treine e implante modelos de machine learning 
 

O Azure DevOps é um serviço interno do Azure que automatiza cada parte do processo de DevOps com integração contínua e entrega contínua para qualquer recurso do Azure.
Se seu aplicativo usa máquinas virtuais, aplicativos Web, Kubernetes ou qualquer outro recurso, você pode implementar infraestrutura como código, integração contínua, teste contínuo, entrega contínua e monitoramento contínuo com o Azure e o Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS – Configurar CI/CD 
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para fazer implantações em vários computadores usando o portal do Azure. Configurar CI/CD em máquinas virtuais.

As máquinas virtuais podem ser adicionadas como destinos em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações de sem interrupção em vários computadores. As exibições do histórico de implantação dentro dos Grupos de implantação fornecem capacidade de rastreamento da VM para o pipeline e, em seguida, para o commit. 
 
**Atualizações sem interrupção**: uma implantação sem interrupção substitui as instâncias da versão anterior de um aplicativo por instâncias da nova versão do aplicativo em um conjunto fixo de computadores (conjunto sem interrupção) em cada iteração. Vejamos como você pode configurar uma atualização sem interrupção para máquinas virtuais.  
Você pode configurar atualizações sem interrupção em suas "**máquinas virtuais**" dentro do portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual. 
2. No painel da VM à esquerda, navegue até o menu de  **entrega contínua** . Em seguida, clique em **Configurar**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. No painel de configuração, clique em "Organização do Azure DevOps" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino da implantação que representam os ambientes físicos; por exemplo, "Desenvolvimento", "Teste", "UAT" e "Produção". Você pode criar um grupo de implantação ou selecionar um existente. Opcionalmente, você pode marcar o computador com a função. Por exemplo, "Web", "DB" etc.  
5. Clique em **OK** na caixa de diálogo para configurar o pipeline de entrega contínua. 
6. Depois de concluído, você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Você verá que a implantação na máquina virtual está em andamento. Você pode clicar no link para navegar até o pipeline. Clique em **Versão 1** para exibir a implantação. Ou você pode clicar em **Editar** para modificar a definição do pipeline de lançamento. 
8. Se tiver várias VMs a serem configuradas, repita as etapas 2 a 5 para que outras VMs sejam adicionadas ao grupo de implantação. 
9. Quando terminar, clique na definição do pipeline, navegue até a organização do Azure DevOps e clique em **Editar** pipeline de lançamento. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Clique no link **1 trabalho, 1 tarefa** na fase **desenvolvimento**. Clique na fase **Implantar**.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. No painel de configuração à direita, você pode ver que, por padrão, o pipeline está configurado para fazer uma atualização sem interrupção para todos os destinos em paralelo. Você pode configurar as implantações para que aconteçam uma de cada vez ou em termos de percentual usando o controle deslizante.  
  
  
Usar um **Canário** reduz o risco, distribuindo lentamente a alteração para um pequeno subconjunto de usuários. Conforme tiver mais confiança na nova versão, você pode começar a liberá-la para mais servidores na infraestrutura e a encaminhar mais usuários para ela. Você pode configurar implantações canário em suas "**máquinas virtuais**" com o portal do Azure usando a opção de entrega contínua. Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual 
2. Siga as etapas 2 a 5 na seção anterior para adicionar várias VMs ao grupo de implantação. 
3. Adicione uma marca personalizada às VMs que devem fazer parte das implantações canário. Por exemplo, “canário”.
4. Quando o pipeline estiver configurado para as VMs, clique no pipeline, inicie a organização do Azure DevOps, **Edite** o pipeline e navegue até a fase **desenvolvimento**. Adicione a marca ao filtro "canário". 
5. Adicione outra fase do grupo de implantação, configure a fase com as marcas para direcionar as VMs restantes no grupo de implantação.  
6. Opcionalmente, configure uma etapa de validação manual que possa promover/rejeitar as implantações canário. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

A opção **Azul e Verde** reduz o tempo de inatividade da implantação por ter um ambiente de espera idêntico. Um dos ambientes sempre está em atividade. Ao se preparar para uma nova versão, você realiza o estágio final do teste no ambiente verde. Quando o software estiver funcionando no ambiente verde, mude o tráfego para que todas as solicitações de entrada vão para o ambiente verde – o ambiente azul agora está ocioso.
Você pode configurar implantações do tipo Azul e Verde em suas "**máquinas virtuais**" no portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo. 

1. Entre no portal do Azure e navegue até uma Máquina Virtual 
2. Siga as etapas 2 a 5 na seção **Atualizações sem interrupção** para adicionar várias VMs ao grupo de implantação. Adicione uma marca personalizada às VMs que devem fazer parte das implantações do tipo azul e verde. Por exemplo, "azul" ou "verde" para as VMs que têm função de espera. 
3. Quando o pipeline estiver configurado para as VMs, clique no pipeline, inicie a organização do Azure DevOps, **Edite** o pipeline e navegue até a fase **desenvolvimento**. Adicione a marca ao filtro "verde". 
4. Adicione uma fase sem agente, configure a fase com a etapa de validação manual e uma etapa de invocar a API REST para trocar as marcas. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projeto do Azure DevOps 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com o DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma do aplicativo, um runtime e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em Máquinas Virtuais do Azure usando um projeto do DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua do aplicativo em um Conjunto de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
