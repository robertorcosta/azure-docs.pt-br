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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885813"
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
O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais. Você pode configurar um pipeline de entrega contínua para uma VM do Azure diretamente do portal do Azure. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para fazer implantações em vários computadores usando o portal do Azure. 


**Configurar CI/CD em máquinas virtuais**

Máquinas virtuais podem ser adicionadas como destinos em um [grupo de implantação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para atualizações em vários computadores. Com base em seus requisitos, você pode escolher qualquer uma das estratégias de implantação integradas: _Sem Interrupção_, _Canário_, _Azul-Verde_ ou pode personalizá-las posteriormente. Após implantadas, as exibições do Histórico de Implantação dentro dos Grupos de Implantação fornecem rastreabilidade da VM ao pipeline e, em seguida, até a confirmação. 
 
**Implantações Sem Interrupção**: uma implantação sem interrupção substitui as instâncias da versão anterior de um aplicativo por instâncias da nova versão do aplicativo em um conjunto fixo de computadores (conjunto sem interrupção) em cada iteração. Vejamos como você pode configurar uma atualização sem interrupção para máquinas virtuais.  
Você pode configurar atualizações sem interrupção em suas "**máquinas virtuais**" dentro do portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual. 
2. No painel da VM à esquerda, navegue até o menu de  **entrega contínua** . Em seguida, clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em "Organização do Azure DevOps" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto no qual deseja configurar o pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de computadores de destino da implantação que representam os ambientes físicos; por exemplo, "Desenvolvimento", "Teste", "UAT" e "Produção". Você pode criar um grupo de implantação ou selecionar um existente. 
5. Selecione o pipeline de build que publica o pacote a ser implantado na máquina virtual. Observe que o pacote publicado deve ter um script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta _deployscripts_ na raiz do pacote. Esse script de implantação será executado pelo pipeline do Azure DevOps em tempo de execução.
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

15. A tarefa Executar Script de Implantação vai, por padrão, executar o script de implantação _deploy.ps1_ ou _deploy.sh_ na pasta _deployscripts_ no diretório raiz do pacote publicado.
  
**Implantações Canário**: Uma implantação canário reduz o risco, distribuindo lentamente a alteração para um pequeno subconjunto de usuários. Conforme tiver mais confiança na nova versão, você pode começar a liberá-la para mais servidores na infraestrutura e a encaminhar mais usuários para ela. Você pode configurar implantações canário em suas "**máquinas virtuais**" com o portal do Azure usando a opção de entrega contínua. Veja um passo a passo do processo. 
1. Entre no portal do Azure e navegue até uma máquina virtual 
2. Siga as etapas 2 a 7 na seção **Implantações Sem Interrupção** para adicionar várias VMs ao grupo de implantação. Na lista suspensa de estratégia de implantação, selecione "Canário".
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Adicione uma tag "canário" às VMs que devem fazer parte de implantações canário e uma tag "prod" às VMs que fizerem parte das implantações após a implantação canário bem-sucedida.
4. Depois de concluído, você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Como na seção **Implantações Sem Interrupção**, você pode clicar em **Editar** pipeline de lançamento no Azure DevOps para ver a configuração do pipeline. O pipeline consiste em três fases: a primeira fase é uma fase DG e é implantada em VMs que são marcadas como _canário_. A segunda fase pausa o pipeline e aguarda a intervenção manual para retomar a execução. Depois que um usuário estiver satisfeito com a estabilidade da implantação canário, ele poderá retomar a execução do pipeline, que executará a terceira fase que é implantada em VMs marcadas como _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Implantações Azul-Verde**: Uma implantação Azul-Verde reduz o tempo de inatividade por ter um ambiente de espera idêntico. Um dos ambientes sempre está em atividade. Ao se preparar para uma nova versão, você realiza o estágio final do teste no ambiente verde. Quando o software estiver funcionando no ambiente verde, mude o tráfego para que todas as solicitações de entrada vão para o ambiente verde – o ambiente azul agora está ocioso.
Você pode configurar implantações do tipo Azul e Verde em suas "**máquinas virtuais**" no portal do Azure usando a opção de entrega contínua. 

Veja um passo a passo do processo.

1. Entre no portal do Azure e navegue até uma Máquina Virtual 
2. Siga as etapas 2 a 7 na seção **Implantações Sem Interrupção** para adicionar várias VMs ao grupo de implantação. Adicione uma tag "azul" ou "verde" às VMs que devem fazer parte das implantações do tipo Azul-Verde. Se a VM for para uma função em espera, você deverá marcá-la como "verde".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Depois de concluído, você terá um pipeline de entrega contínua configurado para implantação na máquina virtual.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Como nas **Implantações Sem Interrupção**, você pode clicar em **Editar** pipeline de lançamento no Azure DevOps para ver a configuração do pipeline. O pipeline consiste em três fases: a primeira fase é uma fase DG e é implantada em VMs que são marcadas como _verde_ (VMs em espera). A segunda fase pausa o pipeline e aguarda a intervenção manual para retomar a execução. Depois que um usuário estiver satisfeito com a estabilidade da implantação, ele poderá redirecionar o tráfego para VMs _verdes_ e retomar a execução do pipeline que, em seguida, alternará as tags _azul_ e _verde_ nas VMs. Isso garante que as VMs com versão mais antiga do aplicativo sejam marcadas como _verdes_ e implantadas na próxima execução do pipeline.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Observe que essa estratégia de implantação requer que haja pelo menos uma VM marcada como azul e verde. Antes de continuar a execução do pipeline na etapa de Intervenção Manual, você deve ter pelo menos uma VM marcada como _azul_.





 
## <a name="azure-devops-project"></a>Projeto do Azure DevOps 
Comece a usar o Azure com mais facilidade do que nunca.
 
Com o DevOps Projects, comece a executar seu aplicativo em qualquer serviço do Azure em apenas três etapas: selecione um idioma do aplicativo, um runtime e um serviço do Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implantar em Máquinas Virtuais do Azure usando um projeto do DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementar a implantação contínua do aplicativo em um Conjunto de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
