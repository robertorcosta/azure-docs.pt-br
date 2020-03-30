---
title: Gerenciar servidores VMware vCenter na recuperação do site do Azure
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter para recuperação de desastres de VMware vMs para o Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257258"
---
# <a name="manage-vmware-vcenter-server"></a>Gerenciar o VMware vCenter Server

Este artigo resume as ações de gerenciamento em um VMware vCenter Server no [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verifique pré-requisitos para o vCenter Server

Os pré-requisitos para servidores vCenter e VMs durante a recuperação de desastres de VMware VMs para Azure estão listados na [matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

Quando você configura a recuperação de desastres para VMMs VMware no local, a Recuperação do Site precisa de acesso ao host vCenter Server/vSphere. O servidor de processo de recuperação de site pode, então, descobrir automaticamente VMs e recomtá-las conforme necessário. Por padrão, o servidor de processo é executado no servidor de configuração de recuperação do site. Adicione uma conta para que o servidor de configuração se conecte ao host vCenter Server/vSphere da seguinte forma:

1. Entre no servidor de configuração.
1. Abra a ferramenta do servidor de configuração _(cspsconfigtool.exe)_ usando o atalho desktop.
1. Na guia **Gerenciar conta,** clique **em Adicionar conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os detalhes da conta e clique em **OK** para adicioná-la. A conta deve ter os privilégios resumidos na tabela de permissões de conta.

   > [!NOTE]
   > Leva cerca de 15 minutos para sincronizar as informações da conta com a Recuperação do Site.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Detecção/migração de VM (sem failback)** | Pelo menos uma conta de usuário somente leitura. | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|
|**Replicação/failover** | Pelo menos uma conta de usuário somente leitura. | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **No access** com o **Objeto Propagação aos** objetos da criança (hosts vSphere, datastores, máquinas virtuais e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo VMware. | Objeto de Data Center –> Propagar para o Objeto Filho, role=AzureSiteRecoveryRole<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, máquinas virtuais e redes).|

## <a name="add-vmware-server-to-the-vault"></a>Adicionar um servidor VMware ao cofre

Quando você configura a recuperação de desastres para VMS VMware no local, você adiciona o host vCenter Server/vSphere no qual você está descobrindo VMs no cofre de recuperação do site, da seguinte forma:

1. No cofre >**Configuração de configuração de** **configuração** > de recuperação do site, abra o servidor de configuração.
1. Na página **Detalhes,** clique **em vCenter**.
1. Em **Adicionar vCenter,** especifique um nome amigável para o host vSphere ou o servidor vCenter.
1. Especifique o endereço IP ou FQDN do servidor.
1. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar solicitações em uma porta diferente.
1. Selecione a conta usada para se conectar ao servidor VMware vCenter ou vSphere ESXi. Em seguida, clique em **OK**.

## <a name="modify-credentials"></a>Modificar as credenciais

Se necessário, você pode modificar as credenciais usadas para se conectar ao host vCenter Server/vSphere da seguinte forma:

1. Entre no servidor de configuração.
1. Abra a ferramenta do servidor de configuração _(cspsconfigtool.exe)_ usando o atalho desktop.
1. Na guia **Gerenciar Conta**, clique em **Adicionar Conta**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os novos detalhes da conta e clique em **OK**. A conta precisa das permissões listadas na tabela [de permissões](#account-permissions) da conta.
1. No cofre > > **Configuração de configuração de configuração de**recuperação do **site,** abra o servidor de configuração.
1. Em **Detalhes,** clique **em Atualizar servidor**.
1. Após o término do trabalho do Servidor de atualização, selecione o servidor vCenter.
1. Em **Resumo,** selecione a conta recém-adicionada na **conta de host do servidor vCenter/vSphere**e clique em **Salvar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Exclua um servidor vCenter

1. No cofre > > **Configuração de configuração de configuração de**recuperação do **site,** abra o servidor de configuração.
1. Na página **Detalhes**, selecione o servidor vCenter.
1. Clique no botão **Excluir**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modifique o endereço IP e a porta

Você pode modificar o endereço IP do vCenter Server ou as portas usadas para comunicação entre o servidor e a Recuperação do Site. Por padrão, a Recuperação do Site acessa as informações do host vCenter Server/vSphere através da porta 443.

1. No cofre > Servidores > **de configuração de configuração** **de recuperação**de site, clique no servidor de configurações ao qual o servidor vCenter é adicionado.
1. Em **servidores vCenter,** clique no servidor vCenter que deseja modificar.
1. Em **Resumo,** atualize o endereço IP e a porta e salve as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Para que as alterações se tornem efetivas, aguarde 15 minutos ou [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migre todas as VMs para um novo servidor

Se você quiser migrar todas as VMs para usar um novo vCenter Server, você só precisa atualizar o endereço IP atribuído ao vCenter Server. Não adicione outra conta VMware, já que isso pode levar a entradas duplicadas. Atualize o endereço da seguinte forma:

1. No cofre > Servidores > **de configuração de configuração** **de recuperação**de site, clique no servidor de configurações ao qual o servidor vCenter é adicionado.
1. Na seção **servidores vCenter,** clique no vCenter Server do que deseja migrar.
1. Em **Resumo,** atualize o endereço IP para o do novo vCenter Server e salve as alterações.
1. Assim que o endereço IP é atualizado, a Recuperação do Site começa a receber informações de detecção de VM do novo vCenter Server. Isso não afeta as atividades contínuas de replicação.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migre algumas VMs para um novo servidor

Se você quiser migrar apenas algumas das Suas VMs replicadas para um novo vCenter Server, faça o seguinte:

1. [Adicione](#add-vmware-server-to-the-vault) o novo vCenter Server ao servidor de configuração.
1. [Desativar a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para VMs que serão passar para o novo servidor.
1. No VMware, migre as VMs para o novo vCenter Server.
1. [Habilite](vmware-azure-tutorial.md#enable-replication) a replicação das VMs migradas novamente, selecionando o novo vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>Migre a maioria das VMs para um novo servidor

Se o número de VMs que você deseja migrar para um novo vCenter Server for maior do que o número de VMs que permanecerão no vCenter Server original, faça o seguinte:

1. [Atualize o endereço IP](#modify-the-ip-address-and-port) atribuído ao servidor vCenter nas configurações do servidor de configuração, para o endereço do novo servidor vCenter.
1. [Desativar a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para as poucas VMs que permanecem no servidor antigo.
1. [Adicione o antigo vCenter Server](#add-vmware-server-to-the-vault) e seu endereço IP ao servidor de configuração.
1. [Reativar a replicação](vmware-azure-tutorial.md#enable-replication) das VMs que permanecem no servidor antigo.

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema, consulte [Falhas de detecção do servidor de problemas vCenter](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
