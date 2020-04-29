---
title: Definir o escopo para a descoberta de VM VMware com migrações para Azure
description: Descreve como definir o escopo de descoberta para avaliação e migração de VM VMware com migrações para Azure.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337630"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Definir escopo de descoberta para VMs VMware

Este artigo descreve como limitar o escopo de descoberta para VMs VMware que são descobertas pelo dispositivo de [migrações para Azure](migrate-appliance-architecture.md).

O dispositivo de migrações para Azure descobre VMs VMware locais quando você: 

- Use a ferramenta [migrações para Azure: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) para avaliar VMs VMware para migração para o Azure.
- Use o [migrações para Azure:](migrate-services-overview.md#azure-migrate-server-migration-tool) ferramenta de migração de servidor para [migração sem agente](server-migrate-overview.md) de VMs do VMware para o Azure.

## <a name="set-discovery-scope"></a>Definir escopo de descoberta


Depois de configurar o dispositivo de migração do Azure para avaliação ou migração de VMs do VMware, o dispositivo inicia a descoberta de VMs e envia metadados da VM para o Azure. Antes de conectar o dispositivo ao vCenter Server para descoberta, você pode definir o escopo de descoberta para limitar a descoberta a data centers de vCenter Server, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais.

Para definir o escopo, você atribui permissões na conta que o migrações para Azure está usando para acessar o vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Criar uma conta de usuário do vCenter

Se você ainda não configurou uma conta de usuário do vCenter que o dispositivo de migrações para Azure usa para descobrir, avaliar e migrar VMs VMware, faça isso primeiro.

1.    Faça logon no cliente Web vSphere como o administrador do vCenter Server.
2.    Selecione **Administração** > **usuários e grupos SSO**e clique na guia **usuários** .
3.    Selecione o ícone **Novo Usuário**.
4.    Preencha as informações do novo usuário > **OK**.

As permissões de conta dependem do que você está implantando.

**Recurso** | **Permissões de conta**
--- | ---
[Avaliar](tutorial-assess-vmware.md)| A conta precisa de acesso somente leitura.
[Descobrir aplicativos/funções/recursos](how-to-discover-applications.md) | A conta precisa de acesso somente leitura, com privilégios habilitados para máquinas virtuais > operações de convidado.
[Analisar dependências (sem agente)](how-to-create-group-machine-dependencies-agentless.md) | A conta precisa de acesso somente leitura, com privilégios habilitados para máquinas virtuais > operações de convidado.
[Migrar (sem agente)](tutorial-migrate-vmware.md) | Você precisa de uma função atribuída às permissões corretas.<br/><br/> Para criar uma função, faça logon no cliente Web vSphere como o administrador do vCenter Server. Selecione a instância de vCenter Server > **criar função**. Especifique um nome de função, por exemplo <em>Azure_Migrate</em>, e atribua as [permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) à função.


## <a name="assign-permissions-on-vcenter-objects"></a>Atribuir permissões em objetos do vCenter

Você pode atribuir permissões em objetos de inventário usando um dos dois métodos:

- Atribua uma função com as permissões necessárias para a conta que você está usando, para todos os objetos pai que hospedam as VMs que você deseja descobrir/migrar.
- Como alternativa, você pode atribuir a função e a conta de usuário no nível do datacenter e propagá-las aos objetos filho. Em seguida, dê à conta uma função **sem acesso** , para cada objeto que você não deseja descobrir/migrar. Não recomendamos essa abordagem, pois ela é complicada e pode expor controles de acesso, pois cada novo objeto filho recebe automaticamente o acesso herdado do pai.

Para atribuir uma função à conta que você está usando para todos os objetos relevantes, faça o seguinte:

- **Para avaliação**: para a avaliação de VM, aplique a função **somente leitura** à conta de usuário do vCenter para todos os objetos pai que hospedam VMs que você deseja descobrir. Objetos pai incluídos: host, pasta de hosts, cluster e pasta de clusters na hierarquia, até o datacenter. Propague essas permissões para objetos filho na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

- **Para a migração sem agente**: para a migração sem agente, aplique uma função definida pelo usuário com [as permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) para a conta de usuário, para todos os objetos pai que hospedam as VMs que você deseja descobrir. Você pode nomear a função <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Suporte a escopo

Atualmente, a ferramenta de avaliação do servidor não pode descobrir VMs se a conta do vCenter tem acesso concedido no nível da pasta da VM do vCenter. Há suporte para pastas de hosts e clusters.

Se você quiser fazer o escopo de sua descoberta por pastas de VM, conclua o próximo procedimento para garantir que a conta do vCenter tenha acesso somente leitura atribuído em um nível de VM.

1. Atribua permissões somente leitura em todas as VMs nas pastas de VM nas quais você deseja definir o escopo para descoberta.
2. Conceda acesso somente leitura a todos os objetos pai que hospedam VMs.
    - Todos os objetos pai (host, pasta de hosts, cluster, pasta de clusters) na hierarquia até o datacenter são incluídos.
    - Não é necessário propagar as permissões para todos os objetos filho.
3. Use as credenciais para descoberta selecionando o datacenter como **Escopo da Coleção**. A configuração do controle de acesso baseado em função garante que a conta de usuário do vCenter correspondente tenha acesso a apenas VMs específicas do locatário.


## <a name="next-steps"></a>Próximas etapas

[Configure o dispositivo](how-to-set-up-appliance-vmware.md) e [inicie a descoberta contínua](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
