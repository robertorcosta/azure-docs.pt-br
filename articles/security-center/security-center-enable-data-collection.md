---
title: Coleta de dados na Central de Segurança do Azure | Microsoft Docs
description: Este artigo descreve como instalar um agente de Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados coletados.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: memildin
ms.openlocfilehash: 056b9bdd46520790f3ffbd9aca56ad8555e23a3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189813"
---
# <a name="data-collection-in-azure-security-center"></a>Coleta de dados na Central de Segurança do Azure
A central de segurança coleta dados de suas VMs (máquinas virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure (incluindo locais) para monitorar vulnerabilidades de segurança e ameaças. Os dados são coletados usando o agente do Log Analytics, que lê uma variedade de configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace visando a análise. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado. O agente de Log Analytics também copia arquivos de despejo de memória para seu espaço de trabalho.

A coleta de dados é necessária para fornecer visibilidade de atualizações ausentes, configurações de segurança do sistema operacional configuradas incorretamente, status do Endpoint Protection e proteção de integridade e ameaças. 

Este artigo descreve como instalar um agente de Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados coletados. As duas operações são necessárias para habilitar a coleta de dados. 

> [!NOTE]
> - A coleta de dados só é necessária para recursos de computação (VMs, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure). Você pode aproveitar a Central de Segurança do Azure mesmo se não provisionar agentes, no entanto, a segurança será limitada e as funcionalidades listadas acima não terão suporte.  
> - Para ver a lista das plataformas compatíveis, consulte [Plataformas compatíveis com a Central de Segurança do Azure](security-center-os-coverage.md).
> - O armazenamento de dados em Log Analytics, independentemente de você usar um espaço de trabalho novo ou existente, pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Habilitar o provisionamento automático do agente de Log Analytics<a name="auto-provision-mma"></a>

Para coletar os dados dos computadores, você deve ter o agente de Log Analytics instalado. A instalação do agente pode ser feita automaticamente (recomendado) ou você pode instalar o agente manualmente. Por padrão, o provisionamento automático está desativado.

Quando o provisionamento automático está ativado, a central de segurança implanta o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas criadas. O provisionamento automático é recomendado, mas você pode instalar o agente manualmente, se necessário (consulte [instalação manual do agente de log Analytics](#manual-agent)).


Para habilitar o provisionamento automático do agente de Log Analytics:
1. No menu da central de segurança no portal, selecione **preços & configurações**.
2. Selecione a assinatura relevante.

   ![Selecionar uma assinatura][7]

3. Selecione a **coleta de dados**.
4. Em **Provisionamento Automático**, selecione **Ativar** para habilitar o provisionamento automático.
5. Clique em **Salvar**. O agente será implantado em todas as VMs dentro de 15 minutos. 

>[!TIP]
> Se um espaço de trabalho precisar ser provisionado, a instalação do agente poderá levar até 25 minutos.

   ![Habilitar o provisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como provisionar uma instalação já existente, veja [Provisionamento automático em caso de uma instalação de agente pré-existente](#preexisting).
> - Para obter instruções sobre o provisionamento manual, consulte [instalar manualmente a extensão do agente de log Analytics](#manual-agent).
> - Para ver as instruções sobre como desativar o provisionamento automático, consulte [Desativar o provisionamento automático](#offprovisioning).
> - Para obter instruções sobre como integrar a Central de Segurança usando o PowerShell, consulte [Automatizar a integração da Central de Segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração do workspace
Os dados coletados pela Central de Segurança são armazenados nos espaços de trabalho do Log Analytics existentes. Seus dados podem ser coletados de VMs do Azure armazenadas em espaços de trabalho criados pela central de segurança ou em um espaço de trabalho existente que você criou. 

A configuração do workspace é definida por assinatura, e várias assinaturas podem usar o mesmo workspace.

### <a name="using-a-workspace-created-by-security-center"></a>Usar workspaces criados pela Central de Segurança

A Central de Segurança pode criar automaticamente um workspace padrão no qual armazenar os dados. 

Para selecionar um workspace criados pela Central de Segurança:

1. Em **Configuração do workspace padrão**, selecione Usar workspaces criados pela Central de Segurança.
   ![Selecione o tipo de preço][10] 

1. Clique em **Salvar**.<br>
    A Central de Segurança criará um novo grupo de recursos e um workspace padrão nessa geolocalização e conectará o agente a esse workspace. A convenção de nomenclatura para o grupo de recursos e o workspace é:<br>
   ** Área de trabalho: DefaultWorkspace- [subscription-ID] - [geo] <br>Grupo de recursos: DefaultResourceGroup- [geo]**

   Se uma assinatura contém VMs de várias localizações, a Central de Segurança cria vários workspaces. Vários workspaces são criados para manter as regras de privacidade de dados.
1. A Central de segurança habilitará automaticamente uma solução da Central de Segurança no workspace por tipo de preço definido para a assinatura. 

> [!NOTE]
> O tipo de preço do Log Analytics para workspaces criados pela Central de Segurança não afeta a cobrança da Central de Segurança. A cobrança da Central de Segurança sempre se baseia na sua política de segurança da Central de Segurança e nas soluções instaladas em um workspace. Para a Camada gratuita, a Central de Segurança instala a solução *SecurityCenterFree* no workspace padrão. Para a Camada Standard, a Central de Segurança habilita a solução *Security* no workspace padrão.
> O armazenamento de dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre contas existentes do log Analytics, consulte [clientes existentes do log Analytics](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Usar um workspace existente

Se você já tiver um espaço de trabalho Log Analytics existente, talvez queira usar o mesmo espaço de trabalho.

Para usar o espaço de trabalho do Log Analytics existente, você precisará ter permissões de leitura e gravação no espaço de trabalho.

> [!NOTE]
> Soluções habilitadas no workspace existente serão aplicadas às VMs do Azure que estão conectados a ele. Para soluções pagas, isso pode resultar em cobranças adicionais. Por questões de considerações de privacidade de dados, verifique se seu workspace selecionado está na região geográfica correta.
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar um espaço de trabalho do Log Analytics existente:

1. Em **Configuração do workspace padrão**, selecione **Usar outro workspace**.

   ![Selecionar um workspace existente][2]

2. No menu suspenso, selecione um workspace para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, todos os workspaces em todas as suas inscrições estão disponíveis. Consulte a [seleção de workspace de assinatura cruzada](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Você precisa de permissão para acessar o workspace.
   >
   >

3. Clique em **Salvar**.
4. Após selecionar **Salvar**, será perguntado se você deseja reconfigurar as VMs monitoradas que estavam conectadas ao workspace padrão anteriormente.

   - Selecione **não** se você quiser que as novas configurações de espaço de trabalho sejam aplicadas somente a novas VMS. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente; VMs recém-descobertas que não têm o agente de Log Analytics instalado.
   - Selecione **Sim** se quiser que as novas configurações de workspace sejam aplicadas a todas as VMs. Além disso, todas as VMs conectadas a um workspace criado da Central de Segurança serão reconectadas ao novo workspace de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os workspaces criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo workspace de destino. Essa operação falhará se um workspace for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecionar um workspace existente][3]

5. Selecione o tipo de preço para o espaço de trabalho desejado para o qual você pretende definir o agente de Log Analytics. <br>Para usar um workspace existente, defina o tipo de preço do workspace. Isso instalará uma solução da Central de Segurança no workspace, se ainda não existir.

    a.  No menu principal da central de segurança, selecione **preços & configurações**.
     
    b.  Selecione o workspace desejado ao qual você pretende conectar o agente.
        ![Selecione o workspace][7] c. Defina o tipo de preço.
        ![Selecione o tipo de preço][9]
   
   >[!NOTE]
   >Se o workspace já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de workspace entre assinaturas
Ao selecionar um workspace para armazenar os dados, todos os workspaces em todas as assinaturas estarão disponíveis. A seleção do workspace entre assinaturas permite coletar dados de máquinas virtuais em execução em assinaturas diferentes e armazená-los no workspace de sua preferência. Essa seleção será útil se você utilizar um workspace centralizado na sua organização e quiser usá-lo para coleta de dados de segurança. Para obter mais informações sobre como gerenciar workspaces, consulte [Gerenciar o acesso ao workspace](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de coleta de dados
A seleção de uma camada de coleta de dados na Central de Segurança do Azure afetará apenas o armazenamento de eventos de segurança no espaço de trabalho do Log Analytics. O agente de Log Analytics ainda coletará e analisará os eventos de segurança necessários para a proteção contra ameaças da central de segurança do Azure, independentemente da camada de eventos de segurança que você escolher armazenar em seu espaço de trabalho Log Analytics (se houver). A escolha de armazenar eventos de segurança no seu workspace permitirá investigar, pesquisar e auditar esses eventos no workspace. 
> [!NOTE]
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).
> 
> É possível escolher a diretiva de filtragem correta para assinaturas e workspaces de quatro conjuntos de eventos a serem armazenados no workspace: 

- **Nenhum** – Desabilitar armazenamento de eventos de segurança. Essa é a configuração padrão.
- **Mínimo** – um conjunto menor de eventos, para clientes que desejam minimizar o volume de eventos.
- **Comum** – este é um conjunto de eventos que satisfaz a maioria dos clientes, garantindo uma trilha de auditoria completa.
- **Todos os eventos** – Para clientes que querem garantir que todos os eventos sejam armazenados.


> [!NOTE]
> Esses conjuntos de eventos de segurança estão disponíveis apenas na Camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
Esses conjuntos foram projetados para lidar com cenários típicos. Avalie qual atende às suas necessidades antes de implementá-los.
>
>

Para determinar os eventos que farão parte dos conjuntos de eventos **Comum** e **Mínimo**, trabalhamos com clientes e padrões do setor para saber mais sobre a frequência não filtrada de cada evento e seu uso. Neste processo, usamos as seguintes diretrizes:

- **Mínimo** – Certificar-se de que esse conjunto aborde apenas os eventos que possam indicar uma violação com êxito e eventos importantes que tenham um volume muito baixo. Por exemplo, esse conjunto contém logon bem-sucedido e com falha do usuário (IDs de evento 4624, 4625), mas não contém logout, o que é importante para auditoria, mas não é significativo para detecção e tem um volume relativamente alto. A maior parte do volume de dados desse conjunto é dos eventos de logon e do evento de criação de processo (ID de evento 4688).
- **Comum** – fornecer uma trilha de auditoria de usuário completa neste conjunto. Por exemplo, esse conjunto contém logons de usuário e entradas de usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações de grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Eventos que têm um volume muito baixo foram incluídos no conjunto Comum, pois a principal motivação para escolhê-los dentre todos os eventos era reduzir o volume e não filtrar eventos específicos.

Aqui está um detalhamento completo das IDs de eventos de Segurança e do AppLocker para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comum | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se você está usando o GPO (Objeto de Política de Grupo), é recomendável habilitar o Evento de Criação de Processo 4688 das políticas de auditoria, bem como o campo *CommandLine* dentro do evento 4688. Para obter mais informações sobre o Processo de Criação do Evento 4688, consulte as [Perguntas Frequentes](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) da Central de Segurança. Para obter mais informações sobre essas políticas de auditoria, consulte [Recomendações de Política de Auditoria](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para habilitar a coleta de dados para [Controles de Aplicativo Adaptáveis](security-center-adaptive-application.md), a Central de Segurança configura uma política do AppLocker local no modo de Auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela Central de Segurança. É importante observar que essa política não será configurada em computadores nos quais já houver uma política do AppLocker configurada. 
> - Para coletar a Plataforma para Filtros do Windows [ID de evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), será preciso habilitar a [Conexão da Plataforma para Filtros de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

Para escolher a política de filtragem:
1. Na página **coleta de dados** , selecione a política de filtragem em **eventos de segurança**.
2. Clique em **Salvar**.

   ![Escolher a política de filtragem][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisionamento automático em casos de uma instalação de agente pré-existente<a name="preexisting"></a> 

Os casos de uso a seguir especificam como o provisionamento automático funciona quando já existe um agente ou extensão instalados. 

- Log Analytics agente está instalado no computador, mas não como uma extensão (agente direto)<br>
Se o agente de Log Analytics estiver instalado diretamente na VM (não como uma extensão do Azure), a central de segurança instalará a extensão do agente de Log Analytics e poderá atualizar o agente do Log Analytics para a versão mais recente.
O agente instalado continuará a relatar para seus espaços de trabalho já configurados e, além disso, reportará para o espaço de trabalho configurado na central de segurança (há suporte para hospedagem múltipla em computadores Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do usuário (não o espaço de trabalho padrão da central de segurança), você precisará instalar a solução "Security/" securityFree "nela para a central de segurança iniciar o processamento de eventos de VMs e computadores que se reportam a esse espaço de trabalho.<br>
<br>
Para computadores Linux, o agente de hospedagem múltipla ainda não tem suporte-portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.
<br>
Para computadores existentes nas assinaturas integradas à central de segurança antes de 2019-03-17, quando um agente existente for detectado, a extensão do agente de Log Analytics não será instalada e o computador não será afetado. Para esses computadores, consulte a recomendação "resolver problemas de integridade do agente de monitoramento em suas máquinas" para resolver os problemas de instalação do agente nesses computadores.

  
- O agente de System Center Operations Manager está instalado no computador<br>
A central de segurança instalará a extensão do agente de Log Analytics lado a lado no Operations Manager existente. O agente de Operations Manager existente continuará a relatar para o servidor de Operations Manager normalmente. O agente de Operations Manager e o agente de Log Analytics compartilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Se Operations Manager versão 2012 do agente estiver instalado, **não** habilite o provisionamento automático.<br>

- Já há uma extensão de VM existente<br>
    - Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar apenas um único espaço de trabalho. A Central de Segurança não substitui as conexões existentes nos workspaces do usuário. A central de segurança armazenará dados de segurança da VM no espaço de trabalho já conectado, desde que a solução "segurança" ou "securityFree" tenha sido instalada nela. A central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.  
    - Para ver para qual workspace a extensão existente está enviando dados, execute o teste para [Validar a conectividade com a Central de Segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Como alternativa, você pode abrir Log Analytics espaços de trabalho, selecionar um espaço de trabalho, selecionar a VM e examinar a conexão do agente de Log Analytics. 
    - Se você tiver um ambiente em que o agente de Log Analytics está instalado em estações de trabalho cliente e relatando para um espaço de Log Analytics existente, examine a lista de [sistemas operacionais com suporte pela central de segurança do Azure](security-center-os-coverage.md) para verificar se o sistema operacional tem suporte. Para obter mais informações, consulte [clientes existentes do log Analytics](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Desativar o provisionamento automático<a name="offprovisioning"></a>
É possível desativar o provisionamento automático de recursos a qualquer momento desativando essa configuração na política de segurança. 


1. Retorne ao menu principal da Central de Segurança e selecione a Política de segurança.
2. Clique em **Editar configurações** na linha da assinatura para a qual você deseja desabilitar o provisionamento automático.
3. Na página **política de segurança – coleta de dados** , em **provisionamento automático** , selecione **desativado**.
4. Clique em **Salvar**.

   ![Desabilitar o provisionamento automático][6]

Quando o provisionamento automático está desabilitado (desativado), a seção de configuração do workspace padrão não é exibida.

Se você desativar o provisionamento automático que estava ativado anteriormente:
-   Os agentes não serão provisionados em novas VMs.
-   A Central de Segurança interromperá a coleta de dados do workspace padrão.
 
> [!NOTE]
>  Desabilitar o provisionamento automático não remove o agente de Log Analytics das VMs do Azure em que o agente foi provisionado. Para obter informações sobre como remover a extensão do OMS, consulte [Como fazer para remover extensões OMS instaladas pela Central de Segurança](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Provisionamento manual de agente <a name="manual-agent"></a>
 
Há várias maneiras de instalar o agente de Log Analytics manualmente. Verifique se o provisionamento automático está desabilitado ao efetuar a instalação manual.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implantação de extensão de VM do Operations Management Suite 

Você pode instalar manualmente o agente de Log Analytics, para que a central de segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas.
1. Selecione o Provisionamento automático – Desativado.
2. Crie um espaço de trabalho e defina o tipo de preço para o espaço de trabalho para o qual você pretende definir o agente de Log Analytics:

   a.  No menu principal da Central de Segurança, selecione **Política de segurança**.
     
   b.  Selecione o Workspace ao qual você pretende conectar o agente. Verifique se que o workspace está na mesma assinatura usada na Central de Segurança e se você tem permissões de leitura/gravação no workspace.
       ![Selecione o workspace][8]
3. Defina o tipo de preço.
   ![Selecione o tipo de preço][9] 
   >[!NOTE]
   >Se o workspace já tiver uma solução de **Segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 
   > 

4. Se você quiser implantar os agentes em novas VMs usando um modelo do Resource Manager, instale a extensão de máquina virtual do OMS:

   a.  [Instalar a extensão da máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalar a extensão da máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5. Para implantar as extensões em VMs existentes, siga as instruções em [Coletar dados sobre Máquinas Virtuais do Microsoft Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A seção **Coletar dados de desempenho e eventos** é opcional.
   >
6. Para usar o PowerShell e implantar a extensão, utilize o exemplo a seguir do PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Acesse o **Log Analytics** e clique em **Configurações avançadas**.
    
      ![Definir o Log Analytics][11]

   2. Copie os valores de **WorkspaceID** e **Chave primária**.
  
      ![Copiar valores][12]

   3. Popule a configuração pública e a configuração privada com estes valores:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Ao instalar em uma VM do Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Ao instalar em uma VM do Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Para obter instruções sobre como integrar a Central de Segurança usando o PowerShell, consulte [Automatizar a integração da Central de Segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Solução de problemas

-   Para identificar problemas de instalação do provisionamento automático, consulte [Problemas de integridade do agente de monitoramento](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos de rede do agente de monitoramento, veja [Solução de problemas dos requisitos de rede do agente de monitoramento](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração manual, consulte [como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Para identificar problemas de máquinas virtuais e computadores não monitorados:

    Uma VM ou um computador não será monitorado pela central de segurança se o computador não estiver executando a extensão do agente de Log Analytics. Um computador pode ter um agente local já instalado, por exemplo, o agente direto do OMS ou o agente de System Center Operations Manager. Os computadores com esses agentes são identificados como não monitorados porque não há suporte total para esses agentes na Central de Segurança. Para se beneficiar totalmente de todos os recursos da central de segurança, a extensão do agente de Log Analytics é necessária.

    Para obter mais informações sobre os motivos pelos quais a central de segurança não consegue monitorar com êxito as VMs e os computadores inicializados para o provisionamento automático, consulte [monitoramento de problemas de integridade do agente](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como a coleta de dados e o provisionamento automático na Central de Segurança funcionam. Para saber mais sobre a central de segurança, consulte as seguintes páginas:

* [Perguntas frequentes sobre a Central de Segurança do Azure](faq-general.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
