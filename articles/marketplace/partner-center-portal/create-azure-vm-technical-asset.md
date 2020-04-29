---
title: Criar seus ativos técnicos da máquina virtual do Azure
description: Saiba como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730711"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Criar seus ativos técnicos da máquina virtual do Azure

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de máquina virtual do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [criar ativos técnicos para uma oferta de máquina virtual para Portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) gerenciar suas ofertas.

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de VM (máquina virtual) para o Azure Marketplace. Uma VM contém dois componentes: o VHD (disco rígido virtual) do sistema operacional e os VHDs de discos de dados associados opcionais:

* **VHD do sistema operacional** – contém o sistema operacional e a solução que é implantada com sua oferta. O processo de preparação do VHD difere dependendo de se trata-se de uma VM baseada em Linux, baseada no Windows ou em personalização.
* **VHDs de discos de dados** – armazenamento dedicado e persistente para uma VM. Não use o VHD do sistema operacional (por exemplo, a unidade C:) para armazenar informações persistentes.

Uma imagem de VM contém um disco de sistema operacional e até 16 discos de dados. Use um VHD por disco de dados, mesmo se o disco estiver em branco.

> [!NOTE]
> Independentemente do sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

> [!IMPORTANT]
> Cada imagem de VM em um plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta. Além do seu domínio de solução, sua equipe de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
* Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Ferramentas sugeridas – opcional

Considere usar um dos seguintes ambientes de script para ajudar a gerenciar VMs e VHDs:

* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Examine as ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem de VM usando uma base aprovada

> [!NOTE]
> Para criar seus ativos técnicos da máquina virtual usando uma imagem criada em sua própria empresa, vá para [criar uma VM usando sua própria imagem](#create-a-vm-using-your-own-image).

Esta seção descreve vários aspectos do uso de uma base aprovada, como usar o protocolo RDP (RDP), selecionar um tamanho para a VM, instalar as atualizações mais recentes do Windows e generalizar a imagem VHD.

As seções a seguir se concentram principalmente em VHDs baseados no Windows. Para obter mais informações sobre como criar VHDs baseados em Linux, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Siga as orientações neste tópico para usar o Azure para criar uma VM que contém um sistema operacional previamente configurado e endossado. Se isso não for compatível com sua solução, será possível criar e configurar uma VM local usando um sistema operacional aprovado. Você pode configurá-lo e prepará-lo para upload, conforme descrito em [ Preparar um VHDX ou VHDX do Windows para fazer o upload para o Azure ](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Selecione uma base aprovada

Selecione o sistema operacional Windows ou o Linux como sua base.

#### <a name="windows"></a>Windows

O VHD do sistema operacional para sua imagem de VM baseada no Windows deve ser baseado em uma imagem base aprovada pelo Azure que contém o Windows Server ou SQL Server. Para começar, crie uma VM de uma das seguintes imagens do portal do Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Se você estiver usando o portal do Azure ou Azure PowerShell atual, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriores serão aprovadas.

#### <a name="linux"></a>Linux

O Azure oferece uma variedade de distribuições do Linux aprovadas. Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure

Siga estas etapas para criar a imagem da VM de base no [portal do Azure](https://ms.portal.azure.com/):

1. Entre no [portal do Azure](https://ms.portal.azure.com/) com o conta Microsoft associado à assinatura do Azure que você deseja usar para publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça seu **Nome do grupo de recursos**, **Assinatura** e **Local do grupo de recursos**. Para obter detalhes, consulte [gerenciar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Selecione **máquinas virtuais** à esquerda para exibir a página de detalhes de máquinas virtuais.
4. Selecione **+ Adicionar** para abrir a **experiência criar uma máquina virtual**.
5. Selecione a imagem na lista suspensa ou clique em **procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis.
6. Selecione o tamanho da VM para implantar usando as seguintes recomendações:
    * Se você planeja desenvolver o VHD local, o tamanho não importa. Considere o uso de uma das VMs menores.
    * Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

7. Na seção **discos** , expanda a seção **avançado** e defina a opção **usar discos gerenciados** como **não**.
8. Forneça os outros detalhes necessários para criar a VM.
9. Selecione **revisão + criar** para revisar suas escolhas. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

O Azure começa a provisionar a máquina virtual que você especificou. Você pode acompanhar seu progresso selecionando a guia **máquinas virtuais** à esquerda. Depois de criado, o status será alterado para **em execução**.

Se você encontrar dificuldades para criar seu novo VHD baseado no Azure, consulte [problemas comuns durante a criação do VHD (FAQs)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation).

### <a name="connect-to-your-azure-vm"></a>Conectar-se à VM do Azure

Esta seção explica como se conectar e entrar na VM que você criou no Azure. Depois de se conectar com êxito, você poderá trabalhar com a VM como se você estivesse conectado localmente ao seu servidor host.

#### <a name="connect-to-a-windows-based-vm"></a>Conectar em uma VM baseada em Windows

Use o cliente de área de trabalho remota para se conectar à VM baseada em Windows hospedada no Azure. A maioria das versões do Windows contêm suporte para o protocolo de área de trabalho remota (RDP). Para outros sistemas operacionais, você pode encontrar mais informações sobre clientes em [clientes área de trabalho remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Este artigo fornece detalhes sobre como usar o suporte interno do Windows RDP para se conectar à sua VM: [como se conectar e fazer logon em uma máquina virtual do Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Você pode receber avisos de segurança durante o processo. Por exemplo, avisos como "o arquivo. rdp é de um Publicador desconhecido" ou "suas credenciais de usuário não podem ser verificadas". É seguro ignorar esses avisos.

#### <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma máquina virtual baseada em Linux

Para se conectar a uma VM baseada em Linux, você precisa de um cliente SSH (Secure Shell Protocol). As etapas a seguir usam o terminal [de Shh de](https://www.ssh.com/ssh/putty/) saída gratuito.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Pesquise por **Máquinas virtuais** e selecione essa opção.
3. Selecione a VM à qual você deseja se conectar.
4. Inicie a VM se ela ainda não estiver em execução.
5. Selecione o nome da VM para abrir sua página de **visão geral** .
6. Observe o endereço IP público e o nome DNS da sua VM (se esses valores não estiverem definidos, você deverá [criar um adaptador de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Abra o aplicativo PuTTY.
8. Na caixa de diálogo de configuração do PuTTY, insira o endereço IP ou nome DNS da sua VM.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustra as configurações de terminal de saída. as caixas nome do host ou endereço IP e porta são realçadas.":::

9. Selecione **abrir** para abrir um terminal de reemitida.
10. Quando solicitado, insira o nome da conta e a senha da sua conta de VM do Linux.

Se você tiver problemas de conexão, consulte a documentação do cliente SSH. Por exemplo, [capítulo 10: mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals).

Para obter detalhes, incluindo como adicionar uma área de trabalho a uma VM do Linux provisionada, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Criar uma VM usando sua própria imagem

Esta seção descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário. Você pode fazer isso fornecendo imagens VHD do sistema operacional e do disco de dados de um VHD (disco rígido virtual) implantado pelo Azure.

> [!NOTE]
> Para usar opcionalmente uma imagem de base aprovada, siga as instruções em [criar uma imagem de VM usando uma base aprovada](#create-a-vm-image-using-an-approved-base).

1. Carregue suas imagens na conta de armazenamento do Azure.
2. Implante a imagem da VM.
3. Capture a imagem da VM.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Carregar suas imagens em uma conta de armazenamento do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Carregue o VHD do sistema operacional generalizado e os VHDs do disco de dados em sua conta de armazenamento do Azure.

### <a name="deploy-your-image"></a>Implantar sua imagem

Crie sua imagem usando o portal do Azure ou o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implantar usando o Portal do Azure

1. Na home page, selecione **criar um recurso**, pesquise "implantação de modelo" e selecione **criar**.
2. Escolha **criar seu próprio modelo no editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustra a página de implantação personalizada.":::

3. Cole esse [modelo JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) no editor e selecione **salvar**.
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
    | Local | Localização geográfica da implantação |
    | Tamanho da VM | [Tamanho da VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), por exemplo Standard_A2 |
    | Nome do endereço IP público | Nome do seu endereço IP público |
    | Nome da VM | Nome da nova VM |
    | Nome da VNET | Nome da rede virtual usada pela VM |
    | Nome da NIC | Nome da placa de interface de rede em execução na rede virtual |
    | URL do VHD | Conclua a URL do VHD de disco do sistema operacional |
    |  |  |

5. Depois de fornecer esses valores, selecione **comprar**.

O Azure iniciará a implantação. Ele cria uma nova VM com o VHD não gerenciado especificado no caminho da conta de armazenamento especificado. Você pode acompanhar o progresso na portal do Azure selecionando **máquinas virtuais** no lado esquerdo do Portal. Quando a VM for criada, o status será alterado de iniciando para em execução.

#### <a name="deploy-using-azure-powershell"></a>Implantar usando o Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Captura da imagem da VM

Use as instruções a seguir que correspondem à sua abordagem:

* Azure PowerShell: [como criar uma imagem de VM não gerenciada de uma VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* CLI do Azure: [Como criar uma imagem de uma máquina virtual ou um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configurar a máquina virtual

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM a ser implantada no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionar os VHDs

Se você selecionou uma das VMs pré-configuradas com um sistema operacional (e, opcionalmente, serviços adicionais), você já escolheu um tamanho de VM padrão do Azure. Iniciar sua solução com um SO pré-configurado é a abordagem recomendada. No entanto, se você estiver instalando um so manualmente, deverá dimensionar seu VHD primário em sua imagem de VM:

* Para o Windows, o VHD do sistema operacional deve ser criado como um VHD de formato fixo de 127 a 128 GB.
* Para o Linux, esse VHD deve ser criado como um VHD de formato fixo de 30 a 50 GB.

Se o tamanho físico for menor que 127 – 128 GB, o VHD deverá ser expansível (esparso/dinâmico). As imagens base do Windows e SQL Server que são fornecidas já atendem a esses requisitos, portanto, não altere o formato ou o tamanho do VHD.

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como VHDs de formato fixo. Eles também devem ser expansíveis (esparsos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

As imagens base das VMs do sistema operacional devem conter as atualizações mais recentes até a data de publicação. Antes de publicar o VHD do sistema operacional que você criou, certifique-se de atualizar o sistema operacional e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server, execute o comando **verificar atualizações** .

Para distribuições Linux, as atualizações geralmente são baixadas e instaladas por meio de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Gerenciador de Atualização](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SO.

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para suas imagens de solução no Azure Marketplace. O artigo a seguir fornece uma lista de verificação de configurações e procedimentos de segurança para ajudá-lo: [recomendações de segurança para imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem de VM.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se for necessária configuração adicional, use uma tarefa agendada que seja executada na inicialização para fazer quaisquer alterações finais na VM após sua implantação. Considere também as seguintes recomendações:

* Se for uma tarefa de execução única, a tarefa deverá ser excluída a si mesma após ser concluída com êxito.
* As configurações não devem depender de unidades diferentes de C ou D, pois apenas essas duas unidades sempre têm garantia de existência (a unidade C é o disco do sistema operacional e a unidade D é o disco local temporário).

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para conseguir isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos da instância e drivers de software de uma VM.

### <a name="windows"></a>Windows

Os discos do sistema operacional do Windows são generalizados com a [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se você atualizar ou reconfigurar o sistema operacional subsequentemente, deverá executar novamente o sysprep.

> [!WARNING]
> Como as atualizações podem ser executadas automaticamente, depois de executar o Sysprep, desative a VM até que ela seja implantada. Esse desligamento evitará que as atualizações subsequentes façam alterações específicas da instância no sistema operacional ou nos serviços instalados. Para obter mais informações sobre como executar o Sysprep, consulte [etapas para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

O processo a seguir generaliza uma VM Linux e a implanta novamente como uma VM separada. Para obter detalhes, consulte [como criar uma imagem de uma máquina virtual ou VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Você pode parar quando chegar à seção "criar uma VM com base na imagem capturada".

1. **Remover o agente Linux do Azure**

    1. Conecte-se à VM do Linux usando um cliente SSH.
    2. Na janela SSH, digite o seguinte comando: `sudo waagent -deprovision+user`.
    3. Digite **Y** para continuar (você pode adicionar o parâmetro **-Force** ao comando anterior para evitar a etapa de confirmação).
    d. Após a conclusão do comando, digite **Exit** para fechar o cliente SSH.

2. **Como interromper uma máquina virtual**

    1. Na portal do Azure, selecione seu grupo de recursos (RG) e desaloque a VM.
    2. O VHD agora está generalizado e você pode criar uma nova VM usando esse VHD.

## <a name="next-steps"></a>Próximas etapas

Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação do VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues).

Caso contrário:

* [Certificar sua imagem de VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace, incluindo onde obter a ferramenta *de teste de certificação para a ferramenta certificada do Azure* e como usá-la para certificar a imagem da VM.
