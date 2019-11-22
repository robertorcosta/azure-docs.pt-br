---
title: Preparar servidores físicos para avaliação para o Azure com a avaliação de servidor de Migrações para Azure
description: Descreve como preparar a avaliação e a migração de servidores físicos para o Azure usando a Avaliação do Servidor de Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 176dfdabeee6299bba0eb17085be25ced3e97993
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091909"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para a avaliação e a migração de servidores físicos para o Azure

Este artigo descreve como preparar a avaliação dos servidores físicos locais para o Azure usando as [Migrações para Azure](migrate-services-overview.md).


> [!NOTE]
> Se você ainda não vir alguns desses recursos no portal das Migrações para Azure, aguarde. Eles aparecerão na semana seguinte ou em breve.

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 

Este tutorial é o primeiro de uma série que mostra como avaliar os servidores físicos com as Migrações para Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prepare o Azure. Configurar permissões para sua conta do Azure e recursos para trabalhar com as Migrações para Azure.
> * Preparar servidores físicos locais para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine a avaliação dos servidores físicos.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Você precisa configurar permissões para a implantação de Migrações para Azure.

- Permissões para sua conta do Azure para criar um projeto de Migrações para Azure. 
- Permissões para sua para registrar o dispositivo de Migrações para Azure. O dispositivo é usado para descoberta e migração do Hyper-V. Durante o registro do dispositivo, as Migrações para Azure criam dois aplicativos Azure AD (Azure Active Directory) que identificam o dispositivo de forma exclusiva:
    - O primeiro aplicativo comunica-se com os pontos de extremidade de serviço das Migrações para Azure.
    - O segundo aplicativo acessa um Azure Key Vault criado durante o registro para armazenar informações de aplicativo do Azure AD e definições de configuração do dispositivo.



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

Verifique se você tem permissões para criar um projeto de Migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Você pode atribuir permissões para as Migrações para Azure para criar os aplicativos do Azure AD desenvolvidos durante o registro do dispositivo usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado. 


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos 

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Preparar para avaliação de servidor físico

Para se preparar para a avaliação do servidor físico, você precisa verificar as configurações do servidor físico e para implantação do dispositivo:

### <a name="verify-physical-server-settings"></a>Verificar as configurações do servidor físico

1. Verifique os [requisitos do servidor físico](migrate-support-matrix-physical.md#assessment-physical-server-requirements) para avaliação do servidor.
2. Verifique se as [portas necessárias](migrate-support-matrix-physical.md#assessment-port-requirements) estão abertas nos servidores físicos.


### <a name="verify-appliance-settings"></a>Verificar configurações do dispositivo

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-support-matrix-physical.md#assessment-appliance-requirements) os requisitos do dispositivo.
2. [Examine](migrate-support-matrix-physical.md#assessment-appliance-url-access) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-physical.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurar uma conta para descoberta de servidor físico

As Migrações para Azure precisam de permissões para descobrir servidores locais.

- **Windows:** Configure uma conta de usuário local em todos os servidores Windows que você deseja incluir na descoberta. A conta de usuário precisa ser adicionada aos seguintes grupos:      – Usuários do Gerenciamento Remoto      – Usuários do Monitor de Desempenho      – Usuários do Log de Desempenho
- **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você irá:
 
> [!div class="checklist"] 
> * Configurou permissões de conta do Azure.
> * Preparar servidores físicos para avaliação e migração.

Prossiga para o próximo tutorial para criar um projeto das Migrações para Azure e avaliar os servidores físicos para migração para o Azure

> [!div class="nextstepaction"] 
> [Avaliar servidores físicos](./tutorial-assess-physical.md) 
