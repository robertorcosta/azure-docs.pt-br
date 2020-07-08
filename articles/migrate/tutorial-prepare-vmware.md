---
title: Preparar VMs VMware para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs VMware com as Migrações para Azure.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8b812924c0922d460c631baec8b0e13a9f45cd76
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109567"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs do VMware para avaliação e migração para o Azure

Este artigo ajuda você a preparar a avaliação e a migração de VMs locais do VMware para o Azure usando as [Migrações para Azure](migrate-services-overview.md).

Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do VMware. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o Azure para trabalhar com as Migrações para Azure.
> * Preparar-se para avaliar VMs do VMware com a ferramenta de Avaliação do Servidor das Migrações para Azure.
> * Preparar-se para migrar VMs do VMware com a ferramenta de Migração de Servidor das Migrações para Azure. 

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles são úteis como uma prova de conceito rápida. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

A tabela resume as tarefas que você precisa concluir no Azure. Há instruções para cada tarefa após a tabela.

**Tarefa** | **Detalhes** | **Permissões**
--- | --- | ---
**Criar um projeto de Migrações para Azure** | Um projeto das Migrações para Azure fornece um local central para orquestrar e gerenciar avaliações e migrações com as ferramentas das Migrações para Azure, ferramentas da Microsoft e ofertas de terceiros. | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário no grupo de recursos no qual o projeto reside.
**Registrar dispositivo** | As Migrações para Azure usam um dispositivo leve de Migrações para Azure a fim de descobrir VMs, avaliá-las com a ferramenta de Avaliação de Servidor e migrá-las usando a migração sem agente com a ferramenta de Migração de Servidor. [Saiba mais](migrate-appliance-architecture.md#appliance-registration) sobre o registro. | Para registrar o dispositivo, sua conta do Azure precisa de permissões de Colaborador ou de Proprietário na assinatura do Azure.
**Criar aplicativos do Azure AD** | Ao registrar um dispositivo, as Migrações para Azure criam aplicativos do Azure AD (Azure Active Directory). <br/><br/> – O primeiro aplicativo é usado para comunicação entre os agentes em execução no dispositivo e nas Migrações para Azure. <br/><br/> – O segundo aplicativo é usado exclusivamente para acessar o KeyVault criado na assinatura do usuário para a migração de uma VM VMware sem agente.   | Sua conta do Azure precisa de permissões para criar aplicativos do Azure AD.
**Criar um Key Vault** | Para migrar VMs VMware usando a migração sem agente, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de replicação em sua assinatura. | Para permitir que as Migrações para Azure criem o Key Vault, você define as permissões (Proprietário ou Colaborador e Administrador de Acesso de Usuários) no grupo de recursos no qual o projeto de Migrações para Azure reside.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Atribuir permissões para criar aplicativos do Azure AD

Para registrar o dispositivo, sua conta do Azure precisa de permissões para criar aplicativos do Azure AD. Atribua as permissões usando um destes métodos:

- **Método 1: conceder permissões à conta**: Um locatário/administrador global pode conceder permissões a contas de usuário no locatário para criar e registrar aplicativos do Azure AD.
- **Método 2: atribuir uma função com as permissões a uma conta de usuário**: Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões necessárias) à conta de usuário.

> [!NOTE]
> Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado.


#### <a name="method-1-grant-permissions-to-the-account"></a>Método 1: conceder permissões à conta

Conceda permissões à conta conforme descrito a seguir:

1. Verifique se você é um locatário ou administrador global. Depois, no Azure AD, navegue até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. Configure **Registros de aplicativo** para **Sim**. Essa é uma configuração padrão que não é confidencial. [Saiba mais](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Método 2: Atribuir função de Desenvolvedor de Aplicativos

Ou o locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Leia mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) sobre como atribuir uma função.

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Key Vault

Para permitir que as Migrações para Azure criem um Key Vault, atribua permissões da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.

    - Para executar a avaliação do servidor, permissões de **Colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você precisa ter permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos.

## <a name="prepare-for-assessment"></a>Preparar-se para avaliação

Para se preparar para avaliação da VM do VMware, você precisa:

1. **Verificar as configurações de VMware**. Verifique se as vCenter Server e as VMs que você deseja migrar atendem aos requisitos.
2. **Configurar permissões para avaliação**. As Migrações para Azure usam uma conta do vCenter para acessar o vCenter Server, bem como para descobrir VMs e avaliá-las.
3. **Verifique os requisitos do dispositivo**. Verifique os requisitos de implantação para o dispositivo de Migrações para Azure antes de implantá-lo no próximo tutorial.

### <a name="verify-vmware-settings"></a>Verificar as configurações de VMware

1. [Verifique os requisitos do VMware](migrate-support-matrix-vmware.md#vmware-requirements) para avaliação.
2. [Garanta](migrate-support-matrix-vmware.md#port-access-requirements) que as portas necessárias estejam abertas no vCenter Server.
3. No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Isso é necessário quando você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA.
4. Há algumas alterações necessárias nas VMs antes de migrá-las para o Azure.

    - Para alguns sistemas operacionais, as Migrações para Azure fazem essas alterações automaticamente. 
    - É importante fazer essas alterações antes de iniciar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.
    - Examine as alterações para [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines) que você precisa fazer.


### <a name="set-up-permissions-for-assessment"></a>Configurar permissões para avaliação

As Migrações para Azure precisam acessar o vCenter Server para que o dispositivo das Migrações para Azure possa descobrir VMs para avaliação e migração sem agente. Configure uma conta da seguinte maneira:

1. No cliente Web vSphere, abra **Administração** > **Acesso** > **Usuários e Grupos do SSO**.
2. Em **Usuários**, clique no ícone **Novo Usuário**.
3. Digite os detalhes do novo usuário.
4. Selecione as permissões de acordo com os valores da tabela.

    **Recurso** | **Permissões de conta**
    --- | ---
    [Avaliar VMs](tutorial-assess-vmware.md) | Para a avaliação, a conta precisa de acesso somente leitura.
    [Descobrir aplicativos, funções e recursos de VM](how-to-discover-applications.md) | Se você quiser usar a descoberta de aplicativos, a conta somente leitura usada para avaliação precisará de privilégios habilitados para **Máquinas virtuais** > **Operações de Convidado**.
    [Analisar dependências em VMs (sem agente)](how-to-create-group-machine-dependencies-agentless.md) | Se você quiser analisar dependências, a conta somente leitura usada para avaliação precisará de privilégios habilitados para **Máquinas virtuais** > **Operações de Convidado**.
    
> [!NOTE]
> Se você quiser que a descoberta de VMs para avaliação seja limitada para um escopo específico, examine [este artigo](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Verifique as configurações do dispositivo para avaliação

No [próximo tutorial](tutorial-assess-vmware.md), você configura o dispositivo das Migrações para Azure e inicia a avaliação. Antes de fazer isso, examine os requisitos de dispositivo da seguinte maneira: 

1. [Examine](migrate-appliance.md#appliance---vmware) os requisitos para implantação do dispositivo de Migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) as portas que o dispositivo usa.
4. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.

## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para migração do VMware sem agente

Você pode migrar VMs VMware usando a [migração baseada em agente ou sem agente](server-migrate-overview.md). Esta seção resume os requisitos para migração sem agente.

1. [Decida](server-migrate-overview.md#compare-migration-methods) se você quer usar a migração sem agente.
2. [Examine](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) os requisitos do hipervisor para computadores que você deseja migrar.
3. [Examine](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) os requisitos para as VMs do VMware que você deseja migrar usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) os requisitos do dispositivo de Migrações para Azure para migração sem agente.
5. Observe o acesso via URL necessário para as nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
6. Examine os requisitos de [acesso à porta](migrate-support-matrix-vmware-migration.md#port-requirements-agentless).
7. Configure permissões para migração sem agente, conforme descrito no procedimento a seguir.


### <a name="assign-permissions-to-an-account"></a>Atribuir permissões a uma conta

O dispositivo das Migrações para Azure se conecta ao vCenter Server para descobrir VMs e migrá-las sem agente. Você pode atribuir as permissões de que o dispositivo precisa para a conta de usuário ou, alternativamente, criar uma função com as permissões e atribuir essa função a uma conta de usuário.

1. No cliente Web vSphere, abra **Administração** > **Acesso** > **Usuários e Grupos do SSO**.
2. Em **Usuários**, clique no ícone **Novo Usuário**.
3. Digite os detalhes do novo usuário.
4. Atribuir [estas permissões](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Criar uma função e atribuir a uma conta

Alternativamente, você pode criar uma conta. Depois, crie uma função e atribua-a à conta, da seguinte maneira:

1. Entrar no cliente Web vSphere como o administrador do vCenter Server
2. Selecione a instância do vCenter Server > **Criar função**.
3. Especifique um nome de função (por exemplo, <em>Azure_Migrate</em>) e atribua as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) à função.

    ![Privilégios da conta do vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Agora, crie uma conta e atribua a função à conta.

> [!NOTE]
> Se você quiser que a descoberta de VMs para migração sem agente seja limitada para um escopo específico, examine [este artigo](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para migração do VMware baseada em agente

Você pode migrar VMs VMware usando a [migração baseada em agente ou sem agente](server-migrate-overview.md). Esta seção resume os requisitos para migração baseada em agente.

1. [Decida](server-migrate-overview.md#compare-migration-methods) se você quer usar a migração baseada em agente.
1. [Examine](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) os requisitos do hipervisor para computadores que você deseja migrar.
2. [Examine](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) os requisitos de VMs VMware, incluindo a instalação do Serviço de mobilidade em cada VM que você deseja migrar.
3. A migração baseada em agente usa um dispositivo de replicação:
    - [Examine](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação para o dispositivo de replicação.
    - [Examine as opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no dispositivo.
    - Observe o acesso via URL necessário para as nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais](migrate-replication-appliance.md#azure-government-url-access).
    - Examine os requisitos de [acesso à porta](migrate-replication-appliance.md#port-access) para o dispositivo de replicação.
4. Há algumas alterações necessárias nas VMs para migrá-las para o Azure. Examine as alterações para [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines) que você precisa fazer.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurou permissões do Azure.
> * Preparou o VMware para avaliação e migração.


Prossiga para o segundo tutorial para configurar um projeto das Migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs do VMware](./tutorial-assess-vmware.md)
