---
title: Preparar VMs VMware para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs VMware com as Migrações para Azure.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: f00d5ba4841427098b0ab79ad1930e357008b6e0
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030788"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs do VMware para avaliação e migração para o Azure

Este artigo ajuda você a preparar a avaliação e/ou a migração de VMs locais do VMware para o Azure usando as [Migrações para Azure](migrate-services-overview.md).

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do VMware. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o Azure para trabalhar com as Migrações para Azure.
> * Preparar VMware para avaliação da VM.
> * Preparar VMware para migração da VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles são úteis quando você aprende a configurar uma implantação e como uma prova de conceito rápida. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, consulte as instruções para avaliação e migração do VMware.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Você precisa dessas permissões.

**Tarefa** | **Permissões**
--- | ---
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões para criar um projeto.
**Registrar o dispositivo de Migrações para Azure** | As Migrações para Azure usam um dispositivo leve de migrações para Azure para avaliar VMs do VMware com a avaliação de servidor de migrações para Azure e executar a [migração sem agente](server-migrate-overview.md) de VMs do VMware com migração de servidor de migrações para Azure. Esse dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para as Migrações para Azure.<br/><br/>Durante o registro do dispositivo, os provedores de recursos a seguir são registrados com a assinatura escolhida no dispositivo: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Para registrar os provedores de recursos, você precisa de uma função de Colaborador ou Proprietário na assinatura.<br/><br/> Como parte da integração, as Migrações para Azure criam dois aplicativos Microsoft Azure AD (Active Directory):<br/> – O primeiro aplicativo é usado para comunicação (autenticação e autorização) entre os agentes em execução no dispositivo com seus respectivos serviços em execução no Azure. Esse aplicativo não tem privilégios para fazer chamadas do ARM ou acesso RBAC em qualquer recurso.<br/> - O segundo aplicativo é usado exclusivamente para acessar o KeyVault criado na assinatura do usuário para a migração sem agente. Ele é fornecido com um acesso RBAC no Azure Key Vault (criado no locatário do cliente) quando a descoberta é iniciada no dispositivo.
**Criar um cofre de chaves** | Para migrar VMs do VMware usando a Migração de Servidor de Migrações para Azure, as Migrações para Azure criam um cofre de chaves para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de Migrações para Azure reside.






### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Para registrar o dispositivo, você pode atribuir permissões para as Migrações para Azure para criar os aplicativos do Azure AD durante o registro do dispositivo. As permissões podem ser atribuídas usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

> [!NOTE]
> - Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado.


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**. Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

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
- **Configurar uma conta de avaliação**. As Migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação.
- **Verificar os requisitos do dispositivo**. Verifique os requisitos de implantação para o dispositivo de Migrações para Azure usado para avaliação.

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

1. [Verificar](migrate-appliance.md#appliance---vmware) os requisitos do dispositivo para VMs do VMware.
2. [Examine](migrate-appliance.md#url-access) as URLs do Azure que o dispositivo precisará acessar. Se estiver usando um firewall ou proxy baseado em URL, certifique-se de que ele permite o acesso às URLs necessárias.
3. [Examine](migrate-appliance.md#collected-data---vmware) o que o dispositivo coletará durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso da porta para o dispositivo.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para migração do VMware sem agente

Examine os requisitos para migração sem agente de VMs do VMware.

1. [Examine](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) os requisitos de servidor do VMware e as [permissões](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) de que as Migrações para Azure precisam para acessar o vCenter Server para migração sem agente usando a Migração de Servidor das Migrações para Azure.
2. [Examine](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) os requisitos para usar o dispositivo de Migrações para Azure para a migração sem agente.
5. Observe o [acesso à URL](migrate-appliance.md#url-access) e [acesso à porta](migrate-support-matrix-vmware-migration.md#agentless-ports) necessários para a migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para migração do VMware baseada em agente

Examine os requisitos para [migração de VMs do VMware baseada em agente](server-migrate-overview.md).

1. [Examine](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) os requisitos de servidor do VMware e as permissões de que as Migrações para Azure precisam para acessar o vCenter Server para migração baseada em agente usando a Migração de Servidor das Migrações para Azure.
2. [Examine](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração baseada em agente, incluindo a instalação do serviço de mobilidade em cada VM que deseja migrar.
3. As migrações baseadas em agente usam um dispositivo de replicação:
    - [Examine](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do dispositivo de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no dispositivo.
    - Examine os requisitos de acesso da [URL](migrate-replication-appliance.md#url-access) e da [porta](migrate-replication-appliance.md#port-access) para o dispositivo de replicação.
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurou permissões do Azure.
> * Preparou o VMware para avaliação e migração.


Prossiga para o segundo tutorial para configurar um projeto das Migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs do VMware](./tutorial-assess-vmware.md)
