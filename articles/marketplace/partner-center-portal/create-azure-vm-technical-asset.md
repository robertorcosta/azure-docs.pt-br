---
title: Criar seus ativos técnicos da Máquina Virtual do Azure
description: Saiba como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: 69458a7a3f2555a301c1266addfe7c49cb111ef6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317749"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Criar seus ativos técnicos da Máquina Virtual do Azure

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace. Uma VM contém dois componentes: o VHD (disco rígido virtual) do sistema operacional e os VHDs dos discos de dados associados opcionais:

* **VHD do sistema operacional** – contém o sistema operacional e a solução que é implantada com sua oferta. O processo de preparação de VHD será diferente dependendo se é uma VM baseada em Linux, baseada em Windows ou uma VM personalizada.
* **VHDs de discos de dados** – armazenamento persistente dedicado para uma VM. Não use a solução o VHD do sistema operacional (por exemplo, a unidade C:) para armazenar informações persistentes.

Uma imagem de VM contém um disco de sistema operacional e até 16 discos de dados. Use um VHD por disco de dados, mesmo se o disco estiver em branco.

> [!NOTE]
> Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

> [!IMPORTANT]
> Cada imagem de VM em um plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta. Além do domínio da solução, a equipe de engenharia deverá ter conhecimento das seguintes tecnologias da Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
* Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Ferramentas sugeridas – opcional

Considere usar um dos seguintes ambientes de script para ajudar a gerenciar VMs e VHDs:

* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/)
* [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao ambiente de desenvolvimento:

* [Gerenciador de Armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Examine as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem de VM usando uma base aprovada

> [!NOTE]
> Para criar seus ativos técnicos da máquina virtual usando uma imagem criada em sua empresa, acesse [Criar uma VM usando sua imagem](#create-a-vm-using-your-own-image).

Essa seção descreve os vários aspectos do uso de uma base aprovada, como o uso do protocolo RDP, a seleção de um tamanho para a VM, a instalação das atualizações mais recentes do Windows e a generalização da imagem do VHD.

As seções a seguir se concentram principalmente em VHDs baseados no Windows. Para obter mais informações sobre como criar um VHDs com base em Linux, confira [Distribuições do Linux endossadas no Azure](../../virtual-machines/linux/endorsed-distros.md).

> [!WARNING]
> Siga as orientações deste tópico para usar o Azure para criar uma VM que contenha um sistema operacional pré-configurado e endossado. Se isso não for compatível com sua solução, será possível criar e configurar uma VM local usando um sistema operacional aprovado. Você pode configurá-lo e prepará-lo para upload, conforme descrito em [ Preparar um VHDX ou VHDX do Windows para fazer o upload para o Azure ](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Selecione uma base aprovada

Selecione o sistema operacional Windows ou Linux como sua base.

#### <a name="windows"></a>Windows

O VHD do sistema operacional para a imagem de VM com base em Windows deve ser baseado em uma imagem de base aprovada do Azure que contenha o Windows Server ou o SQL Server. Para começar, crie uma VM de uma das seguintes imagens no portal do Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)

> [!NOTE]
> Se você estiver usando o portal do Azure atual ou o Azure PowerShell, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriormente serão aprovadas.

#### <a name="linux"></a>Linux

O Azure oferece uma variedade de distribuições aprovadas do Linux. Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure

Siga estas etapas para criar a imagem de VM de base no [portal do Azure](https://ms.portal.azure.com/):

1. Entre no [portal do Azure](https://ms.portal.azure.com/) com a conta Microsoft associada à assinatura do Azure que você deseja usar para publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça seu **Nome do grupo de recursos**, **Assinatura** e **Local do grupo de recursos**. Para obter detalhes, confira [Gerenciar recursos](../../azure-resource-manager/resource-group-portal.md).
3. Selecione **Máquinas virtuais** à esquerda para exibir a página de detalhes de máquinas virtuais.
4. Selecione **+ Adicionar** para abrir **Criar uma experiência de máquina virtual**.
5. Selecione a imagem na lista suspensa ou clique em **Procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis.
6. Selecione o tamanho da VM para implantar usando as seguintes recomendações:
    * Se você pretende desenvolver o VHD localmente, o tamanho não importa. Considere o uso de uma das VMs menores.
    * Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

7. Na seção **Discos**, expanda a seção **Avançado** e defina a opção **Usar discos gerenciados** como **Não**.
8. Forneça os outros detalhes necessários para criar a VM.
9. Selecione **Examinar + criar** para examinar suas opções. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

O Azure inicia o provisionamento da máquina virtual especificada. Você pode acompanhar seu progresso selecionando a guia **Máquinas Virtuais** à esquerda. Depois de criado, o status mudará para **Em execução**.

Se você teve dificuldades para criar seu VHD baseado no Azure, confira [Problemas comuns durante a criação do VHD (perguntas frequentes)](common-issues-during-vhd-creation.md).

### <a name="connect-to-your-azure-vm"></a>Conectar-se à VM do Azure

Esta seção explica como se conectar e entrar com a VM que você criou no Azure. Depois de você se conectar com êxito, poderá trabalhar com a VM como se tivesse efetuado logon localmente em seu servidor de host.

#### <a name="connect-to-a-windows-based-vm"></a>Conectar em uma VM baseada em Windows

Você usa o cliente de Área de Trabalho Remota para se conectar à VM com base em Windows hospedada no Azure. A maioria das versões do Windows contêm suporte para o protocolo de área de trabalho remota (RDP). Para outros sistemas operacionais, você pode encontrar mais informações sobre os clientes nos [clientes de Área de Trabalho Remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Este artigo fornece detalhes sobre como usar o suporte interno do RDP do Windows para se conectar à sua VM: [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Você pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O arquivo .rdp é de um editor desconhecido" ou "Suas credenciais de usuário não podem ser verificadas". É seguro ignorar esses avisos.

#### <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma máquina virtual baseada em Linux

Para conectar-se a VM com base em Linux, é necessário um cliente do protocolo (SSH) de um shell seguro. As etapas a seguir usam a versão gratuita do terminal [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Pesquise por **Máquinas virtuais** e selecione essa opção.
3. Selecione a VM a qual você deseja se conectar.
4. Inicie a VM se ainda não estiver sendo executada.
5. Selecione o nome da VM para abrir sua página de **Visão geral**.
6. Observe o endereço IP público e o nome DNS da sua VM (se esses valores não estiverem definidos, você deverá [criar um adaptador de rede](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Abra o aplicativo PuTTY.
8. Na caixa de diálogo de configuração do PuTTY, insira o endereço IP ou nome DNS da sua VM.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustra as configurações do terminal do PuTTY. As caixas Nome do host ou Endereço IP e Porta estão realçadas.":::

9. Selecione **Abrir** para abrir um terminal do PuTTY.
10. Quando você for solicitado, insira o nome da conta e a senha da sua conta de VM do Linux.

Se você tiver problemas de conexão, veja a documentação do cliente SSH. Por exemplo, [Capítulo 10: mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals).

Para detalhes, incluindo como adicionar uma área de trabalho a uma VM Linux provisionada, confira [Instalar e configurar a Área de Trabalho Remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Criar uma VM usando sua imagem

Esta seção descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário. Você pode fazer isso fornecendo imagens VHD do sistema operacional e do disco de dados de um VHD (disco rígido virtual) implantado pelo Azure.

> [!NOTE]
> Para usar opcionalmente uma imagem de base aprovada, siga as instruções em [Criar uma imagem de VM usando uma base aprovada](#create-a-vm-image-using-an-approved-base).

1. Carregue suas imagens na conta de Armazenamento do Azure.
2. Implante a imagem da VM.
3. Capture a imagem da VM.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Carregar suas imagens na conta de Armazenamento do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Carregue o VHD do sistema operacional generalizado e os VHDs do disco de dados em sua conta de armazenamento do Azure.

### <a name="deploy-your-image"></a>Implantar sua imagem

Crie sua imagem usando o portal do Azure ou o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implantar usando o Portal do Azure

1. Na home page, selecione **Criar um recurso**, pesquise "Implantação de Modelo" e selecione **Criar**.
2. Escolha **Criar seu modelo no editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustra a página de implantação personalizada.":::

3. Copie este [modelo JSON](../partner-center-portal/azure-vm-image-certification.md) no editor e selecione **Salvar**.
4. Forneça os valores de parâmetro para as páginas de propriedades **Implantação Personalizada**.

    | Parâmetro | Descrição |
    | ------------ | ------------- |
    | Nome de Conta de Armazenamento do Usuário | Conteúdo da célula 2 |
    | Nome do Contêiner de Armazenamento de Usuário | Nome da conta de armazenamento onde se encontra o VHD generalizado |
    | Nome DNS para o IP público | Nome DNS do IP público. Defina o nome DNS para o endereço IP público no portal do Azure depois que a oferta for implantada. |
    | Nome de Usuário do Administrador | Nome de usuário da conta do administrador para a nova VM |
    | Senha do Administrador | Senha da conta Administrador para nova VM |
    | Tipo de SO | Sistema operacional da VM: Windows ou Linux |
    | ID da assinatura | Identificador para a assinatura selecionada |
    | Location | Localização geográfica da implantação |
    | Tamanho da VM | [Tamanho da VM do Azure](../../virtual-machines/windows/sizes.md), por exemplo Standard_A2 |
    | Nome do endereço IP público | Nome do seu endereço IP público |
    | Nome da VM | Nome da nova VM |
    | Nome da VNET | Nome da rede virtual usada pela VM |
    | Nome da NIC | Nome da placa de interface de rede em execução na rede virtual |
    | URL do VHD | Conclua a URL do VHD de disco do sistema operacional |
    |  |  |

5. Depois de fornecer esses valores, selecione **Comprar**.

O Azure iniciará a implantação. Ele cria uma VM com o VHD não gerenciado especificado, no caminho de conta de armazenamento especificada. Você pode acompanhar o progresso no portal do Azure selecionando **Máquinas Virtuais** no lado esquerdo do portal. Quando a VM tiver sido criada, o status será alterado de Iniciando para Em execução.

#### <a name="deploy-using-azure-powershell"></a>Implantar usando o Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Captura da imagem da VM

Use as seguintes instruções que correspondem à sua abordagem:

* Azure PowerShell: [Como criar uma imagem de VM não gerenciada a partir de uma VM do Azure](../../virtual-machines/windows/capture-image-resource.md)
* CLI do Azure: [Como criar uma imagem de máquina virtual ou um VHD](../../virtual-machines/linux/capture-image.md)
* API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configurar a máquina virtual

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionar os VHDs

Se você selecionou uma das VMs pré-configuradas com um sistema operacional (e, opcionalmente, serviços adicionais), então já escolheu um tamanho de VM do Azure padrão. Iniciar sua solução com um SO pré-configurado é a abordagem recomendada. No entanto, se você estiver instalando um sistema operacional manualmente, será necessário dimensionar o VHD principal na imagem da VM:

* Para Windows, o VHD do sistema operacional deve ser criado como um VHD de formato fixo de 127 a 128 GB.
* Para Linux, esse VHD deve ser criado como um VHD de formato fixo de 30 a 50 GB.

Se o tamanho físico for menor que 127 – 128 GB, o VHD deverá ser Expansível (esparso/dinâmico). As imagens básicas do Windows e do SQL Server fornecidas já atenderam a esses requisitos; portanto, não altere o formato nem o tamanho do VHD.

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como VHDs de formato fixo. Eles também devem ser Expansíveis (esparsos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

As imagens de base das VMs do sistema operacional devem conter as atualizações mais recentes até a data publicada. Antes de publicar o VHD do sistema operacional que você criou, assegure-se de atualizar o sistema operacional e todos os serviços instalados com os patches de segurança e manutenção mais recentes.

Para Windows Server, execute o comando **Verificar se há atualizações**.

Para distribuições Linux, as atualizações geralmente são baixadas e instaladas por meio de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Gerenciador de Atualização](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SO.

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para as imagens de solução no Azure Marketplace. O seguinte artigo fornece uma lista de verificação de configurações e procedimentos de segurança para ajudar você: [Recomendações de segurança para imagens do Azure Marketplace](../../security/security-recommendations-azure-marketplace-images.md). Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem de VM.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se uma configuração adicional for necessária, use uma tarefa agendada que seja executada na inicialização para fazer as alterações finais na VM assim que ela for implantada. Considere também as seguintes recomendações:

* Se for uma tarefa de execução única, a tarefa deverá ser excluída após a conclusão ser realizada com êxito.
* As configurações não devem depender de unidades diferentes de C ou D, pois apenas essas duas unidades sempre têm garantia de existência (a unidade C é o disco do sistema operacional e a unidade D é o disco local temporário).

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

### <a name="windows"></a>Windows

Os discos do sistema operacional do Windows são generalizados com a [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se você atualizar ou reconfigurar o sistema operacional subsequentemente, deverá executar novamente o sysprep.

> [!WARNING]
> Como as atualizações poderão ser executadas automaticamente após a execução do sysprep, desligue a VM até que ela seja implantada. Esse desligamento evitará atualizações subsequentes de alterações específicas da instância no sistema operacional ou serviços instalados. Para obter mais informações sobre como executar o Sysprep, consulte [etapas para generalizar um VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)).

### <a name="linux"></a>Linux

O processo a seguir generalizará uma VM do Linux e a reimplantará como uma VM separada. Para detalhes, confira [Como criar uma imagem de uma máquina virtual ou um VHD](../../virtual-machines/linux/capture-image.md). Você pode parar quando chegar à seção "Criar uma VM com base na imagem capturada".

1. **Remover o agente Linux do Azure**

    1. Conecte-se à VM do Linux usando um cliente SSH.
    2. Na janela SSH, digite o seguinte comando: `sudo waagent -deprovision+user`.
    3. Digite **Y** para continuar (Você pode adicionar o parâmetro **-force** ao comando anterior para evitar essa etapa de confirmação).
    d. Depois que o comando for concluído, digite **Exit** para fechar o cliente SSH.

2. **Interromper uma máquina virtual**

    1. No portal do Azure, selecione seu RG (grupo de recursos) e desaloque a VM.
    2. O VHD agora é generalizado e você poderá criar uma VM usando esse VHD.

## <a name="next-steps"></a>Próximas etapas

Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação do VHD](common-issues-during-vhd-creation.md).

Caso contrário:

* [Certificar sua imagem de VM](get-sas-uri.md) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace, incluindo onde obter a *Ferramenta de teste de certificação para o Azure Certified* e como usá-la para certificar a imagem da VM.
