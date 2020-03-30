---
title: Habilite o Monitor Azure para VMs usando a política do Azure
description: Este artigo descreve como você habilita o Monitor Azure para VMs para várias máquinas virtuais do Azure ou conjuntos de escala de máquinas virtuais usando a Política do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282929"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Habilite o Monitor Azure para VMs usando a política do Azure

Este artigo explica como habilitar o Monitor Azure para VMs para máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais usando a Política do Azure. Ao final deste processo, você terá configurado com sucesso a ativação dos agentes de Análise e Dependência de Log e máquinas virtuais identificadas que não estão em conformidade.

Para descobrir, gerenciar e habilitar o Monitor Azure para VMs para todas as suas máquinas virtuais do Azure ou conjuntos de escala de máquinas virtuais, você pode usar o Azure Policy ou o Azure PowerShell. A Azure Policy é o método que recomendamos porque você pode gerenciar definições de políticas para governar efetivamente suas assinaturas para garantir uma conformidade consistente e habilitação automática de VMs recém-provisionadas. Estas definições de políticas:

* Implantam o agente do Log Analytics e o Dependency Agent.
* Relatam os resultados da conformidade.
* Remediar para VMs não compatíveis.

Se você estiver interessado em realizar essas tarefas com o Azure PowerShell ou um modelo do Azure Resource Manager, consulte [Ativar o Monitor Azure para VMs usando modelos do Azure PowerShell ou do Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de usar a Policy para embarcar em suas VMs do Azure e conjuntos de escala de máquinas virtuais para o Azure Monitoring for VMs, você deve habilitar a solução VMInsights no espaço de trabalho que você usará para armazenar seus dados de monitoramento. Essa tarefa pode ser concluída a partir da página **'Iniciar no** Monitor do Azure' na guia **Outras opções de onboarding.**  Selecione **Configurar um espaço de trabalho**, o que solicitará que você selecione o espaço de trabalho a ser configurado.

![Configurar o workspace](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Você também pode configurar seu espaço de trabalho escolhendo **Ativar usando a diretiva** e, em seguida, selecionar o botão Configurar barra de ferramentas do espaço de **trabalho.**  Isso instalará a solução VMInsights no espaço de trabalho selecionado, o que permitirá que o espaço de trabalho armazene os dados de monitoramento enviados pelas VMs e conjuntos de escala de máquinas virtuais que você habilita rativar usando a Política. 

![Habilite o uso da política](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Gerenciar visão geral do recurso de cobertura de políticas

O Azure Monitor for VMs Policy Coverage simplifica a descoberta, o gerenciamento e a habilitação em escala da iniciativa **Enable Azure Monitor for VMs,** que inclui as definições de políticas mencionadas anteriormente. Para acessar esse recurso, selecione **Outras opções** de onboarding na guia **Get Started** no Azure Monitor para VMs. Selecione **Gerenciar cobertura de diretiva** para abrir a página **Azure Monitor for VMs Policy Coverage.**

![Monitor azure da aba VMs Get Started](./media/vminsights-enable-at-scale-policy/get-started-page.png)

A partir daqui, você pode verificar e gerenciar a cobertura da iniciativa através de seus grupos de gestão e assinaturas. Você pode entender quantas VMs existem em cada um dos grupos de gerenciamento e assinaturas e seu status de conformidade.

![Azure Monitor para página de política de gerenciamento de VMs](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Essas informações são úteis para ajudá-lo a planejar e executar seu cenário de governança para O Monitor Do Azure para VMs a partir de um local central. Embora a Azure Policy forneça uma visão de conformidade quando uma política ou iniciativa é atribuída a um escopo, com esta nova página você pode descobrir onde a política ou iniciativa não é atribuída e atribuí-la no lugar. Todas as ações como atribuir, exibir e editar redirecionar diretamente para a diretiva do Azure. A página **Azure Monitor for VMs Policy Coverage** é uma experiência expandida e integrada apenas para a iniciativa **Enable Azure Monitor for VMs**.

A partir desta página, você também pode configurar seu espaço de trabalho do Log Analytics para VMs do Azure Monitor, que:

- Instala a solução Mapa de Serviço.
- Habilita os contadores de desempenho do sistema operacional usados pelos gráficos de desempenho, pastas de trabalho e suas consultas e alertas de log personalizados.

![Monitor azure para VMs configura espaço de trabalho](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Essa opção não está relacionada a nenhuma ação política. Ele está disponível para fornecer uma maneira fácil de satisfazer os [pré-requisitos necessários](vminsights-enable-overview.md) para habilitar o Monitor Azure para VMs.  

### <a name="what-information-is-available-on-this-page"></a>Quais informações estão disponíveis nesta página?

A tabela a seguir fornece um detalhamento das informações apresentadas na página de cobertura da política e como interpretá-la.

| Função | Descrição | 
|----------|-------------| 
| **Escopo** | Grupo de gerenciamento e assinaturas aos que você tem ou herdou acesso com capacidade de detalhar através da hierarquia do grupo de gerenciamento.|
| **Role** | Seu papel para o escopo, que pode ser leitor, proprietário ou contribuinte. Em alguns casos, pode parecer em branco para indicar que você pode ter acesso à assinatura, mas não ao grupo de gerenciamento a que pertence. As informações em outras colunas variam dependendo do seu papel. O papel é fundamental para determinar quais dados você pode ver e ações que você pode executar em termos de atribuir políticas ou iniciativas (proprietário), editá-los ou visualizar conformidade. |
| **Total VMs** | Número de VMs esse escopo. Para um grupo de gestão, é uma soma de VMs aninhados as assinaturas ou grupo de gestão infantil. |
| **Cobertura de atribuição** | Percentual de VMs que são cobertos pela política ou iniciativa. |
| **Status da atribuição** | Informações sobre o status de sua política ou atribuição de iniciativa. |
| **VMs compatíveis** | Número de VMs que estejam em conformidade com a política ou iniciativa. Para a iniciativa **Enable Azure Monitor for VMs**, este é o número de VMs que possuem agente log analytics e agente de dependência. Em alguns casos, pode parecer em branco por causa de nenhuma atribuição, sem VMs, ou não permissões suficientes. As informações são fornecidas em **Compliance State**. |
| **Conformidade** | O número geral de conformidade é a soma de recursos distintos que estão em conformidade com a soma de todos os recursos distintos. |
| **Estado de Conformidade** | Informações sobre o estado de conformidade para sua política ou atribuição de iniciativa.|

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listado ou um subconjunto dele. Por exemplo, você pode ter criado uma atribuição para uma assinatura (escopo de política) e não um grupo de gerenciamento (escopo de cobertura). Neste caso, o valor da Cobertura de **Atribuição** indica as VMs no escopo de política ou iniciativa dividido pelas VMs no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs, grupos de recursos ou uma assinatura do escopo de diretiva. Se o valor estiver em branco, indica que a política ou a iniciativa não existe ou você não tem permissão. As informações são fornecidas em **Status de Atribuição**.

## <a name="enable-by-using-azure-policy"></a>Habilitar usando o Azure Policy

Para habilitar o Azure Monitor para VMs usando o Azure Policy no locatário:

- Atribuir a iniciativa a um escopo: grupo de gerenciamento, assinatura ou grupo de recursos.
- Revisar e remediar os resultados de conformidade.

Para obter mais informações sobre como atribuir o Azure Policy, confira [Visão geral do Azure Policy](../../governance/policy/overview.md#policy-assignment) e examine a [Visão geral dos grupos de gerenciamento](../../governance/management-groups/overview.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para VMs Azure

As definições de diretiva para uma VM Azure estão listadas na tabela a seguir.

|Nome |Descrição |Type |
|-----|------------|-----|
|Habilitar o Azure Monitor para VMs |Habilite o Monitor Azure para as máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. |Iniciativa |
|Implantação do agente de dependência de auditoria – imagem VM (OS) não listada |Reporta as VMs como incompatíveis se a imagem VM (OS) não for definida na lista e o agente não estiver instalado. |Política |
|Implantação do agente audit log analytics – imagem VM (OS) não listada |Reporta as VMs como incompatíveis se a imagem VM (OS) não for definida na lista e o agente não estiver instalado. |Política |
|Implantar agente de dependência para VMs Linux |Implantar agente de dependência para VMs Linux se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar agente de dependência para VMs windows |Implante o agente dependency para VMs do Windows se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar agente de Análise de Log para VMs Linux |Implantar o agente Log Analytics para VMs Linux se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar o agente de análise de log para VMs do Windows |Implantar o agente Log Analytics para VMs do Windows se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Políticas para conjuntos de escala de máquinas virtuais do Azure

As definições de diretiva para um conjunto de escala de máquina virtual do Azure estão listadas na tabela a seguir.

|Nome |Descrição |Type |
|-----|------------|-----|
|Habilite o Monitor Azure para conjuntos de escala de máquinas virtuais |Habilite o Monitor Azure para os conjuntos de escala de máquina virtual no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Nota: Se a política de upgrade do conjunto de escala estiver definida como Manual, aplique a extensão a todas as VMs do conjunto, chamando a atualização sobre elas. Na CLI, isso `az vmss update-instances`é . |Iniciativa |
|Implantação do agente de dependência de auditoria em conjuntos de escala de máquinavirtual – imagem VM (OS) não listada |Informa a escala da máquina virtual definida como incompatível se a imagem VM (OS) não for definida na lista e o agente não estiver instalado. |Política |
|Implantação do agente audit log analytics em conjuntos de escala de máquinavirtual – imagem VM (OS) não listada |Informa a escala da máquina virtual definida como incompatível se a imagem VM (OS) não for definida na lista e o agente não estiver instalado. |Política |
|Implantar agente de dependência para conjuntos de escala de máquinavirtual Linux |Implantar agente de dependência para os conjuntos de escala de máquina virtual Linux se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar agente de dependência para conjuntos de escala de máquinavirtual do Windows |Implantar agente de dependência para conjuntos de escala de máquina virtual do Windows se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar o agente Log Analytics para conjuntos de escala de máquinas virtuais Linux |Implantar o agente Log Analytics para os conjuntos de escala de máquina virtual Linux se a Imagem VM (OS) for definida na lista e o agente não estiver instalado. |Política |
|Implantar o agente Log Analytics para conjuntos de escala de máquinavirtual do Windows |Implantar o agente Log Analytics para os conjuntos de escala da máquina virtual do Windows se a imagem vm (OS) for definida na lista e o agente não estiver instalado. |Política |

A política independente (não incluída com a iniciativa) é descrita aqui:

|Nome |Descrição |Type |
|-----|------------|-----|
|Espaço de trabalho do Audit Log Analytics para VM – Incompatibilidade de relatórios |Denuncie as VMs como incompatíveis se elas não estiverem registrando no espaço de trabalho do Log Analytics especificado na atribuição de diretiva ou iniciativa. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa do Azure Monitor

Para criar a atribuição de diretiva a partir da página **Azure Monitor for VMs Policy Coverage,** siga essas etapas. Para entender como concluir essas etapas, confira  [Criar uma atribuição de política no portal do Azure](../../governance/policy/assign-policy-portal.md).

Quando você atribui a política ou iniciativa, o escopo selecionado na atribuição pode ser o escopo listado aqui ou um subconjunto dele. Por exemplo, você pode ter criado uma atribuição para a assinatura (escopo de política) e não para o grupo de gerenciamento (escopo de cobertura). Nesse caso, o percentual de cobertura indicaria as VMs no escopo de política ou iniciativa dividido pelas VMs no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs, ou grupos de recursos, ou uma assinatura do escopo de diretiva. Se estiver em branco, indica que a política ou a iniciativa não existe ou você não tem permissões. As informações são fornecidas em **Status de Atribuição**.

1. Faça login no [portal Azure](https://portal.azure.com).

2. No portal Azure, selecione **Monitor**. 

3. Escolha **Máquinas Virtuais** na seção **Insights.**
 
4. Selecione a guia **'Iniciar'.** Na página, selecione **Gerenciar cobertura de políticas**.

5. Selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **Escopo** selecionando a elipse (...). No exemplo, um escopo limita a atribuição de políticas a um agrupamento de máquinas virtuais para aplicação.

6. Na página **de atribuição de políticas do Azure,** ela está pré-preenchida com a iniciativa **Enable Azure Monitor for VMs**. 
    A **caixa de nome da Atribuição** é preenchida automaticamente com o nome da iniciativa, mas você pode alterá-la. Você também pode adicionar uma descrição opcional. A **caixa atribuída** é preenchida automaticamente com base em quem está logado. Esse valor é opcional.

7. (Opcional) Para remover um ou mais recursos do escopo, selecione **Exclusões**.

8. Na lista suspensa **Espaço de Trabalho do Log Analytics** da região com suporte, selecione um espaço de trabalho.

   > [!NOTE]
   > Se o workspace estiver fora do escopo da atribuição, conceda as permissões *Colaborador do Log Analytics* à ID de Entidade de Segurança da atribuição de política. Se você não fizer isso, você pode `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` ver uma falha de implantação como conceder acesso, revisar [como configurar manualmente a identidade gerenciada](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A caixa de **seleção identidade gerenciada** é selecionada porque a iniciativa que está sendo atribuída inclui uma diretiva com o efeito *deployIfNotExists.*
    
9. Na lista suspensa **Gerenciar localização de identidade**, selecione a região apropriada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o **Monitor Do Azure para VMs Policy Coverage** atualiza a cobertura de **atribuição,** **o status da atribuição,** **as VMs em conformidade**e o Estado de **Conformidade** para refletir as alterações. 

A matriz a seguir mapeia cada estado de conformidade possível para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Compatível** | Todas as VMs no escopo têm os agentes de Análise de Log e Dependência implantados para eles.|
| **Não está em conformidade** | Nem todas as VMs no escopo têm os agentes de Análise de Log e Dependência implantados para eles e podem exigir remediação.|
| **Não Começou** | Uma nova tarefa foi adicionada. |
| **Bloqueio** | Você não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Vazio** | Nenhuma política é atribuída. | 

<sup>1</sup> Se você não tiver acesso ao grupo de gerenciamento, peça a um proprietário para fornecer acesso. Ou, veja conformidade e gerencie atribuições através dos grupos de gerenciamento de crianças ou assinaturas. 

A tabela a seguir mapeia cada possível status de atribuição para a iniciativa.

| Status da atribuição | Descrição | 
|------------------|-------------|
| **Sucesso** | Todas as VMs no escopo têm os agentes de Análise de Log e Dependência implantados para eles.|
| **Aviso** | A assinatura não está um grupo de gestão.|
| **Não Começou** | Uma nova tarefa foi adicionada. |
| **Bloqueio** | Você não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Vazio** | Não existem VMs ou uma apólice não é atribuída. | 
| **Ação** | Atribuir uma diretiva ou editar uma atribuição. | 

<sup>1</sup> Se você não tiver acesso ao grupo de gerenciamento, peça a um proprietário para fornecer acesso. Ou, veja conformidade e gerencie atribuições através dos grupos de gerenciamento de crianças ou assinaturas.

## <a name="review-and-remediate-the-compliance-results"></a>Revisar e corrigir os resultados de conformidade

O exemplo a seguir é para uma VM Azure, mas também se aplica a conjuntos de escala de máquinas virtuais. Para saber como revisar os resultados de conformidade, consulte [Identificar resultados de não conformidade](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na página **Azure Monitor for VMs Policy Coverage,** selecione um grupo de gerenciamento ou uma assinatura da tabela. Selecione **Exibir conformidade** selecionando a elipse (...).   

![Conformidade com a política para máquinas virtuais do Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Com base nos resultados das políticas incluídas na iniciativa, as VMs são relatadas como incompatíveis nos seguintes cenários:

* O agente de análise de log ou agente de dependência não está implantado.  
    Esse cenário é típico para um escopo com as VMs existentes. Para atenuá-lo, implante os agentes [necessários criando tarefas de remediação](../../governance/policy/how-to/remediate-resources.md) em uma política não conforme.  
    - Implantar agente de dependência para VMs Linux
    - Implantar agente de dependência para VMs windows
    - Implantar agente de Análise de Log para VMs Linux
    - Implantar o agente de análise de log para VMs do Windows

* A imagem VM (OS) não é identificada na definição da diretiva.  
    Os critérios da política de implantação incluem apenas as VMs implantadas com base em imagens de VM conhecidas do Azure. Verifique a documentação para ver se o sistema operacional da VM é compatível. Caso ele não seja, duplique a política de implantação e atualize-a ou modifique-a para fazer com que a imagem fique em conformidade.  
    - Implantação do agente de dependência de auditoria – imagem VM (OS) não listada
    - Implantação do agente audit log analytics – imagem VM (OS) não listada

* As VMs não estão fazendo logon no espaço de trabalho do Log Analytics especificado.  
    É possível que algumas VMs no escopo da iniciativa estejam fazendo logon em um espaço de trabalho do Log Analytics diferente daquele especificado na atribuição de política. Esta política é uma ferramenta para identificar quais VMs estão reportando a um espaço de trabalho não compatível.  
    - Espaço de trabalho do Audit Log Analytics para VM – Incompatibilidade de relatórios

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

A qualquer momento depois de atribuir uma iniciativa a um grupo de gerenciamento ou assinatura, você pode editá-la para modificar as seguintes propriedades:

- Nome da atribuição
- Descrição
- Atribuído por
- Espaço de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está ativado para suas máquinas virtuais, essas informações estão disponíveis para análise com o Monitor Azure para VMs. 

- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md). 

- Para identificar gargalos e utilização geral com o desempenho da VM, consulte Exibir o desempenho da VM do [Azure](vminsights-performance.md). 
