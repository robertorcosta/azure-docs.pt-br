---
title: Proteja suas máquinas e aplicativos
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
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282634"
---
# <a name="protect-your-machines-and-applications"></a>Proteja suas máquinas e aplicativos
Quando o Azure Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que o guiam durante o processo de configuração dos controles necessários para endurecer e proteger seus recursos.

Este artigo explica a página **Computação e Aplicativos** da seção de segurança de recursos do Security Center.

Para obter uma lista completa das recomendações que você pode ver nesta página, consulte [Computação e recomendações de aplicativos](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Veja a segurança dos recursos de sua computação e aplicativos

[![Painel da Central de Segurança](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Para visualizar o status dos recursos de computação e aplicativos, a partir do painel esquerdo no Security Center, **selecione Compute & aplicativos**. As seguintes guias estão disponíveis:

* **Visão geral**: lista as recomendações para todos os recursos de computação e aplicativos, bem como seu status de segurança atual 

* [**VMs e Servidores**](#vms-and-computers): lista as recomendações para suas VMs, computadores e estado de segurança atual de cada um

* [**Conjuntos de escala vm**](#vmscale-sets): lista as recomendações para seus conjuntos de escala, 

* [**Serviços em nuvem**](#cloud-services): lista as recomendações para suas funções web e trabalhadores monitoradas pelo Security Center

* [**Serviços de aplicativos**](#app-services): lista as recomendações para seus ambientes de serviço do App e o estado de segurança atual de cada

* [**Contêineres**](#containers): lista as recomendações para seus contêineres e avaliação de segurança de suas configurações

* **Recursos de computação**: lista as recomendações para seus recursos de computação, como clusters de malha de serviço e hubs de eventos

### <a name="whats-in-each-tab"></a>O que tem em cada guia?

Cada guia tem várias seções e, em cada seção, você pode detalhar para ver detalhes adicionais sobre o item mostrado.

Em cada guia, você também verá recomendações para os recursos relevantes em seu ambiente monitorado. A primeira coluna lista a recomendação, a segunda mostra o número total de recursos afetados, e a terceira mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que você poderá executar depois de selecioná-la. Por exemplo, se você selecionar **Atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a gravidade da atualização ausente serão exibidos.

> [!NOTE]
> As recomendações de segurança são as mesmas da página Recomendações, mas aqui **elas** são filtradas para o tipo de recurso específico que você selecionou. Para obter mais informações sobre como resolver recomendações, consulte [Implementando recomendações de segurança no Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VMs e Servidores
A seção de VMs e computadores oferece uma visão geral de todas as recomendações de segurança para suas VMs e computadores. Quatro tipos de máquinas estão incluídas:

![Computador não Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM Clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM Clássica do Azure.

![VMs identificadas no workspace](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs identificadas somente pelo workspace que faz parte da assinatura exibida. Isso inclui VMs de outras assinaturas que se reportam ao espaço de trabalho nesta assinatura, e VMs que foram instaladas com o agente direto do Operations Manager e não possuem ID de recurso.

O ícone que aparece em cada recomendação ajuda a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **Tipo de recurso** e por **Gravidade**.

Para detalhar as recomendações de segurança para cada VM, clique na VM.
Veja aqui os detalhes de segurança da VM ou do computador. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada questão.

[![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Conjuntos de escala de máquinas virtuais
O Security Center descobre automaticamente se você tem conjuntos de escalas e recomenda que você instale o Microsoft Monitoring Agent neles.

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **Instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Uma lista de conjuntos de dimensionamento não monitorados é exibida.

1. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um workspace preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do workspace, se ainda não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de escala para instalar automaticamente o Microsoft Monitoring Agent:
1. Vá para Azure Policy e clique em **Definições**.

1. Procure a política Implante o **agente Log Analytics para conjuntos de escala sinuosas de máquinavirtual do Windows** e clique nele.

1. Clique em **Atribuir**.

1. Defina o **Escopo** e o **espaço de trabalho do Log Analytics** e clique em **Atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o Microsoft Monitoring Agent, no Azure Policy, vá para **Correção** e aplique a política existente aos conjuntos de dimensionamento existentes.





### <a name="cloud-services"></a><a name="cloud-services"></a>Serviços em nuvem
Para serviços de nuvem, uma recomendação será criada quando a versão do sistema operacional estiver desatualizada.

![Serviços de Nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Em um cenário onde você tem uma recomendação, siga os passos da recomendação para atualizar o sistema operacional. Quando uma atualização estiver disponível, você terá um alerta (vermelho ou laranja - dependendo da gravidade do problema). Para obter uma explicação completa desta recomendação, clique em Atualizar a **versão do SISTEMA OPERACIONAL** na coluna **DESCRIÇÃO.**






### <a name="app-services"></a><a name="app-services"></a>Serviços de aplicativos
Para visualizar as informações do App Service, você deve estar no nível de preços padrão do Security Center e ativar o App Service em sua assinatura. Para obter instruções sobre como ativar esse recurso, consulte [Proteger o Serviço de Aplicativos com o Azure Security Center](security-center-app-services.md).

Em **Serviços de Aplicativos**, você encontra uma lista de seus ambientes do Serviço de Aplicativo e o resumo de integridade com base na avaliação executada pela Central de Segurança.

![Serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services.png)

Existem três tipos de serviços de aplicação mostrados:

![Ambiente de Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de Serviços de Aplicativos

![Aplicativo Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicativo Web

![Aplicativo de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicativo de função

Se você selecionar um aplicativo web, uma exibição de resumo será aberta com três guias:

   - **Recomendações**: com base em avaliações realizadas pelo Security Center que falharam.
   - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
   - **Avaliações não disponíveis**: lista de avaliações que falharam devido a um erro ou a recomendação não é relevante para o Serviço de Aplicativo específico

   Em **Recomendações** há uma lista das recomendações para o aplicativo Web selecionado e a gravidade de cada recomendação.

   ![Recomendações dos Serviços de Aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não examinados.

   - A coluna **De avaliações aprovadas** mostra uma lista de avaliações aprovadas. Gravidade dessas avaliações sempre é verde.

   - Selecione uma avaliação passada na lista para obter uma descrição da avaliação, uma lista de recursos íntegros e não íntegros e uma lista de recursos não examinados. Há uma guia para os recursos não íntegros, mas essa lista está sempre vazia, pois a avaliação foi aprovada.





### <a name="containers"></a><a name="containers"></a>Recipientes

Quando você abre a guia **Contêineres,** dependendo do seu ambiente, você pode ver qualquer um dos três tipos de recursos:

![Host de contêineres](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hosts de contêineres - VMs executando docker 

![Serviço do Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clusters azure Kubernetes Service (AKS)

![Registro de contêiner](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registros de Registro de Contêineres (ACR) do Azure - Mostradosomente somente quando você está no nível de preços padrão e quando você tem o pacote azure Container Registry ativado.

Para obter instruções sobre como usar os recursos de segurança do contêiner, consulte [Monitorando a segurança de seus contêineres](monitor-container-security.md).

Os benefícios do pacote de registro de contêineres do Azure são explicados [aqui](azure-container-registry-integration.md)

Os benefícios do pacote kubernetes serviços são explicados [aqui](azure-kubernetes-service-integration.md)

[![Guia de contêineres](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Para ver as recomendações de um recurso específico na lista, clique nesse recurso.

#### <a name="visibility-into-container-registries"></a>Visibilidade nos registros de contêineres

Por exemplo, clicar no registro asc-demo ACR da lista mostrada no gráfico acima leva a esta página de detalhes:

[![Recomendações para um registro ACR específico](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilidade de contêineres hospedados em máquinas IaaS Linux

Quando você clica em uma das VMs executando docker, você verá a página de detalhes com informações relacionadas aos contêineres na máquina, como a versão do Docker e o número de imagens em execução no host.

![Recomendações para um Docker em execução de VM](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Recomendações de segurança com base no parâmetro de comparação do CIS para Docker

A Central de Segurança examina as configurações do Docker e fornece visibilidade sobre configurações incorretas, oferecendo uma lista de todas as regras com falha que foram avaliadas. A Central de Segurança fornece diretrizes para ajudá-lo a resolver esses problemas rapidamente e economizar tempo. A Central de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente delas.

![guia contêiner](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos do Azure, consulte os seguintes artigos:

* [Lista completa de referência das recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)