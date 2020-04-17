---
title: Crie seus ativos técnicos da Máquina Virtual do Azure
description: Saiba como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 46c59e8b4e60fbe17887ea0fc375b6da758ebf50
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457392"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Crie seus ativos técnicos da Máquina Virtual do Azure

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas da Sua Máquina Virtual Do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [Criar ativos técnicos para uma oferta de máquina virtual para o Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) para gerenciar suas ofertas.

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace. Uma VM contém dois componentes: o disco rígido virtual do sistema operacional (VHD) e os Discos de dados associados opcionais VHDs:

* **Sistema operacional VHD** – Contém o sistema operacional e a solução que implanta com a sua oferta. O processo de preparação do VHD difere dependendo se é uma VM baseada em Linux, Windows ou personalizada.
* **Discos de dados VHDs** - Armazenamento dedicado e persistente para uma VM. Não use o sistema operacional VHD (por exemplo, a unidade C:) para armazenar informações persistentes.

Uma imagem VM contém um disco do sistema operacional e até 16 discos de dados. Use um VHD por disco de dados, mesmo que o disco esteja em branco.

> [!NOTE]
> Independentemente do sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas eles sempre podem adicionar discos durante ou após a implantação.

> [!IMPORTANT]
> Cada imagem VM em um plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar esses ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure quanto das tecnologias usadas para construir a oferta. Além do domínio da solução, sua equipe de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

* Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
* Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking)
* Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento prático de [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Ferramentas sugeridas – opcional

Considere usar um dos seguintes ambientes de scriptpara ajudar a gerenciar VMs e VHDs:

* [PowerShell do Azure](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Revise as ferramentas disponíveis na página Ferramentas para Desenvolvedores do [Azure](https://azure.microsoft.com/product-categories/developer-tools/) e, se você estiver usando o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Crie uma imagem VM usando uma base aprovada

> [!NOTE]
> Para criar seus ativos técnicos de máquina virtual usando uma imagem que você construiu em suas próprias instalações, vá para [Criar uma VM usando sua própria imagem](#create-a-vm-using-your-own-image).

Esta seção descreve vários aspectos do uso de uma base aprovada, como usar o Remote Desktop Protocol (RDP), selecionar um tamanho para a VM, instalar as atualizações mais recentes do Windows e generalizar a imagem VHD.

As seções a seguir se concentram principalmente em VHDs baseados em windows. Para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [o Linux sobre distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Siga a orientação neste tópico para usar o Azure para criar uma VM contendo um sistema operacional pré-configurado e endossado. Se isso não for compatível com a sua solução, é possível criar e configurar uma VM no local usando um sistema operacional aprovado. Você pode configurá-lo e prepará-lo para upload, conforme descrito em [ Preparar um VHDX ou VHDX do Windows para fazer o upload para o Azure ](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Selecione uma base aprovada

Selecione o sistema de operação windows ou o Linux como sua base.

#### <a name="windows"></a>Windows

O sistema operacional VHD para sua imagem VM baseada no Windows deve ser baseado em uma imagem base aprovada pelo Azure que contém O Servidor Windows ou SQL Server. Para começar, crie uma VM a partir de uma das seguintes imagens do portal Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Se você estiver usando o portal Azure atual ou o Azure PowerShell, então as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriormente são aprovadas.

#### <a name="linux"></a>Linux

O Azure oferece uma gama de distribuições Linux aprovadas. Para obter uma lista atual, consulte [Linux em distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal do Azure

Siga estas etapas para criar a imagem vm base no [portal Azure](https://ms.portal.azure.com/):

1. Faça login no [portal Azure](https://ms.portal.azure.com/) com a conta da Microsoft associada à assinatura do Azure que você deseja usar para publicar sua oferta de VM.
2. Crie um novo grupo de recursos e forneça seu **Nome do grupo de recursos**, **Assinatura** e **Local do grupo de recursos**. Para obter detalhes, consulte [Gerenciar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Selecione **máquinas virtuais** à esquerda para exibir a página de detalhes das máquinas virtuais.
4. Selecione **+ Adicione** para abrir a **experiência de uma máquina virtual**.
5. Selecione a imagem na lista de baixa ou clique **em Procurar todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
6. Selecione o tamanho da VM para implantar usando as seguintes recomendações:
    * Se você planeja desenvolver o VHD no local, o tamanho não importa. Considere o uso de uma das VMs menores.
    * Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

7. Na seção **Discos,** expanda a seção **Avançado** e defina a opção **Usar discos gerenciados** como **No**.
8. Forneça os outros detalhes necessários para criar a VM.
9. Selecione **'Revisar + criar** para rever suas escolhas' Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

O Azure começa a provisionar a máquina virtual especificada. Você pode acompanhar seu progresso selecionando a guia **Máquinas Virtuais** à esquerda. Depois de criado, o status mudará para **Running**.

Se você encontrar dificuldade em criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação de VHD (FAQs)](https://aka.ms/VHDcreationIssues).

### <a name="connect-to-your-azure-vm"></a>Conectar-se à VM do Azure

Esta seção explica como se conectar e entrar na VM que você criou no Azure. Depois de conectado com sucesso, você pode trabalhar com a VM como se estivesse conectado localmente ao seu servidor host.

#### <a name="connect-to-a-windows-based-vm"></a>Conectar em uma VM baseada em Windows

Use o cliente de desktop remoto para se conectar à VM baseada no Windows hospedada no Azure. A maioria das versões do Windows contêm suporte para o protocolo de área de trabalho remota (RDP). Para outros sistemas operacionais, você pode encontrar mais informações sobre clientes em [clientes de Desktop Remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Este artigo detalha como usar o suporte integrado ao Windows RDP para se conectar à sua VM: [Como conectar e fazer logon a uma máquina virtual do Azure executando o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Você pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O arquivo .rdp é de um editor desconhecido" ou "Suas credenciais de usuário não podem ser verificadas". É seguro ignorar esses avisos.

#### <a name="connect-to-a-linux-based-vm"></a>Conectar-se a uma máquina virtual baseada em Linux

Para se conectar a uma VM baseada em Linux, você precisa de um cliente ssh (secure Shell Protocol). As etapas seguintes utilizam o terminal SHH livre [putty.](https://www.ssh.com/ssh/putty/)

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Pesquise por **Máquinas virtuais** e selecione essa opção.
3. Selecione a VM a que deseja se conectar.
4. Inicie a VM se ela ainda não estiver funcionando.
5. Selecione o nome da VM para abrir sua página **Visão Geral.**
6. Observe o endereço IP público e o nome DNS da vm (se esses valores não estiverem definidos, você deve [criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Abra o aplicativo PuTTY.
8. Na caixa de diálogo de configuração do PuTTY, insira o endereço IP ou nome DNS da sua VM.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustra as configurações do terminal PuTTY. o nome do host ou endereço IP e caixas de porta estão destacados.":::

9. Selecione **Abrir** para abrir um terminal PuTTY.
10. Quando solicitado, digite o nome da conta e a senha da sua conta Linux VM.

Se você tiver problemas de conexão, consulte a documentação do seu cliente SSH. Por exemplo, [Capítulo 10: Mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals).

Para obter detalhes, incluindo como adicionar uma área de trabalho a uma VM Linux provisionada, consulte Instalar e configurar o [Remote Desktop para se conectar a um VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Crie uma VM usando sua própria imagem

Esta seção descreve como criar e implantar uma imagem vm (máquina virtual) fornecida pelo usuário. Você pode fazer isso fornecendo imagens VHD do sistema operacional e do disco de dados a partir de um disco rígido virtual (VHD) implantado pelo Azure.

> [!NOTE]
> Para usar opcionalmente uma imagem base aprovada, siga as instruções em [Criar uma imagem VM usando uma base aprovada](#create-a-vm-image-using-an-approved-base).

1. Envie suas imagens para a conta do Azure Storage.
2. Implante a imagem VM.
3. Capture a imagem vm.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Carregue suas imagens para uma conta de armazenamento do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Carregue seu sistema operacional generalizado VHD e VHDs de disco de dados para sua conta de armazenamento Azure.

### <a name="deploy-your-image"></a>Implante sua imagem

Crie sua imagem usando o portal Azure ou o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implantar usando o Portal do Azure

1. Na página inicial, **selecione Criar um recurso,** procure "Implantação de modelos" e selecione **Criar**.
2. Escolha **Construir seu próprio modelo no editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustra a página de implantação personalizada.":::

3. Cole este [modelo JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) no editor e selecione **Salvar**.
4. Forneça os valores de parâmetro para as páginas de propriedades **Implantação Personalizada**.

    | Parâmetro | Descrição |
    | ------------ | ------------- |
    | Nome de Conta de Armazenamento do Usuário | Conteúdo da célula 2 |
    | Nome do Contêiner de Armazenamento de Usuário | Nome da conta de armazenamento onde se encontra o VHD generalizado |
    | Nome DNS para o IP público | Nome Público de DNS ip. Defina o nome DNS para o endereço IP público no portal Azure após a implantação da oferta. |
    | Nome de Usuário do Administrador | Nome de usuário da conta do administrador para a nova VM |
    | Senha do Administrador | Senha da conta Administrador para nova VM |
    | Tipo de SO | Sistema operacional VM: Windows ou Linux |
    | ID da assinatura | Identificador para a assinatura selecionada |
    | Location | Localização geográfica da implantação |
    | Tamanho da VM | [Tamanho azure VM,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)por exemplo, Standard_A2 |
    | Nome do endereço IP público | Nome do seu endereço IP público |
    | Nome da VM | Nome da nova VM |
    | Nome da VNET | Nome da rede virtual usada pela VM |
    | Nome da NIC | Nome da placa de interface de rede em execução na rede virtual |
    | URL do VHD | Conclua a URL do VHD de disco do sistema operacional |
    |  |  |

5. Depois de fornecer esses valores, selecione **Comprar**.

O Azure começará a ser implantado. Ele cria uma nova VM com o VHD não gerenciado especificado no caminho da conta de armazenamento especificada. Você pode acompanhar o progresso no portal Azure selecionando **Máquinas Virtuais** no lado esquerdo do portal. Quando a VM for criada, o status mudará de Começar para Execução.

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

* Azure PowerShell: [como criar uma imagem VM não gerenciada a partir de uma VM azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* CLI do Azure: [Como criar uma imagem de uma máquina virtual ou um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configure a máquina virtual

Esta seção descreve como dimensionar, atualizar e generalizar uma VM Azure. Essas etapas são necessárias para preparar sua VM para ser implantada no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionar os VHDs

Se você selecionou uma das VMs pré-configuradas com um sistema operacional (e, opcionalmente, serviços adicionais), você já escolheu um tamanho padrão de VM do Azure. Iniciar sua solução com um SO pré-configurado é a abordagem recomendada. No entanto, se você estiver instalando um sistema operacional manualmente, você deve dimensionar seu VHD principal na sua imagem VM:

* Para windows, o sistema operacional VHD deve ser criado como um VHD de formato fixo de 127-128 GB.
* Para Linux, este VHD deve ser criado como um VHD de formato fixo de 30-50 GB.

Se o tamanho físico for inferior a 127-128 GB, o VHD deve ser expansível (esparso/dinâmico). As imagens base do Windows e do SQL Server que são fornecidas já atendem a esses requisitos, portanto não altere o formato ou o tamanho do VHD.

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como VHDs de formato fixo. Também devem ser expansíveis (esparsos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

As imagens base das VMs do sistema operacional devem conter as últimas atualizações até a data de publicação. Antes de publicar o sistema operacional VHD que você criou, certifique-se de atualizar o Sistema Operacional e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server, execute o comando **Verificar para atualizações.**

Para distribuições Linux, as atualizações geralmente são baixadas e instaladas por meio de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Gerenciador de Atualização](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SO.

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para suas imagens de solução no Azure Marketplace. O artigo a seguir fornece uma lista de verificação de configurações e procedimentos de segurança para ajudá-lo: [Recomendações de segurança para imagens do Mercado Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem de VM.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se for necessária uma configuração adicional, use uma tarefa programada que seja executada na inicialização para fazer quaisquer alterações finais na VM depois de implantada. Considere também as seguintes recomendações:

* Se for uma tarefa em execução, a tarefa deve ser excluída depois de concluída com sucesso.
* As configurações não devem depender de drives que não sejam C ou D, pois apenas essas duas unidades são sempre garantidas de existir (a unidade C é o disco do sistema operacional e a unidade D é o disco local temporário).

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para isso, o sistema operacional VHD deve ser generalizado, uma operação que remove todos os identificadores e drivers de software específicos de instância de uma VM.

### <a name="windows"></a>Windows

Os discos do sistema operacional do Windows são generalizados com a [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se você atualizar ou reconfigurar o sistema operacional subsequentemente, deverá executar novamente o sysprep.

> [!WARNING]
> Como as atualizações podem ser executadas automaticamente, depois de executar o sysprep, desligue a VM até que seja implantada. Esse desligamento evitará que atualizações subseqüentes sejam alterações específicas de instâncias no sistema operacional ou serviços instalados. Para obter mais informações sobre como executar o sysprep, consulte [Passos para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

O processo a seguir generaliza um VM Linux e o reimplanta como uma VM separada. Para obter detalhes, [consulte Como criar uma imagem de uma máquina virtual ou VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Você pode parar quando chegar à seção "Criar uma VM a partir da imagem capturada".

1. **Remover o agente Linux do Azure**

    1. Conecte-se à VM do Linux usando um cliente SSH.
    2. Na janela SSH, digite `sudo waagent -deprovision+user`o seguinte comando: .
    3. Tipo **Y** para continuar (você pode adicionar o parâmetro **de força** ao comando anterior para evitar a etapa de confirmação).
    d. Depois que o comando estiver concluído, **digite Exit** para fechar o cliente SSH.

2. **Como interromper uma máquina virtual**

    1. No portal Azure, selecione seu grupo de recursos (RG) e desaloque a VM.
    2. Seu VHD agora é generalizado e você pode criar uma nova VM usando este VHD.

## <a name="next-steps"></a>Próximas etapas

Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [Problemas comuns durante a criação do VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues).

Caso contrário:

* [Certificar sua imagem VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) explica como testar e enviar uma imagem VM para a certificação Do Azure Marketplace, incluindo onde obter a Ferramenta de Teste de Certificação para a ferramenta *Certificada Azure* e como usá-la para certificar sua imagem VM.
