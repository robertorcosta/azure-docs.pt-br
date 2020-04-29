---
title: Proteja seus computadores e aplicativos
description: Este documento aborda as recomendações da Central de Segurança que ajudam a proteger suas máquinas virtuais, seus computadores e os ambientes de aplicativos Web e do Serviço de Aplicativo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80435639"
---
# <a name="protect-your-machines-and-applications"></a>Proteja seus computadores e aplicativos
Quando a central de segurança do Azure identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página **computação e aplicativos** da seção segurança de recursos da central de segurança.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de computação e aplicativos](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Exibir a segurança de seus recursos de computação e aplicativos

[![Painel da Central de Segurança](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Para exibir o status de seus recursos de computação e aplicativos, no painel esquerdo da central de segurança, selecione **computação & aplicativos**. As seguintes guias estão disponíveis:

* **Visão geral**: lista as recomendações para todos os recursos de computação e aplicativos, bem como seu status de segurança atual 

* [**VMs e servidores**](#vms-and-computers): lista as recomendações para suas VMs, computadores e o estado de segurança atual de cada

* [**Conjuntos de dimensionamento de VM**](#vmscale-sets): lista as recomendações para seus conjuntos de dimensionamento, 

* [**Serviços de nuvem**](#cloud-services): lista as recomendações para suas funções Web e de trabalho monitoradas pela central de segurança

* [**Serviços de aplicativos**](#app-services): lista as recomendações para seus ambientes de serviço de aplicativo e o estado de segurança atual de cada um

* [**Contêineres**](#containers): lista as recomendações para seus contêineres e avaliação de segurança de suas configurações

* **Recursos de computação**: lista as recomendações para seus recursos de computação, como clusters de Service Fabric e hubs de eventos

### <a name="whats-in-each-tab"></a>O que há em cada guia?

Cada guia tem várias seções e, em cada seção, você pode fazer uma busca detalhada para ver detalhes adicionais sobre o item mostrado.

Em cada guia, você também verá as recomendações para os recursos relevantes em seu ambiente monitorado. A primeira coluna lista a recomendação, a segunda mostra o número total de recursos afetados e a terceira mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que você poderá executar depois de selecioná-la. Por exemplo, se você selecionar **Atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a gravidade da atualização ausente serão exibidos.

> [!NOTE]
> As recomendações de segurança são as mesmas da página **recomendações** , mas aqui são filtradas para o tipo de recurso específico que você selecionou. Para obter mais informações sobre como resolver recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VMs e servidores
A seção VMs e computadores fornece uma visão geral de todas as recomendações de segurança para suas VMs e computadores. Quatro tipos de computadores estão incluídos:

![Computador não Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM Clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM Clássica do Azure.

![VMs identificadas no workspace](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs identificadas somente pelo workspace que faz parte da assinatura exibida. Isso inclui VMs de outras assinaturas que são relatadas para o espaço de trabalho nessa assinatura e VMs que foram instaladas com o Operations Manager agente direto e não têm nenhuma ID de recurso.

O ícone que aparece em cada recomendação ajuda a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **Tipo de recurso** e por **Gravidade**.

Para detalhar as recomendações de segurança para cada VM, clique na VM.
Veja aqui os detalhes de segurança da VM ou do computador. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada problema.

[![Serviços de nuvem](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
A central de segurança descobre automaticamente se você tem conjuntos de dimensionamento e recomenda que você instale o agente de Log Analytics neles.

Para instalar o agente de Log Analytics: 

1. Selecione a recomendação **Instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Uma lista de conjuntos de dimensionamento não monitorados é exibida.

1. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um workspace preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do espaço de trabalho se ele não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de dimensionamento para instalar automaticamente o agente de Log Analytics:
1. Vá para Azure Policy e clique em **Definições**.

1. Procure a política **implantar log Analytics agente para conjuntos de dimensionamento de máquinas virtuais do Windows** e clique nele.

1. Clique em **Atribuir**.

1. Defina o **Escopo** e o **espaço de trabalho do Log Analytics** e clique em **Atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o agente de Log Analytics, em Azure Policy, vá para **correção** e aplique a política existente aos conjuntos de dimensionamento existentes.





### <a name="cloud-services"></a><a name="cloud-services"></a>Serviços de nuvem
Para serviços de nuvem, uma recomendação será criada quando a versão do sistema operacional estiver desatualizada.

![Serviços de Nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Em um cenário em que você tem uma recomendação, siga as etapas na recomendação para atualizar o sistema operacional. Quando uma atualização estiver disponível, você terá um alerta (vermelho ou laranja, dependendo da gravidade do problema). Para obter uma explicação completa dessa recomendação, clique em **Atualizar versão do sistema operacional** na coluna **Descrição** .






### <a name="app-services"></a><a name="app-services"></a>Serviços de aplicativos
Para exibir as informações do serviço de aplicativo, você deve estar no tipo de preço Standard da central de segurança e habilitar o serviço de aplicativo em sua assinatura. Para obter instruções sobre como habilitar esse recurso, consulte [proteger o serviço de aplicativo com a central de segurança do Azure](security-center-app-services.md).

Em **Serviços de Aplicativos**, você encontra uma lista de seus ambientes do Serviço de Aplicativo e o resumo de integridade com base na avaliação executada pela Central de Segurança.

![Serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services.png)

Há três tipos de serviços de aplicativo mostrados:

![Ambiente de Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de Serviços de Aplicativos

![Aplicativo Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicativo Web

![Aplicativo de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicativo de função

Se você selecionar um aplicativo Web, uma exibição de resumo será aberta com três guias:

   - **Recomendações**: com base em avaliações executadas pela central de segurança que falharam.
   - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
   - **Avaliações não disponíveis**: lista de avaliações que falharam devido a um erro ou a recomendação não é relevante para o Serviço de Aplicativo específico

   Em **Recomendações** há uma lista das recomendações para o aplicativo Web selecionado e a gravidade de cada recomendação.

   ![Recomendações dos Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não examinados.

   - A coluna **avaliações passadas** mostra uma lista de avaliações aprovadas. Gravidade dessas avaliações sempre é verde.

   - Selecione uma avaliação passada na lista para obter uma descrição da avaliação, uma lista de recursos íntegros e não íntegros e uma lista de recursos não examinados. Há uma guia para os recursos não íntegros, mas essa lista está sempre vazia, pois a avaliação foi aprovada.





### <a name="containers"></a><a name="containers"></a>Contêineres

Ao abrir a guia **contêineres** , dependendo do seu ambiente, você poderá ver qualquer um dos três tipos de recursos:

![Host do contêiner](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hosts de contêiner-VMs executando o Docker 

![Serviço do Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clusters do AKS (serviço kubernetes do Azure)

![Registro de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registros de ACR (registro de contêiner do Azure) – mostrados somente quando você está no tipo de preço Standard e quando o pacote do registro de contêiner do Azure está habilitado.

Para obter instruções sobre como usar os recursos de segurança do contêiner, consulte [monitorando a segurança de seus contêineres](monitor-container-security.md).

Os benefícios do pacote do registro de contêiner do Azure são explicados [aqui](azure-container-registry-integration.md)

Os benefícios do pacote de serviços Kubernetess são explicados [aqui](azure-kubernetes-service-integration.md)

[![Guia contêineres](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Para ver as recomendações para um recurso específico na lista, clique nesse recurso.

#### <a name="visibility-into-container-registries"></a>Visibilidade de registros de contêiner

Por exemplo, clicar no registro de ACR ASC-demo da lista mostrada no gráfico acima leva a esta página de detalhes:

[![Recomendações para um registro ACR específico](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilidade de contêineres hospedados em máquinas IaaS Linux

Ao clicar em uma das VMs que executam o Docker, você verá a página de detalhes com informações relacionadas aos contêineres no computador, como a versão do Docker e o número de imagens em execução no host.

![Recomendações para uma VM que executa o Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Recomendações de segurança com base no parâmetro de comparação do CIS para Docker

A Central de Segurança examina as configurações do Docker e fornece visibilidade sobre configurações incorretas, oferecendo uma lista de todas as regras com falha que foram avaliadas. A Central de Segurança fornece diretrizes para ajudá-lo a resolver esses problemas rapidamente e economizar tempo. A Central de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente delas.

![guia contêiner](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

* [Lista de referências completa das recomendações de segurança da central de segurança do Azure](recommendations-reference.md)
* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)