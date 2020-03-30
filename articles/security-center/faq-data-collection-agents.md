---
title: Faq do Azure Security Center - coleta de dados e agentes
description: Perguntas frequentes sobre coleta de dados, agentes e espaços de trabalho para o Azure Security Center, um produto que ajuda você a prevenir, detectar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245467"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>PERGUNTAS FREQUENTES - Perguntas sobre coleta de dados, agentes e espaços de trabalho

O Security Center coleta dados de suas máquinas virtuais (VMs) do Azure, conjuntos de escala de máquinas virtuais, contêineres IaaS e computadores não-Azure (incluindo máquinas no local) para monitorar vulnerabilidades e ameaças de segurança. Os dados são coletados usando o Microsoft Monitoring Agent, que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados em seu workspace para serem analisados.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Sou cobrado pelos registros do Azure Monitor nos espaços de trabalho criados pelo Security Center?

Não. Os espaços de trabalho criados pelo Security Center, enquanto configurados para logs do Monitor do Azure por cobrança de nó, não incorrem em taxas de logs do Azure Monitor. A cobrança da Central de Segurança sempre tem base em sua política de segurança da Central de Segurança e nas soluções instaladas em um workspace:

- **Camada gratuita**: a Central de Segurança instala a solução 'SecurityCenterFree' no workspace padrão. Você não será cobrado pelo nível Livre.

- **Camada Standard**: a Central de Segurança habilita a solução 'Security' no workspace padrão.

Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> O nível de preços de análise de log dos espaços de trabalho criados pelo Security Center não afeta o faturamento do Security Center.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para o provisionamento automático da instalação do Microsoft Monitoring Agent?

As VMs Windows ou Linux IaaS se qualificam se:

- A extensão do Microsoft Monitoring Agent não está instalada na VM atualmente.
- A VM está em estado de execução.
- O Agente de Máquina Virtual do Windows ou Linux [Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) está instalado.
- A máquina virtual não é usada como um dispositivo, como o firewall de aplicativo Web ou o firewall mais recente.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os workspaces padrão criados pela Central de Segurança?

**Não recomendamos a exclusão do workspace padrão.** A Central de Segurança usa os workspaces padrão para armazenar dados de segurança de suas VMs. Se você excluir um workspace, a Central de Segurança não poderá coletar esses dados, e algumas recomendações de segurança e alertas não estarão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs conectadas ao workspace excluído. A Central de Segurança reinstala o agente e cria novos workspaces padrão.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu espaço de trabalho do Log Analytics existente?

Você pode selecionar um espaço de trabalho do Log Analytics existente para armazenar dados coletados pela Central de Segurança. Para usar o espaço de trabalho do Log Analytics existente:

- O workspace deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o workspace.

Para selecionar um espaço de trabalho do Log Analytics existente:

1. Em **Política de segurança – Coleta de dados**, selecione **Usar outro workspace**.

    ![Usar outro workspace][4]

1. No menu suspenso, selecione um workspace para armazenar os dados coletados.

    > [!NOTE]
    > No menu suspenso, são mostrados apenas os workspaces aos quais você tem acesso e que estejam em sua assinatura do Azure.

1. Selecione **Salvar**. Você será perguntado se gostaria de reconfigurar VMs monitoradas.

    - Selecione **Não** se quiser que as novas configurações de workspace sejam **aplicadas somente às novas VMs**. As novas configurações de workspace se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
    - Selecione **Sim** se você quiser que as novas configurações do espaço de trabalho se **apliquem em todas as VMs**. Além disso, todas as VMs conectadas a um workspace criado da Central de Segurança serão reconectadas ao novo workspace de destino.

    > [!NOTE]
    > Se você selecionar **Sim,** não exclua nenhum espaço de trabalho criado pelo Security Center até que todas as VMs tenham sido reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um workspace for excluído muito cedo.

    - Para cancelar a operação, **selecione Cancelar**.

## <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o Microsoft Monitoring Agent já estivesse instalado como uma extensão na VM?<a name="mmaextensioninstalled"></a>

Quando o Agente de Monitoramento é instalado como uma extensão, a configuração de extensão permite reportar apenas um único espaço de trabalho. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. O Security Center armazenará dados de segurança de uma VM em um espaço de trabalho que já está conectado, desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada nele. O Security Center pode atualizar a versão de extensão para a versão mais recente neste processo.

Para obter mais informações, consulte [Provisionamento automático nos casos de instalação de um agente pré-existente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-microsoft-monitoring-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>E se um Microsoft Monitoring Agent estiver diretamente instalado na máquina, mas não como uma extensão (Agente Direto)?<a name="directagentinstalled"></a>

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), o Security Center instalará a extensão microsoft Monitoring Agent e poderá atualizar o agente microsoft monitoring para a versão mais recente.

O agente instalado continuará a reportar aos seus espaços de trabalho já configurados e, além disso, reportará ao espaço de trabalho configurado no Security Center (o multi-homing é suportado em máquinas Windows).

Se o espaço de trabalho configurado for um espaço de trabalho do usuário (não o espaço de trabalho padrão do Security Center), você precisará instalar a solução "Security/"SecurityCenterFree" nele para que o Security Center comece a processar eventos de VMs e computadores reportando para esse espaço de trabalho.

Para máquinas Linux, o agent multi-homing ainda não é suportado - portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.

Para as máquinas existentes em assinaturas a bordo do Security Center antes de 17 de março de 2019, quando um agente existente será detectado, a extensão do Microsoft Monitoring Agent não será instalada e a máquina não será afetada. Para essas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitoramento em suas máquinas" para resolver os problemas de instalação do agente nessas máquinas

Para obter mais informações, consulte a próxima seção [O que acontece se um gerente de operações do Centro de Sistema ou um agente direto OMS já estiver instalado na minha VM?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>E se um agente do System Center Operations Manager já estiver instalado na minha VM?<a name="scomomsinstalled"></a>

O centro de segurança instalará a extensão do Microsoft Monitoring Agent lado a lado com o agente de gerente de operações do Centro de Sistema existente. O agente existente continuará a reportar ao servidor do Gerente de Operações do Centro do Sistema normalmente. Observe que o agente do Gerente de Operações e o Microsoft Monitoring Agent compartilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Nota - Se a versão 2012 do agente Gerente de Operações estiver instalada, não ative o provisionamento automático (os recursos de capacidade de gerenciamento podem ser perdidos quando o servidor do Gerenciador de Operações também estiver na versão 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?

Se você remover a Extensão de Monitoramento da Microsoft, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a Central de Segurança determinará que a VM não possui a extensão e reinstalará a extensão.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer para impedir a instalação automática do agente e a criação do workspace?

Você pode desligar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita as recomendações e os alertas da Central de Segurança. Para desabilitar o provisionamento automático:

1. Se sua assinatura estiver configurada para a camada Standard, abra a política de segurança para essa assinatura e selecione a camada **Gratuita**.

   ![Tipo de preço][1]

1. Em seguida, desligue o provisionamento automático selecionando **Desativar** na política de segurança – página **de coleta de dados.**
   ![Coleta de dados][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo recusar a instalação do agente e a criação do workspace automáticas?

> [!NOTE]
> Reveja as seções [Quais são as implicações da recusa?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se você optar por recusar o provisionamento automático.

Talvez seja ideal recusar o provisionamento automático nos seguintes casos:

- A instalação automática do agente pela Central de Segurança se aplica a toda a assinatura. Você não pode aplicar a instalação automática em um subconjunto de VMs. Se houver VMs essenciais que não podem ser instaladas com o Microsoft Monitoring Agent, você deverá recusar o provisionamento automático.
- A instalação da extensão Microsoft Monitoring Agent (MMA) atualiza a versão do agente. Isso se aplica a um agente direto e a um agente gerente de operações do Centro de Sistema (neste último, o Gerente de Operações e o MMA compartilham bibliotecas comuns de tempo de execução - que serão atualizadas no processo). Se o agente do Gerenciador de Operações instalado for versão 2012 e for atualizado, os recursos de capacidade de gerenciamento podem ser perdidos quando o servidor do Gerenciador de Operações também estiver na versão 2012. Considere optar por desativar o provisionamento automático se o agente do Gerente de Operações instalado for a versão 2012.
- Se você tiver um espaço de trabalho personalizado externo à assinatura (um espaço de trabalho centralizado), então você deve optar por não provisionar automaticamente. Você pode instalar a extensão do Microsoft Monitoring Agent manualmente e conectá-la ao workspace sem que a Central de Segurança substitua a conexão.
- Se você quiser evitar a criação de vários workspaces por assinatura e tiver seu próprio workspace personalizado na assinatura, terá duas opções:

   1. Você pode recusar o provisionamento automático. Após a migração, defina as configurações de espaço de trabalho padrão conforme descrito em [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Ou você pode permitir que a migração seja concluída, que o Microsoft Monitoring Agent seja instalado nas VMs e que as VMs sejam conectadas ao workspace criado. Em seguida, selecione o seu próprio workspace personalizado definindo a configuração do workspace padrão com a opção de aceitar para reconfigurar os agentes já instalados. Para saber mais, confira [Como fazer para usar meu espaço de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de se recusar o provisionamento automático?

Quando a migração é concluída, o Security Center não pode coletar dados de segurança da VM e algumas recomendações e alertas de segurança não estão disponíveis. Se você optar por sair, instale o Microsoft Monitoring Agent manualmente. Confira as [etapas recomendadas para a recusa](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são as etapas recomendadas para a recusa do provisionamento automático?

Instale manualmente a extensão do Microsoft Monitoring Agent para que o Security Center possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas. Consulte [Instalação do agente para VM do Windows](../virtual-machines/extensions/oms-windows.md) ou [Instalação do agente para VM do Linux](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Você pode conectar o agente a qualquer workspace personalizado existente ou a um workspace criado pela Central de Segurança. Se um workspace personalizado não tiver as soluções 'Security' ou 'SecurityCenterFree' habilitadas, você precisará aplicar uma solução. Para se candidatar, selecione o espaço de trabalho ou a assinatura personalizados e aplique um nível de preço através da diretiva Security – página **de nível de preços.**

   ![Tipo de preço][1]

A Central de Segurança habilitará a solução correta no workspace com base no tipo de preço selecionado.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como fazer para remover extensões do OMS instaladas pela Central de Segurança?<a name="remove-oms"></a>

Você pode remover manualmente o Microsoft Monitoring Agent. Isso não é recomendado, pois limita as recomendações e os alertas da Central de Segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a Central de Segurança reinstalará o agente após a remoção.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Confira Como fazer para impedir a instalação automática do agente e a criação do workspace? para obter instruções sobre como desabilitar a coleta de dados.

Para remover manualmente o agente:

1.  No portal, abra o **Log Analytics**.

1.  Na página Do Log Analytics, selecione um espaço de trabalho:

1.  Selecione as VMs que você não deseja monitorar e selecione **Desconectar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM Linux já tiver um agente OMS não extensivo, a remoção da extensão removerá o agente também e você terá que reinstalá-lo.


## <a name="how-do-i-disable-data-collection"></a>Como desabilitar a coleta de dados?

O provisionamento automático é desativado por padrão. Você pode desabilitar o provisionamento automático de recursos a qualquer momento, desativando essa configuração na política de segurança. O provisionamento automático é altamente recomendado para obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades do sistema operacional e proteção de ponto final.

Para desabilitar a coleta de dados, [Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Selecionar política**. Selecione a assinatura em que você deseja desabilitar o provisionamento automático. Quando você seleciona uma assinatura **Política de segurança - coleta de dados** é exibido. Em **Provisionamento automático**, selecione **Desabilitado**.


## <a name="how-do-i-enable-data-collection"></a>Como habilitar a coleta de dados?

É possível habilitar a coleta de dados para suas assinaturas do Azure na Política de segurança. Para habilitar a coleta de dados. [Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política de segurança**. Selecione a assinatura em que você deseja habilitar o provisionamento automático. Quando você seleciona uma assinatura **Política de segurança - coleta de dados** é exibido. Em **Provisionamento automático**, selecione **Habilitado**.


## <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados é habilitada?

Quando o provisionamento automático está habilitado, a Central de Segurança provisiona o Microsoft Monitoring Agent em todas as VMs do Azure com suporte, bem como em quaisquer novas VMs que forem criadas. O provisionamento automático é recomendado, mas a instalação manual do agente também está disponível. [Saiba como instalar a extensão do Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente habilita o evento de criação de processo 4688 e o campo *CommandLine* dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da Central de Segurança. Para obter mais informações sobre os detalhes registrados para cada novo processo, consulte [campos de descrição em 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também coleta os eventos 4688 criados na VM e os armazena na pesquisa.

O agente também habilita a coleta de dados para [Controles de Aplicativos Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Essa política fará com que o AppLocker gere eventos, que são então coletados e aproveitados pelo Security Center. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 

Quando a Central de Segurança detecta atividade suspeita na VM, o cliente é notificado por email caso [informações de contato de segurança](security-center-provide-security-contact-details.md) tenham sido fornecidas. Um alerta também fica visível no painel de alertas de segurança da Central de Segurança.


## <a name="will-security-center-work-using-an-oms-gateway"></a>O Security Center funcionará usando um gateway OMS?

Sim. O Azure Security Center aproveita o Azure Monitor para coletar dados de VMs e servidores do Azure, usando o Microsoft Monitoring Agent.
Para coletar os dados, cada VM e servidor devem se conectar à Internet usando HTTPS. A conexão pode ser direta, usando um proxy ou através do [GATEWAY OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Monitoring Agent afeta o desempenho dos meus servidores?

O agente consome uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho, bem como o agente e a extensão, consulte o [guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).


## <a name="where-is-my-data-stored"></a>Onde meus dados são armazenados?

Os dados coletados desse agente são armazenados no espaço de trabalho do Log Analytics existente associado à sua assinatura do Azure ou a novos espaços de trabalho. Para obter mais informações, consulte [Segurança de Dados](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png