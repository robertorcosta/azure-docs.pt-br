---
title: Preparar VMs do VMware para avaliação e migração para o Azure usando as Migrações para Azure | Microsoft Docs
description: Descreve como preparar a avaliação e a migração de VMs locais do VMware para o Azure usando as Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4cc04e9ab0acdc9d0cdff77ed1de7bea1c1362d4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498482"
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
--- | --- | ---
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões para criar um projeto. 
**Registrar o dispositivo de Migrações para Azure** | As Migrações para Azure usam um dispositivo leve de migrações para Azure para avaliar VMs do VMware com a avaliação de servidor de migrações para Azure e executar a [migração sem agente](server-migrate-overview.md) de VMs do VMware com migração de servidor de migrações para Azure. Esse dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para as Migrações para Azure.<br/><br/>Durante o registro, as Migrações para Azure criam dois aplicativos Azure AD (Azure Active Directory) que identificam o dispositivo de forma exclusiva e precisa de permissões para criar esses aplicativos.<br/> - O primeiro aplicativo comunica-se com os pontos de extremidade de serviço das Migrações para Azure.<br/> - O segundo aplicativo acessa um Azure Key Vault criado durante o registro para armazenar informações de aplicativo do Azure AD e definições de configuração do dispositivo.
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

### <a name="assign-role-assignment-permissions"></a>Atribuir permissões de atribuição de função

Para habilitar a Migrações para Azure para criar um cofre de chaves, atribua permissões de atribuição de função da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.

    - Para executar a avaliação do servidor, permissões de **Colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você precisa ter permissões de **Proprietário** (ou **Colaborador** e **Administrador de Acesso do Usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação da VM do VMware

Para se preparar para avaliação da VM do VMware, você precisa:

- **Verificar as configurações do VMware**. Verifique se as vCenter Server e as VMs que você deseja migrar atendem aos requisitos.
- **Configurar uma conta de avaliação**. As Migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação. Você precisa de uma conta somente leitura para o acesso de Migrações para Azure.
- **Verificar os requisitos do dispositivo**. Verifique os requisitos de implantação para o dispositivo de Migrações para Azure usado para avaliação.

### <a name="verify-vmware-settings"></a>Verificar as configurações de VMware

1. [Verifique](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) os requisitos do servidor do VMware.
2. [Garanta](migrate-support-matrix-vmware.md#assessment-port-requirements) que as portas necessárias estejam abertas no vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As Migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente. Somente para a avaliação, configure uma conta somente leitura para o vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Verifique as configurações do dispositivo para avaliação

Verifique os requisitos do dispositivo antes de implantá-lo.

1. [Verifique](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos e as limitações do dispositivo.
2. Se você estiver usando um proxy de firewall baseado em URL, [examine](migrate-support-matrix-vmware.md#assessment-url-access-requirements) as URLs do Azure que o dispositivo precisará acessar. Certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.
3. Examine os [dados de desempenho](migrate-appliance.md#collected-performance-data-vmware)] e os [metadados](migrate-appliance.md#collected-metadata-vmware) que o dispositivo coleta durante a descoberta e avaliação.
4. [Observe](migrate-support-matrix-vmware.md#assessment-port-requirements) as portas acessadas pelo dispositivo.
5. No vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Você implanta o dispositivo das Migrações para Azure como uma VM do VMware usando um arquivo OVA. 

Se estiver usando um firewall.proxy baseado em URL, permita o acesso às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements) necessárias.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para migração do VMware sem agente

Examine os requisitos para migração sem agente de VMs do VMware.

1. [Examinar](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) os requisitos do servidor VMware
2. Configure uma conta com as [permissões necessárias](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), de modo que as Migrações para Azure possam acessar o vCenter Server para migração sem agente usando as Migrações para Azure do servidor de migração.
3. [Examine](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) os requisitos para usar o dispositivo de Migrações para Azure para a migração sem agente.
5. Observe o [acesso à URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) e o [acesso à porta](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) que o dispositivo de Migrações para Azure precisa para migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para migração do VMware baseada em agente

Examine os requisitos para [migração de VMs do VMware baseada em agente](server-migrate-overview.md).

1. [Examinar](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos do servidor VMware. 
2. Configure uma conta com as [permissões necessárias](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). de modo que as Migrações para Azure possam acessar o vCenter Server para migração baseada em agente usando as Migrações para Azure do servidor de migração.
3. [Examine](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos para as VMs do VMware que você deseja migrar para o Azure usando a migração baseada em agente, incluindo a instalação do serviço de mobilidade em cada VM que deseja migrar.
4. Observe o [acesso à URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Examine o [acesso à porta](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) que os componentes de Migrações para Azure precisam para o acesso baseado em agente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você irá:
 
> [!div class="checklist"] 
> * Configurou permissões do Azure.
> * Preparou o VMware para avaliação e migração.


Prossiga para o segundo tutorial para configurar um projeto das Migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"] 
> [Avaliar VMs do VMware](./tutorial-assess-vmware.md) 

