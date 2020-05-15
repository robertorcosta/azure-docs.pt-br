---
title: Preparar servidores físicos para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de servidores físicos com as Migrações para Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b7bde5df943a35bfcf08ace3b454a26dae8c1d89
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901419"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para a avaliação e a migração de servidores físicos para o Azure

Este artigo descreve como preparar a avaliação dos servidores físicos locais para o Azure usando as [Migrações para Azure](migrate-services-overview.md).

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 

Este tutorial é o primeiro de uma série que mostra como avaliar os servidores físicos com as Migrações para Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prepare o Azure. Configurar permissões para sua conta do Azure e recursos para trabalhar com as Migrações para Azure.
> * Preparar servidores físicos locais para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine a avaliação dos servidores físicos.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure-for-server-assessment"></a>Preparar o Azure para avaliação do servidor

Configure o Azure para trabalhar com as Migrações para Azure. 

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário para criar um projeto. 
**Registrar provedores de recursos (somente avaliação)** | As Migrações para Azure usam um dispositivo leve de Migrações para Azure para descobrir e avaliar computadores com a avaliação de servidor das Migrações para Azure.<br/><br/> Durante o registro do dispositivo, os provedores de recursos são registrados com a assinatura escolhida no dispositivo. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registrar os provedores de recursos, você precisa de uma função de Colaborador ou Proprietário na assinatura.
**Criar aplicativo do Azure AD (somente avaliação)** | Ao registrar o dispositivo, o recurso Migrações para Azure cria um aplicativo do Azure AD (Azure Active Directory) que é usado para a comunicação entre os agentes em execução no dispositivo com seus respectivos serviços em execução no Azure. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Você precisa de permissões para criar uma função de aplicativos do Azure AD (disponíveis na função de desenvolvedor de aplicativos).


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto 

Verifique se você tem permissões para criar um projeto de Migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo 

Você pode atribuir permissões para as Migrações para Azure para criar o aplicativo do Azure AD durante o registro do dispositivo usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

> [!NOTE]
> - O aplicativo não tem nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado.


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Preparar o Azure para migração do servidor físico

Prepare o Azure para migrar servidores físicos usando a Migração de Servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto de Migrações para Azure** | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário para criar um projeto.
**Verificar as permissões para sua conta do Azure** | Sua conta do Azure precisa de permissões para criar uma VM e gravar em um disco gerenciado do Azure.
**Criar uma rede do Azure** | Configurar uma rede no Azure.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta do Azure

Atribua a função Colaborador da Máquina Virtual à conta do Azure. Isso fornece permissões para:
  - Criar uma VM no grupo de recursos selecionado.
  - Criar uma VM na rede virtual selecionada.
  - Gravar em um disco gerenciado do Azure. 

### <a name="create-an-azure-network"></a>Criar uma rede do Azure

[Configurar](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma VNet (rede virtual) do Azure. Quando você replica para o Azure, as VMs do Azure são criadas e ingressadas na VNet do Azure que você especifica ao configurar a migração.


## <a name="prepare-for-physical-server-assessment"></a>Preparar para avaliação de servidor físico

Para se preparar para a avaliação do servidor físico, você precisa verificar as configurações do servidor físico e para implantação do dispositivo:

### <a name="verify-physical-server-settings"></a>Verificar as configurações do servidor físico

1. Verifique os [requisitos do servidor físico](migrate-support-matrix-physical.md#physical-server-requirements) para avaliação do servidor.
2. Verifique se as [portas necessárias](migrate-support-matrix-physical.md#port-access) estão abertas nos servidores físicos.


### <a name="verify-appliance-settings"></a>Verificar configurações do dispositivo

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-appliance.md#appliance---physical) os requisitos de dispositivo para servidores físicos.
2. Examine as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine](migrate-appliance.md#collected-data---vmware) o que o dispositivo coletará durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-physical.md#port-access) os requisitos da porta de acesso para avaliação do servidor.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurar uma conta para descoberta de servidor físico

As Migrações para Azure precisam de permissões para descobrir servidores locais.

- **Windows:** configure uma conta de usuário local em todos os servidores Windows que você deseja incluir na descoberta. A conta de usuário precisa ser adicionada aos seguintes grupos: – Usuários de gerenciamento remoto – Usuários do Monitor de Desempenho – Usuários de Log de Desempenho
- **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir.

## <a name="prepare-for-physical-server-migration"></a>Preparar para migração do servidor físico

Examine os requisitos para migração de servidores físicos.

> [!NOTE]
> Ao migrar computadores físicos, a Migração de Servidor das Migrações para Azure usa a mesma arquitetura de replicação que a recuperação de desastre baseada em agente do serviço de Azure Site Recovery, e alguns dos componentes compartilham a mesma base de código. Alguns conteúdos podem ser vinculados à documentação do Site Recovery.

- [Examine](migrate-support-matrix-physical-migration.md#physical-server-requirements) os requisitos do servidor físico para a migração.
- A Migração de Servidor das Migrações para Azure usa um servidor de replicação para a migração de servidor físico:
    - [Examine](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do dispositivo de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no dispositivo.
    - Examine as [URLs do Azure](migrate-appliance.md#url-access) necessárias para que o dispositivo de replicação acesse as nuvens públicas e governamentais.
    - Examine os requisitos de acesso da [porta] (migrate-replication-appliance.md#port-access) para o dispositivo de replicação.




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurou permissões de conta do Azure.
> * Preparar servidores físicos para avaliação e migração.

Prossiga para o próximo tutorial para criar um projeto das Migrações para Azure e avaliar os servidores físicos para migração para o Azure

> [!div class="nextstepaction"]
> [Avaliar servidores físicos](./tutorial-assess-physical.md)
