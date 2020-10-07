---
title: Criar ativos técnicos para uma oferta de máquina virtual do Azure Marketplace
description: Saiba como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a83532e2dd6fc8e83206a3b4a055170b40d131fd
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803510"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Criar ativos técnicos para uma oferta de máquina virtual do Azure Marketplace

Ao publicar suas imagens de máquina virtual (VM) no Azure Marketplace, a equipe do Azure valida a imagem da VM para garantir sua inicialização, segurança e compatibilidade do Azure. Se qualquer um dos testes de alta qualidade falhar, a publicação falhará com uma mensagem que contém o erro e [as etapas de retificação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)possíveis.

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace. Uma VM contém dois componentes: o VHD (disco rígido virtual) do sistema operacional e os VHDs dos discos de dados associados opcionais:

- **VHD do sistema operacional**: contém o sistema operacional e a solução que é implantada com sua oferta. O processo de preparação do VHD difere dependendo de se trata-se de uma VM baseada em Linux, baseada em Windows ou personalizada.

- **VHDs de disco de dados**: armazenamento dedicado e persistente para uma VM. Não use a solução o VHD do sistema operacional (por exemplo, a unidade C:) para armazenar informações persistentes.

Uma imagem de VM contém um disco de sistema operacional e até 16 discos de dados. Use um VHD por disco de dados, mesmo se o disco estiver em branco.

> [!NOTE]
> Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

> [!IMPORTANT]
> Cada imagem de VM em um plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta. Além do domínio da solução, a equipe de engenharia deverá ter conhecimento das seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático de [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Ferramentas sugeridas opcionais

Considere usar um dos seguintes ambientes de script para ajudar a gerenciar VMs e VHDs:

- [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem de VM usando uma base aprovada

Para criar seus ativos técnicos da máquina virtual usando uma imagem criada em sua própria empresa, consulte [criar uma imagem de VM usando uma base aprovada](#create-a-vm-image-using-an-approved-base) abaixo.

Essa seção descreve os vários aspectos do uso de uma base aprovada, como o uso do protocolo RDP, a seleção de um tamanho para a VM, a instalação das atualizações mais recentes do Windows e a generalização da imagem do VHD.

Siga as orientações neste artigo para usar o Azure para criar uma VM que contém um sistema operacional previamente configurado e endossado. Se isso não for compatível com sua solução, será possível criar e configurar uma VM local usando um sistema operacional aprovado. Você pode configurá-lo e prepará-lo para upload, conforme descrito em [ Preparar um VHDX ou VHDX do Windows para fazer o upload para o Azure ](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Selecionar uma imagem de base aprovada

Selecione o sistema operacional Windows ou Linux como sua base.

O VHD do sistema operacional para a imagem VM deve ser baseado em uma imagem de base aprovada do Azure que contém o Windows Server ou o SQL Server.

Quando você envia uma solicitação para republicar a imagem com atualizações, o caso de teste de verificação de número de peça pode falhar. Nessa instância, a imagem não será aprovada.

Essa falha ocorrerá quando você usou uma imagem base que pertence a outro Publicador e atualizou a imagem. Nesse caso, você não terá permissão para publicar sua imagem.

Para corrigir esse problema, recupere a imagem mais recente do Azure Marketplace e faça alterações nessa imagem. Consulte o seguinte para exibir as imagens base aprovadas nas quais você pode pesquisar sua imagem:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

O Azure oferece uma variedade de distribuições aprovadas do Linux. Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Criar VM no portal do Azure

Siga estas etapas para criar a imagem de VM de base no [portal do Azure](https://ms.portal.azure.com/):

1. Entre no [portal do Azure](https://ms.portal.azure.com/) com a conta Microsoft associada à assinatura do Azure que você deseja usar para publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça seu **Nome do grupo de recursos**, **Assinatura** e **Local do grupo de recursos**. Para obter detalhes, confira [Gerenciar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

3. Selecione **máquinas virtuais** no painel de navegação à esquerda para exibir a página de detalhes de máquinas virtuais.
4. Selecione **+ Adicionar** para abrir **Criar uma experiência de máquina virtual**.
5. Selecione a imagem na lista suspensa ou selecione **procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis. Exemplo:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

6. Selecione o tamanho da VM para implantar usando as seguintes recomendações:
    1. Se você pretende desenvolver o VHD localmente, o tamanho não importa. Considere o uso de uma das VMs menores.
    2. Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

7. Na seção **Discos**, expanda a seção **Avançado** e defina a opção **Usar discos gerenciados** como **Não**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

8. Forneça os outros detalhes necessários para criar a VM.
9. Selecione **Examinar + criar** para examinar suas opções. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

O Azure inicia o provisionamento da máquina virtual especificada. Você pode acompanhar seu progresso selecionando a guia **Máquinas Virtuais** à esquerda. Depois de criado, o status mudará para **Em execução**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Criar uma VM de geração 2 no portal do Azure

Crie uma VM de geração 2 (Gen2) no portal do Azure.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
2. Selecione **Criar um recurso**.
3. Selecione **ver tudo** no Azure Marketplace à esquerda.
4. Selecione uma imagem que dê suporte a Gen2.
5. Selecione **Criar**.
6. Na guia **Avançado**, na seção **Geração de VM**, selecione a opção **Gen 2**.
7. Na guia **Noções básicas** em **Detalhes da instância** vá para **Tamanho** e abra a folha **Selecionar um tamanho de VM**.
8. Selecione um tamanho recomendado de VM e tamanho de [Gen 2 com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) .
9. Percorra o [Fluxo de criação do portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) para concluir a criação da VM.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

## <a name="connect-to-your-azure-vm"></a>Conectar-se à VM do Azure

Esta seção explica como se conectar e entrar na VM que você criou no Azure. Depois de você se conectar com êxito, poderá trabalhar com a VM como se tivesse efetuado logon localmente em seu servidor de host.

### <a name="connect-to-a-windows-based-vm"></a>Conectar em uma VM baseada em Windows

Você usa o cliente de Área de Trabalho Remota para se conectar à VM com base em Windows hospedada no Azure. A maioria das versões do Windows contêm suporte para o protocolo de área de trabalho remota (RDP). Para outros sistemas operacionais, você pode encontrar mais informações sobre os clientes nos [clientes de Área de Trabalho Remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Este artigo fornece detalhes sobre como usar o suporte interno do Windows RDP para se conectar à sua VM: [como se conectar e fazer logon em uma máquina virtual do Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Você pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O arquivo .rdp é de um editor desconhecido" ou "Suas credenciais de usuário não podem ser verificadas". É seguro ignorar esses avisos.

### <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma máquina virtual baseada em Linux

Para conectar-se a VM com base em Linux, é necessário um cliente do protocolo (SSH) de um shell seguro. As etapas a seguir usam a versão gratuita do terminal [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Pesquise por Máquinas virtuais e selecione essa opção.
3. Selecione a VM a qual você deseja se conectar.
4. Inicie a VM se ainda não estiver sendo executada.
5. Selecione o nome da VM para abrir sua página de Visão geral.
6. Observe o endereço IP público e o nome DNS da sua VM (se esses valores não estiverem definidos, você deverá [criar um adaptador de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Abra o aplicativo PuTTY.
8. Na caixa de diálogo de configuração do PuTTY, insira o endereço IP ou nome DNS da sua VM.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

9. Selecione **Abrir** para abrir um terminal do PuTTY.
10. Quando você for solicitado, insira o nome da conta e a senha da sua conta de VM do Linux.

## <a name="configure-the-virtual-machine"></a>Configurar a máquina virtual

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionar os VHDs

As regras a seguir são para limitações no tamanho do disco do sistema operacional. Ao enviar qualquer solicitação, verifique se o tamanho do disco do sistema operacional está dentro da limitação do Linux ou do Windows.

| Sistema operacional | Tamanho de VHD recomendado |
| --- | --- |
| Linux | 30 a 1023 GB |
| Windows | 30 a 250 GB |

Como as VMs permitem o acesso ao sistema operacional subjacente, verifique se o tamanho do VHD é grande o suficiente para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco entre 30 e 50 &nbsp; GB.

| Tamanho do VHD | Tamanho real de ocupado | Solução |
| --- | --- | --- |
| >500 TB | n/a | Contate a equipe de suporte para obter uma aprovação de exceção. |
| 250-500 TB | >200 GB diferentes do tamanho do blob | Contate a equipe de suporte para obter uma aprovação de exceção. |

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

As imagens de base das VMs do sistema operacional devem conter as atualizações mais recentes até a data publicada. Antes de publicar o VHD do sistema operacional que você criou, assegure-se de atualizar o sistema operacional e todos os serviços instalados com os patches de segurança e manutenção mais recentes.

- Para Windows Server, execute o comando Verificar se há atualizações.
- Para distribuições Linux, as atualizações geralmente são baixadas e instaladas por meio de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Gerenciador de Atualização](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SO.

#### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para as imagens de solução no Azure Marketplace. Para obter uma lista de verificação de configurações e procedimentos de segurança, consulte [recomendações de segurança para imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem de VM.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se precisar de configuração adicional, use uma tarefa agendada que seja executada na inicialização para fazer alterações finais na VM após sua implantação. Considere também o seguinte:

- Se for uma tarefa de execução única, a tarefa deverá ser excluída após a conclusão ser realizada com êxito.
- As configurações não devem depender de unidades diferentes de C ou D, pois apenas essas duas unidades sempre têm garantia de existência (a unidade C é o disco do sistema operacional e a unidade D é o disco local temporário).

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

### <a name="for-windows"></a>Para Windows

Os discos do sistema operacional Windows são generalizados com a ferramenta [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se, mais tarde, você atualizar ou reconfigurar o sistema operacional, deverá executar novamente o Sysprep.

> [!WARNING]
> Depois de executar o Sysprep, desligue a VM até que ela seja implantada, pois as atualizações podem ser executadas automaticamente. Esse desligamento evitará atualizações subsequentes de alterações específicas da instância no sistema operacional ou serviços instalados. Para obter mais informações sobre como executar o sysprep, confira [Etapas para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo a seguir generalizará uma VM do Linux e a reimplantará como uma VM separada. Para detalhes, confira [Como criar uma imagem de uma máquina virtual ou um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Você pode parar quando chegar à seção chamada "criar uma VM com base na imagem capturada".

1. Remover o agente Linux do Azure

    1. Conectar-se à sua VM do Linux usando um cliente SSH
    2. Na janela SSH, digite o seguinte comando: `sudo waagent –deprovision+user`.
    3. Digite Y para continuar (Você pode adicionar o parâmetro -force ao comando anterior para evitar essa etapa de confirmação).
    4. Depois que o comando for concluído, digite Exit para fechar o cliente SSH.

2. Como interromper uma máquina virtual

    1. No portal do Azure, selecione seu RG (grupo de recursos) e desaloque a VM.
    2. O VHD agora é generalizado e você poderá criar uma VM usando esse VHD.

## <a name="next-steps"></a>Próximas etapas

- Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação do VHD](common-issues-during-vhd-creation.md).
- Caso contrário, a [VM (máquina virtual) de teste implantada a partir do VHD](azure-vm-image-certification.md) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace, incluindo onde obter a ferramenta de teste de certificação para a ferramenta certificada do Azure e como usá-la para certificar a imagem da VM.
