---
title: Preparar VMs do Hyper-V para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs do Hyper-V com as Migrações para Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ca9020a9c306eea39d75c15c96b5f9fe9bcc11fe
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84770536"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para a avaliação e a migração de VMs do Hyper-V para o Azure

Este artigo ajuda você a preparar-se para a avaliação e migração de VMs do Hyper-V locais para o Azure usando a [Avaliação do Servidor das Migrações para Azure](migrate-services-overview.md#azure-migrate-server-assessment-tool) e a [Migração de Servidor das Migrações para Azure](migrate-services-overview.md#azure-migrate-server-migration-tool).


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o Azure para trabalhar com as Migrações para Azure.
> * Preparar-se para avaliar as VMs do Hyper-V.
> * Preparar-se para migrar as VMs do Hyper-V 

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

A tabela resume as tarefas que você precisa concluir no Azure. Você encontrará instruções depois da tabela.

**Tarefa** | **Detalhes** | **Permissões**
--- | --- | ---
**Criar um projeto de Migrações para Azure** | Um projeto das Migrações para Azure fornece um local central para orquestrar e gerenciar avaliações e migrações com as ferramentas das Migrações para Azure, ferramentas da Microsoft e ofertas de terceiros. | Sua conta do Azure precisa de permissões de Colaborador ou de Proprietário no grupo de recursos no qual o projeto reside.
**Registrar dispositivo** | As Migrações para Azure usam um dispositivo leve de Migrações para Azure para descobrir e avaliar VMs do Hyper-V. [Saiba mais](migrate-appliance-architecture.md#appliance-registration). | Para registrar o dispositivo, sua conta do Azure precisa de permissões de Colaborador ou de Proprietário na assinatura do Azure.
**Criar um aplicativo do Azure AD** | Ao registrar o dispositivo, o recurso Migrações para Azure cria um aplicativo do Azure AD (Azure Active Directory) que é usado para a comunicação entre os agentes em execução no dispositivo e as Migrações para Azure. | Sua conta do Azure precisa de permissões para criar aplicativos do Azure AD.
**Criar uma máquina virtual** | Você precisa de permissões para criar uma VM no grupo de recursos e na rede virtual e para gravar em um disco gerenciado do Azure. | Sua conta do Azure precisa da função Colaborador da Máquina Virtual.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

Verifique se você tem permissões para criar um projeto de Migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Atribuir permissões para criar aplicativos do Azure AD

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

### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta do Azure

Atribua a função de Colaborador da Máquina Virtual à conta para que você tenha permissões para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar em um disco gerenciado do Azure. 


### <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

[Configure uma rede do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Os computadores locais são replicados para discos gerenciados do Azure. Quando você faz failover para o Azure para migração, as VMs do Azure são criadas usando esses discos gerenciados e ingressadas na rede do Azure que você configura.


## <a name="prepare-for-assessment"></a>Preparar-se para avaliação

Você pode preparar o Hyper-V para avaliação da VM manualmente ou usando um script de configuração. As etapas de preparação são listadas a seguir. Se você se preparar com um script, elas serão configuradas automaticamente.

**Step** | **Script** | **Manual**
--- | --- | ---
**Verificar os requisitos do host Hyper-V** | O script verifica se o host está executando uma versão compatível do Hyper-V e a função do Hyper-V.<br/><br/> Habilita o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no host (necessárias para a coleta de metadados). | Verifique os [requisitos do host do Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) para avaliação do servidor.<br/><br/> Verifique se as [portas necessárias](migrate-support-matrix-hyper-v.md#port-access) estão abertas nos hosts do Hyper-V.
**Verificar a versão do PowerShell** | Verifica se você está executando o script em uma versão do PowerShell compatível. | Verifique se você está executando o PowerShell versão 4.0 ou posterior no host Hyper-V.
**Criar uma conta** | Verifica se você (o usuário que está executando o script) tem privilégios administrativos no host Hyper-V.<br/><br/>  Permite que você crie uma conta de usuário local (não administrador) que o serviço de Migrações para Azure usará para se comunicar com o host do Hyper-V. Essa conta de usuário é adicionada a esses grupos no host:<br/><br/> – Usuários do Gerenciamento Remoto<br/><br/> – Administradores do Hyper-V<br/><br/>– Usuários do Monitor de Desempenho | Configure uma conta de usuário local ou de domínio com permissões de administrador nos hosts/cluster do Hyper-V.<br/><br/> – Você precisa de uma única conta para todos os hosts e clusters que deseja incluir na descoberta.<br/><br/> – A conta pode ser local ou de domínio. Recomendamos que ela tenha permissões de Administrador nos hosts ou clusters do Hyper-V.<br/><br/> Como alternativa, se você não quiser atribuir permissões de administrador, as seguintes permissões serão necessárias: Usuários de Gerenciamento Remoto, Administradores do Hyper-V e Usuários do Monitor de Desempenho.
**Habilitar a Comunicação Remota do PowerShell** | Habilitar a comunicação remota do PowerShell no host para que o dispositivo de Migrações para Azure possa executar comandos do PowerShell no host, em uma conexão do WinRM.| Para configurar, em cada host, abra um console do PowerShell como administrador e execute este comando:<br/><br/>``` Enable-PSRemoting -force ```
**Configurar os Serviços de Integração do Hyper-V** | Verifica se os Integration Services do Hyper-V estão habilitados em todas as VMs gerenciadas pelo host. |  [Habilite os Serviços de Integração do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM.<br/><br/> Se você estiver executando o Windows Server 2003, [siga estas instruções](prepare-windows-server-2003-migration.md).
**Delegar credenciais se os discos de VM estiverem em compartilhamentos SMB remotos** | O script delega credenciais. | [Habilitar o CredSSP](#enable-credssp-to-delegate-credentials) para delegar credenciais.

### <a name="run-the-script"></a>Executar o script

Crie o script da seguinte maneira:

1. Verifique se você tem o PowerShell versão 4.0 ou posterior instalado no host Hyper-V.
2. Baixe o script do [Centro de Download da Microsoft](https://aka.ms/migrate/script/hyperv). O script é assinado criptograficamente pela Microsoft.
3. Valide a integridade do script usando arquivos de hash MD5 ou SHA256. Os valores de hashtag são indicados abaixo. Execute o seguinte comando para gerar o hash para o script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemplo de uso:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Depois de validar a integridade do script, execute o script em cada host Hyper-V com este comando do PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valores de hashtag

Os valores de hash são:

| **Hash** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Habilitar o CredSSP para delegar credenciais

Se o host tiver VM com discos localizados em compartilhamentos SMB, complete esta etapa no host.

- Você pode executar esse comando remotamente em todos os hosts do Hyper-V.
- Se você adicionar novos nós de host a um cluster, eles serão adicionados automaticamente para descoberta, mas você precisará habilitar o CredSSP manualmente nos novos nós, se necessário.

Habilite da seguinte maneira:

1. Identifique os hosts do Hyper-V que executam VMs do Hyper-V com discos em compartilhamentos SMB.
2. Execute o seguinte comando em cada host do Hyper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Ao configurar o dispositivo, você conclui a configuração do CredSSP [habilitando-o no dispositivo](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isso é descrito no próximo tutorial desta série.


## <a name="prepare-for-appliance-deployment"></a>Preparar para implantação do dispositivo

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-appliance.md#appliance---hyper-v) os requisitos do dispositivo.
2. Examine as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls). Se estiver usando um firewall ou proxy baseado em URL, certifique-se de que ele permite o acesso às URLs necessárias.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Examine](migrate-appliance.md#collected-data---hyper-v) os requisitos de acesso da porta para o dispositivo.


## <a name="prepare-for-hyper-v-migration"></a>Preparar para a migração do Hyper-V

1. [Examine](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) os requisitos de host do Hyper-V para migração e as URLs do Azure para as quais os hosts e clusters do Hyper-V precisam de acesso para migração da VM.
2. [Examine](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) os requisitos para VMs do Hyper-V que você deseja migrar para o Azure.
3. Há algumas alterações necessárias nas VMs antes de migrá-las para o Azure.
    - É importante fazer essas alterações antes de iniciar a migração. Se você migrar a VM antes de fazer a alteração, ela não poderá ser inicializada no Azure.
    - Examine as alterações para [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines) que você precisa fazer.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurou permissões de conta do Azure.
> * Preparou VMs do Hyper-V para avaliação e migração.
> * Preparou para a implantação do dispositivo das Migrações para Azure.

Prossiga para o próximo tutorial para criar um projeto das Migrações para Azure, implantar o dispositivo e descobrir e avaliar as VMs do Hyper-V para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs do Hyper-V](./tutorial-assess-hyper-v.md)
