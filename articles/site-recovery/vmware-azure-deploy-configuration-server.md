---
title: Implantar o servidor de configuração na Recuperação do Site do Azure
description: Este artigo descreve como implantar um servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257375"
---
# <a name="deploy-a-configuration-server"></a>Implante um servidor de configuração

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados. Este artigo orienta você pelas etapas necessárias para implantar o servidor de configuração quando você estiver replicando VMs do VMware no Azure. Se você precisar configurar um servidor de configuração para replicação física do servidor, consulte [Configurar o servidor de configuração para recuperação de desastres de servidores físicos no Azure](physical-azure-set-up-source.md).

> [!TIP]
> Para saber mais sobre a função de um servidor de configuração como parte da arquitetura Azure Site Recovery, consulte [a arquitetura de recuperação de desastres do VMware to Azure](vmware-azure-architecture.md).

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Implantar um servidor de configuração através de um modelo OVA

O servidor de configuração deve ser configurado como uma VMM VMl altamente disponível com certos requisitos mínimos de hardware e dimensionamento. Para uma implantação conveniente e fácil, o Site Recovery fornece um modelo DE OVA (Open Virtualization Application, aplicativo de virtualização aberto) para configurar o servidor de configuração que cumpre todos os requisitos exigidos aqui.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos mínimos de hardware para um servidor de configuração são resumidos nas seguintes seções.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Requisitos de permissão do Azure Active Directory

Você deve ter um usuário com uma das seguintes permissões definidas no Azure Active Directory (Azure AD) para registrar o servidor de configuração com os serviços de recuperação do Site do Azure.

1. O usuário deve ter uma função de desenvolvedor de aplicativos para criar um aplicativo.
    - Para verificar, faça login no portal Azure.</br>
    - Vá para > **funções e administradores**ativos **do Azure**.</br>
    - Verifique se a função de desenvolvedor de aplicativos é atribuída ao usuário. Caso não, use um usuário com essa permissão ou entre em contato com um [administrador para ativar a permissão](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Se a função de desenvolvedor de aplicativos não puder ser atribuída, certifique-se de que o **sinalizador de aplicativos pode registrar aplicativos** definido como **verdadeiro** para que o usuário crie uma identidade. Para habilitar essas permissões:
    - Entre no portal do Azure.
    - Vá para as > **configurações do usuário**do **diretório ativo do Azure**.
    - Em **registros de aplicativos,** **os usuários podem registrar aplicativos,** selecionar **Sim**.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Os Serviços ativos da Federação de *Diretórios não são suportados*. Use uma conta gerenciada através [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>Baixe o modelo

1. No cofre, vá para **Preparar Fonte de Infra-Estrutura** > **Source**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo OVA para o servidor de configuração.

   > [!TIP]
   >Você também pode baixar a versão mais recente do modelo do servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Após esse período, você deve obter uma licença.

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no VMware vCenter Server ou no host vSphere ESXi, usando o VMware vSphere Client.
2. No **menu Arquivo,** **selecione Implantar modelo OVF** para iniciar o **assistente Implantar modelo OVF.**

     ![Implantar modelo OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**. O uso da opção de provisionamento fino pode afetar o desempenho do servidor de configuração.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **'Ligar após a** > **implantação'.**
    * Para adicionar uma interface de rede adicional, desmarque **Ligar após a implantação** e, em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.

> [!IMPORTANT]
> Não altere as configurações de recursos, como a restrição de memória, núcleos e CPU, ou modifique ou exclua serviços ou arquivos instalados no servidor de configuração após a implantação. Esses tipos de alterações afetam o registro do servidor de configuração com os serviços do Azure e o desempenho do servidor de configuração.

## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

> [!NOTE]
> Dois NICs são necessários se você planeja reter os endereços IP das máquinas de origem no failover e deseja voltar ao local mais tarde. Uma NIC está conectada a máquinas de origem, e a outra NIC é usada para conectividade Azure.

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Next**.
3. Selecione um tipo de adaptador e uma rede.
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Em seguida, **selecione Next** > **Finish** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registre o servidor de configuração com os serviços do Azure Site Recovery

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. A primeira vez que você faz login, em poucos segundos a ferramenta Azure Site Recovery Configuration é iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Next**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure.</br>
    a. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.</br>
    b. Certifique-se de que a conta de usuário escolhida tenha permissão para criar um aplicativo no Azure. Para habilitar as permissões necessárias, siga as diretrizes na seção Requisitos de [permissão do Azure Active Directory](#azure-active-directory-permission-requirements).
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração começa automaticamente em poucos segundos.

### <a name="configure-settings"></a>Definir configurações

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade**. A partir das caixas gotas, primeiro selecione a NIC que o servidor de processo incorporado usa para detecção e empurrar a instalação do serviço de mobilidade nas máquinas de origem. Em seguida, selecione a NIC que o servidor de configuração usa para conectividade com o Azure. Selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida. Não altere o endereço IP de um servidor de configuração. Certifique-se de que o IP atribuído ao servidor de configuração é um IP estático e não um IP DHCP.
2. No **cofre Select Recovery Services,** faça login no Microsoft Azure com as credenciais usadas na etapa 6 do Registre o servidor de [configuração com os serviços de recuperação do Site do Azure](#register-the-configuration-server-with-azure-site-recovery-services).
3. Após o login, selecione sua assinatura do Azure e o grupo de recursos e cofre relevantes.

    > [!NOTE]
    > Após o registro, não há flexibilidade para alterar o cofre dos serviços de recuperação.
    > Alterar um cofre de serviços de recuperação requer a dissociação do servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas o servidor de configuração é interrompida. Para saber mais, consulte [Gerenciar o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. Na **instalação de software de terceiros:**

    |Cenário   |Etapas a serem executadas  |
    |---------|---------|
    |Posso baixar e instalar o MySQL manualmente?     |  Sim. Baixe o aplicativo MySQL, coloque-o na pasta **C:\Temp\ASRSetup**e instale manualmente. Depois de aceitar os termos e selecionar **Baixar e instalar,** o portal diz *já instalado*. Você pode prosseguir para a próxima etapa.       |
    |Posso evitar o download do MySQL online?     |   Sim. Coloque o aplicativo instalador do MySQL na pasta **C:\Temp\ASRSetup**. Aceite os termos, selecione **Baixar e instalar,** e o portal usa o instalador que você adicionou para instalar o aplicativo. Após o término da instalação, siga para a próxima etapa.    |
    |Quero baixar e instalar o MySQL através do Azure Site Recovery.    |  Aceite o contrato de licença e selecione **Baixar e instalar**. Após o término da instalação, siga para a próxima etapa.       |

5. Na **configuração do aparelho Validar,** os pré-requisitos são verificados antes de continuar.
6. No **servidor Configure vCenter Server/vSphere ESXi,** digite o endereço FQDN ou IP do servidor vCenter ou o host vSphere, onde as VMs que você deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** > **continuar**. As credenciais digitadas aqui são salvas localmente.
8. Na **Configuração de credenciais de máquina virtual,** digite o nome de usuário e a senha das máquinas virtuais para instalar automaticamente o serviço de mobilidade durante a replicação. Para computadores **Windows**, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o **Linux**, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registro.
10. Após o término do registro, abra o portal do Azure e verifique se o servidor de configuração e o servidor VMware estão listados **nos** > **servidores de configuração de configuração****de recuperação** > do Vault**Manage** > Site .

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Para atualizar o servidor de configuração para a versão mais recente, consulte [Gerenciar o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes de recuperação do site, consulte [atualizações de serviço na Recuperação do Site](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Gerenciar o servidor de configuração

Para evitar interrupções na replicação contínua, certifique-se de que o endereço IP do servidor de configuração não seja alterado depois que o servidor de configuração estiver registrado em um cofre. Para saber mais sobre tarefas comuns de gerenciamento de servidor de configuração, consulte [Gerenciar o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md).

## <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

Consulte nosso [artigo de solução de problemas](vmware-azure-troubleshoot-configuration-server.md) para resolver problemas de implantação & conectividade.

## <a name="faqs"></a>Perguntas frequentes

* Quanto tempo a licença é fornecida em um servidor de configuração implantado através do OVF válido? O que acontece se eu não reativar a licença?

    A licença fornecida com um modelo OVA é uma licença de avaliação válida por 180 dias. Antes de expirar, você precisa ativar a licença. Caso contrário, pode resultar em desligamento frequente do servidor de configuração e causar um obstáculo às atividades de replicação. Para obter mais informações, consulte [Gerenciar o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#update-windows-license).

* Posso usar a VM onde o servidor de configuração está instalado para diferentes propósitos?

    Não. Use o VM para o único propósito do servidor de configuração. Certifique-se de seguir todas as especificações mencionadas nos [Pré-requisitos](#prerequisites) para uma gestão eficiente da recuperação de desastres.
* Posso trocar o cofre já registrado no servidor de configuração por um cofre recém-criado?

    Não. Depois que um cofre é registrado no servidor de configuração, ele não pode ser alterado.
* Posso usar o mesmo servidor de configuração para proteger máquinas físicas e virtuais?

    Sim. O mesmo servidor de configuração pode ser usado para replicar máquinas físicas e virtuais. No entanto, a máquina física pode ser falhada apenas para uma VMware VMware.
* Qual é o propósito de um servidor de configuração e onde ele é usado?

    Para saber mais sobre o servidor de configuração e suas funcionalidades, consulte [a arquitetura de replicação VMware to Azure](vmware-azure-architecture.md).
* Onde posso encontrar a versão mais recente do servidor de configuração?

    Para obter as etapas para atualizar o servidor de configuração por meio do portal, veja [Atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Para obter instruções sobre como atualizar todos os componentes de recuperação do site, consulte [atualizações de serviço na Recuperação do Site](https://aka.ms/asr_how_to_upgrade).
* Em que local posso baixar a frase secreta para o servidor de configuração?

    Para baixar a senha, consulte [Gerenciar o servidor de configuração para recuperação de desastres VMware VM](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).
* Posso alterar a frase secreta?

    Não. Não altere a senha do servidor de configuração. Uma mudança na senha quebra a replicação de máquinas protegidas e leva a um estado crítico de saúde.
* Em que local posso baixar as chaves de registro do cofre?

    No **Cofre de Serviços de Recuperação,** selecione **Gerenciar** > servidores de configuração**de configuração** > de recuperação**de site**. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.
* Posso clonar um servidor de configuração existente e usá-lo para orquestração de replicação?

    Não. O uso de um componente de servidor de configuração clonada não é suportado. A clonagem de um servidor de processo de saída de escala também é um cenário sem suporte. A clonagem de componentes de recuperação do site afeta as replicação contínuas.

* Posso alterar o IP de um servidor de configuração?

    Não. Não altere o endereço IP de um servidor de configuração. Certifique-se de que todos os IPs atribuídos ao servidor de configuração são IPs estáticos e não IPs DHCP.
* Posso configurar um servidor de configuração no Azure?

    Configure um servidor de configuração em um ambiente local com uma linha de visão direta com o v-Center e para minimizar as latências de transferência de dados. É possível fazer backups agendados do servidor de configuração para [fins de failback](vmware-azure-manage-configuration-server.md#failback-requirements).

* Posso alterar o driver de cache em um servidor de configuração ou servidor de processo de saída de escala?

    Não, o driver de cache não pode ser alterado quando a configuração estiver concluída.

Para obter mais perguntas frequentes sobre servidores de configuração, consulte [Perguntas comuns do servidor de configuração](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
