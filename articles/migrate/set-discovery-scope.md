---
title: Defina o escopo para a descoberta do VMware VM com o Azure Migrate
description: Descreve como definir o escopo de descoberta para avaliação e migração de VMware VMware com o Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337630"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Definir escopo de descoberta para VMs VMware

Este artigo descreve como limitar o escopo de descoberta de VMware VMs que são descobertos pelo [aparelho Azure Migrate](migrate-appliance-architecture.md).

O aparelho Azure Migrate descobre VMMs VMware no local quando você: 

- Use a ferramenta [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) para avaliar vMs vmware para migração para o Azure.
- Use a ferramenta [Migração do Azure:Migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) para [migração sem agente](server-migrate-overview.md) de VMs VMware para o Azure.

## <a name="set-discovery-scope"></a>Definir escopo de descoberta


Depois de configurar o aparelho Azure Migrate para avaliação ou migração de VMs do VMware, o aparelho começa a descobrir VMs e enviar metadados de VM para o Azure. Antes de conectar o aparelho ao vCenter Server para ser desvendado, você pode definir o escopo de detecção para limitar a detecção a data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais.

Para definir o escopo, você atribui permissões na conta que o Azure Migrate está usando para acessar o servidor vCenter.

## <a name="create-a-vcenter-user-account"></a>Criar uma conta de usuário do vCenter

Se você ainda não configurou uma conta de usuário do vCenter que o aparelho Azure Migrate usa para descobrir, avaliar e migrar VMMs VMware, faça isso primeiro.

1.    Faça logon no cliente Web vSphere como o administrador do vCenter Server.
2.    Selecione **Usuários** > **e Grupos de Administração SSO e**clique na guia **Usuários.**
3.    Selecione o ícone **Novo Usuário**.
4.    Preencha as novas informações do usuário > **OK**.

As permissões de conta dependem do que você está implantando.

**Recurso** | **Permissões de conta**
--- | ---
[Avaliar](tutorial-assess-vmware.md)| A conta precisa de acesso somente leitura.
[Descobrir aplicativos/funções/recursos](how-to-discover-applications.md) | A conta precisa de acesso somente leitura, com privilégios habilitados para máquinas virtuais > Operações de Hóspedes.
[Analisar dependências (sem agente)](how-to-create-group-machine-dependencies-agentless.md) | A conta precisa de acesso somente leitura, com privilégios habilitados para máquinas virtuais > Operações de Hóspedes.
[Migrar (sem agente)](tutorial-migrate-vmware.md) | Você precisa de um papel que tenha as permissões certas.<br/><br/> Para criar uma função, faça login no vSphere Web Client como o administrador do vCenter Server. Selecione a instância do vCenter Server > **criar a função**. Especifique um nome de função, por <em>exemplo, Azure_Migrate</em>e atribua as [permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) à função.


## <a name="assign-permissions-on-vcenter-objects"></a>Atribuir permissões em objetos do vCenter

Você pode atribuir permissões em objetos de inventário usando um dos dois métodos:

- Atribua uma função com as permissões necessárias, para a conta que você está usando, para todos os objetos-pai que hospedam VMs que você deseja descobrir/migrar.
- Alternativamente, você pode atribuir a função e a conta do usuário no nível do datacenter e prolímon-las aos objetos da criança. Em seguida, dê à conta uma função **Sem acesso,** para cada objeto que você não deseja descobrir/migrar. Não recomendamos essa abordagem, pois é complicada e pode expor controles de acesso, porque cada novo objeto infantil é automaticamente concedido acesso herdado do pai.

Para atribuir uma função à conta que você está usando para todos os objetos relevantes, faça o seguinte:

- **Para avaliação**: Para avaliação de VM, aplique a função **somente leitura** na conta de usuário do vCenter para todos os objetos-pai que hospedam VMs que você deseja descobrir. Objetos-pai incluídos: host, pasta de hosts, cluster e pasta de clusters na hierarquia, até o data center. Propagar essas permissões para objetos infantis na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

- **Para migração sem agente**: Para migração sem agente, aplique uma função definida pelo usuário com [permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) à conta do usuário, para todos os objetos-pai que hospedam VMs que você deseja descobrir. Você pode nomear o papel <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Suporte ao escopo

Atualmente, a ferramenta Avaliação do Servidor não pode descobrir VMs se a conta vCenter tiver acesso concedido no nível da pasta vCenter VM. Pastas de hosts e clusters são suportadas.

Se você quiser escopo de sua descoberta por pastas VM, complete o próximo procedimento para garantir que a conta vCenter tenha acesso somente leitura atribuído a um nível de VM.

1. Atribua permissões somente de leitura em todas as VMs nas pastas vm que você deseja escopo para detecção.
2. Conceda acesso somente à leitura a todos os objetos-pai que hospedam VMs.
    - Todos os objetos-pai (host, pasta de hosts, cluster, pasta de clusters) na hierarquia até o data center estão incluídos.
    - Não é necessário propagar as permissões para todos os objetos filho.
3. Use as credenciais para descoberta selecionando o datacenter como **Escopo da Coleção**. A configuração de controle de acesso baseada em função garante que a conta de usuário correspondente do vCenter tenha acesso apenas a VMs específicas do inquilino.


## <a name="next-steps"></a>Próximas etapas

[Configure o aparelho](how-to-set-up-appliance-vmware.md) e [inicie a descoberta contínua](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
