---
title: Preparar VMs VMware para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs VMware com as Migrações para Azure.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677292"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs do VMware para avaliação e migração para o Azure

Este artigo ajuda você a preparar a avaliação e/ou a migração de VMs locais do VMware para o Azure usando as [Migrações para Azure](migrate-services-overview.md).



Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do VMware. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o Azure para trabalhar com as Migrações para Azure.
> * Preparar o VMware para avaliação de VM com a ferramenta Migrações para Azure: Avaliação do Servidor.
> * Preparar o VMware para migração de VM com a ferramenta de Migrações para Azure: Migração de Servidor. 

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles são úteis quando você aprende a configurar uma implantação e como uma prova de conceito rápida. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Você precisa dessas permissões para essas tarefas no Azure, antes de poder avaliar ou migrar VMs do VMware.

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário para criar um projeto. 
**Registrar provedores de recursos** | As Migrações para Azure usam um dispositivo leve de Migrações para Azure para descobrir e avaliar VMs VMware, bem como migrá-las para a avaliação de servidor das Migrações para Azure.<br/><br/> Durante o registro do dispositivo, os provedores de recursos são registrados com a assinatura escolhida no dispositivo. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registrar os provedores de recursos, você precisa de uma função de Colaborador ou Proprietário na assinatura.
**Criar aplicativos do Azure AD** | Ao registrar o dispositivo, as Migrações para Azure criam aplicativos do Azure AD (Azure Active Directory). <br/><br/> – O primeiro aplicativo é usado para comunicação entre os agentes em execução no dispositivo e seus respectivos serviços em execução no Azure.<br/><br/> – O segundo aplicativo é usado exclusivamente para acessar o KeyVault criado na assinatura do usuário para a migração de uma VM VMware sem agente. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Você precisa de permissões para criar uma função de aplicativos do Azure AD (disponíveis na função de desenvolvedor de aplicativos).
**Criar um cofre de chaves** | Para migrar VMs VMware usando a migração sem agente, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura.<br/><br/> Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside.




### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Para registrar o dispositivo, você pode atribuir permissões para as Migrações para Azure para criar os aplicativos do Azure AD durante o registro do dispositivo. As permissões podem ser atribuídas usando um dos seguintes métodos:

- **Conceder permissões**: Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- **Atribuir função de desenvolvedor de aplicativos**: Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

> [!NOTE]
> - Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado.


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

Se desejar que o locatário/administrador global conceda permissões, execute o seguinte:

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**. Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos

Ou o locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Leia mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) sobre como atribuir uma função.

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Key Vault

Para permitir que as Migrações para Azure criem um Key Vault, atribua permissões da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.

    - Para executar a avaliação do servidor, permissões de **Colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você precisa ter permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação da VM do VMware

Para se preparar para avaliação da VM do VMware, você precisa:

- **Verificar as configurações do VMware**. Verifique se as vCenter Server e as VMs que você deseja migrar atendem aos requisitos.
- **Configurar uma conta para avaliação**. As Migrações para Azure usam essa conta para acessar o vCenter Server para descobrir VMs para avaliação.
- **Verificar os requisitos do dispositivo**. Verifique os requisitos de implantação para o dispositivo de Migrações para Azure antes de implantá-lo.

### <a name="verify-vmware-settings"></a>Verificar as configurações de VMware

1. [Verifique](migrate-support-matrix-vmware.md#vmware-requirements) os requisitos do servidor do VMware.
2. [Garanta](migrate-support-matrix-vmware.md#port-access) que as portas necessárias estejam abertas no vCenter Server.
3. No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As Migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente.

- Se planejar descobrir aplicativos ou visualizar dependências sem agente, crie uma conta do vCenter Server com acesso somente leitura juntamente com os privilégios habilitados para **Máquinas virtuais** > **Operações de Convidado**.

  ![Privilégios da conta do vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Se não estiver planejando fazer a descoberta de aplicativos e a visualização de dependências sem agente, configure uma conta somente leitura para o vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Verifique as configurações do dispositivo para avaliação

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-appliance.md#appliance---vmware) os requisitos do dispositivo de Migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso da porta para o dispositivo.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para migração do VMware sem agente

Examine os requisitos para [migração sem agente](server-migrate-overview.md) de VMs VMware.

1. [Examinar](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) os requisitos do servidor VMware.
2. [Examine as permissões](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) que as Migrações para Azure precisam para acessar o vCenter Server.
3. [Examine](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) os requisitos de VMs VMware.
4. [Examine](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) os requisitos do dispositivo de Migrações para Azure.
5. Observe o acesso via URL necessário para as nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
6. Examine os requisitos de [acesso à porta](migrate-support-matrix-vmware-migration.md#agentless-ports).

## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para migração do VMware baseada em agente

Examine os requisitos para [migração de VMs do VMware baseada em agente](server-migrate-overview.md).

1. [Examinar](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) os requisitos do servidor VMware.
2. [Examine as permissões](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) que as Migrações para Azure precisam para acessar o vCenter Server.
2. [Examine](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) os requisitos de VMs VMware, incluindo a instalação do Serviço de mobilidade em cada VM que você deseja migrar.
3. A migração baseada em agente usa um dispositivo de replicação:
    - [Examine](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação para o dispositivo de replicação.
    - [Examine as opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no dispositivo.
    - Observe o acesso via URL necessário para as nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais](migrate-replication-appliance.md#azure-government-url-access).
    - Examine os requisitos de [acesso à porta](migrate-replication-appliance.md#port-access) para o dispositivo de replicação.
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurou permissões do Azure.
> * Preparou o VMware para avaliação e migração.


Prossiga para o segundo tutorial para configurar um projeto das Migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs do VMware](./tutorial-assess-vmware.md)
