---
title: Gerenciar servidores VMware vCenter no Azure Site Recovery
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter para recuperação de desastres de VMs do VMware para o Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083968"
---
# <a name="manage-vmware-vcenter-server"></a>Gerenciar o servidor VMware vCenter

Este artigo discute as várias operações do Site Recovery que podem ser executadas em um VMware vCenter. Verifique os [pré-requisitos](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

A Site Recovery precisa de acesso ao VMware para que o servidor de processo descubra automaticamente as máquinas virtuais e para failover e failback de máquinas virtuais. Crie uma conta de acesso da seguinte maneira:

1. Faça logon na máquina do servidor de configuração.
2. Abra e inicie o cspsconfigtool.exe usando o atalho da Área de Trabalho.
3. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Forneça os detalhes da conta e clique em **OK** para adicioná-la.  A conta deve ter os privilégios resumidos na tabela a seguir. 

Demora cerca de 15 minutos para que as informações de conta sejam sincronizadas com o serviço do Site Recovery.

### <a name="account-permissions"></a>Permissões da conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta automática/migrar (sem failback)** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|
|**Replicação/failover** | Você precisa de pelo menos um usuário somente leitura| Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Sem acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e atribua a função a um usuário ou grupo do VMware | Objeto de Data Center –> Propagar para o Objeto Filho, role=AzureSiteRecoveryRole<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído ao nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adicionar um servidor VMware ao cofre

1. No portal do Azure, abra seu cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
2. Na página **Detalhes**, clique em **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificar as credenciais

Modifique as credenciais usadas para se conectar ao servidor vCenter ou ao host ESXi da seguinte maneira:

1. Faça logon no servidor de configuração e inicie o cspsconfigtool.exe por meio da área de trabalho.
2. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da nova conta e clique em **OK** para adicioná-la. A conta deve ter os privilégios listados [acima](#account-permissions).
4. No portal do Azure, abra o cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
5. Na página **Detalhes**, clique em **Atualizar Servidor**.
6. Após a conclusão do trabalho Atualizar Servidor, selecione o vCenter Server para abrir a página **Resumo** do vCenter.
7. Selecione a conta recém-adicionada no campo **Conta do host do servidor vCenter/vSphere** e clique em **Salvar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Excluir um servidor vCenter

1. No portal do Azure, abra seu cofre > **Infraestrutura do Site Recovery** > **Servidores de Configuração** e abra o servidor de configuração.
2. Na página **Detalhes**, selecione o servidor vCenter.
3. Clique no botão **Excluir**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modificar o endereço IP e a porta do vCenter

1. Entre no Portal do Azure.
2. Navegue até o **cofre dos serviços de recuperação** > **site Recovery infraestrutura** > **servidores de configuração**.
3. Clique no servidor de configuração ao qual o vCenter está atribuído.
4. Na seção **servidores do vCenter** , clique no vCenter que você deseja modificar.
5. Na página Resumo do vCenter, atualize o endereço IP e a porta do vCenter nos respectivos campos e salve as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Para que as alterações se tornem efetivas, aguarde 15 minutos ou [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrar todas as máquinas virtuais protegidas para um novo vCenter

Para migrar todas as máquinas virtuais para o novo vCenter, não adicione outra conta do vCenter. Isso pode levar a entradas duplicadas. Basta atualizar o endereço IP do novo vCenter:

1. Entre no Portal do Azure.
2. Navegue até o **cofre dos serviços de recuperação** > **site Recovery infraestrutura** > **servidores de configuração**.
3. Clique no servidor de configuração ao qual o vCenter antigo está atribuído.
4. Na seção **servidores vCenter** , clique no vCenter do qual você está planejando migrar.
5. Na página Resumo do vCenter, atualize o endereço IP do novo vCenter no campo **vCenter Server/vSphere nome do host ou endereço IP**. Salve suas alterações.

Assim que o endereço IP for atualizado, Site Recovery componentes começarão a receber informações de descoberta de máquinas virtuais do novo vCenter. Isso não afetará as atividades de replicação em andamento.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrar algumas máquinas virtuais protegidas para um novo vCenter

> [!NOTE]
> Esta seção é aplicável somente quando você está migrando algumas de suas máquinas virtuais protegidas para um novo vCenter. Se você quiser proteger um novo conjunto de máquinas virtuais de um novo vCenter, [Adicione novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault) e comece com **[habilitar a proteção](vmware-azure-tutorial.md#enable-replication)** .

Para mover algumas máquinas virtuais para um novo vCenter:

1. [Adicione os novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault).
2. [Desabilite a replicação das máquinas virtuais](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) que você pretende migrar.
3. Conclua a migração de máquinas virtuais selecionadas para o novo vCenter.
4. Agora, proteja as máquinas virtuais migradas [selecionando o novo vCenter ao habilitar a proteção](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Se o número de máquinas virtuais que estão sendo migradas for **maior** que o número de máquinas virtuais retidas no vCenter antigo, atualize o endereço IP do novo vCenter usando as instruções fornecidas aqui. Para as poucas máquinas virtuais que são mantidas no vCenter antigo, [desabilite a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [Adicione novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault)e comece a **[habilitar a proteção](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. Se as máquinas virtuais protegidas forem movidas de um host ESXi para outro, isso afetará a replicação?

    Não, isso não afetará a replicação em andamento. No entanto, [certifique-se de implantar o servidor de destino mestre com privilégios suficientes](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Quais são os números de porta usados para a comunicação entre o vCenter e outros componentes de Site Recovery?

    A porta padrão é 443. O servidor de configuração acessará as informações de host do vCenter/vSphere por meio desta porta. Se você quiser atualizar essas informações, clique [aqui](#modify-the-vcenter-ip-address-and-port).
