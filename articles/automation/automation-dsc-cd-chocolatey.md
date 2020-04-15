---
title: Azure Automation State Configuração contínua implantação com Chocolatey
description: Descreve a implantação contínua do DevOps usando a configuração do estado de automação do Azure com o gerenciador de pacotes Chocolatey. Inclui um exemplo com o modelo completo do JSON Resource Manager e a fonte PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383273"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Fornecer implantação contínua para máquinas virtuais usando a configuração do estado de automação e chocolate

Em um mundo DevOps, existem muitas ferramentas para auxiliar com vários pontos no pipeline de integração contínua. A [configuração do Estado de](automation-dsc-overview.md) Automação do Azure é uma nova adição bem-vinda às opções que as equipes de DevOps podem empregar. 

O Azure Automation é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós e recursos compartilhados, como credenciais, horários e variáveis globais. A configuração do Estado de Automação do Azure amplia esse recurso de automação para incluir as ferramentas DSC (Desired State Configuration, configuração de estado desejado) do PowerShell. Confira uma excelente [visão geral](automation-dsc-overview.md).

Este artigo demonstra como configurar a Implantação Contínua (CD) para um computador Windows. Você pode facilmente estender a técnica para incluir quantos computadores Windows forem necessários na função, por exemplo, um site, e ir de lá para funções adicionais.

![Implantação contínua para VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="at-a-high-level"></a>Em um alto nível

Há um pouco acontecendo aqui, mas felizmente pode ser dividido em dois processos principais:

- Escrever código e testá-lo e, depois, criar e publicar pacotes de instalação para as versões principais e secundárias do sistema.
- Criação e gerenciamento de VMs que instalam e executam o código nos pacotes.  

Uma vez que ambos os processos principais estão em vigor, é fácil atualizar automaticamente o pacote em suas VMs à medida que novas versões são criadas e implantadas.

## <a name="component-overview"></a>Visão geral do componente

Os gerentes de pacotes, como o [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) são bem conhecidos no mundo Linux, mas não tanto no mundo windows.
[Chocolatey](https://chocolatey.org/) é uma coisa, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o tema é uma ótima introdução. Em poucas palavras, chocolatey permite que você use a linha de comando para instalar pacotes de um repositório central em um sistema operacional Windows. Você pode criar e gerenciar seu próprio repositório, e o Chocolatey pode instalar pacotes de qualquer repositório que você designar.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) é uma ferramenta PowerShell que permite que você declare a configuração que deseja para uma máquina. Por exemplo, se você quiser que o Chocolatey seja instalado, o IIS instalado, a porta 80 aberta e a versão 1.0.0 do seu site instalada, o DSC Local Configuration Manager (LCM) implementa essa configuração. Um servidor de tração DSC contém um repositório de configurações para suas máquinas. O LCM em cada computador verifica periodicamente se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar realinhar o computador com a configuração armazenada. Você pode editar a configuração armazenada no servidor de recepção para alinhar um computador ou um conjunto de computadores com a configuração alterada.

Um recurso DSC é um módulo de código que tem recursos específicos, como gerenciamento de rede, Active Directory ou SQL Server. O Recurso DSC do Chocolatey sabe como acessar um Servidor do NuGet (entre outros), baixar e instalar pacotes e assim por diante. Há muitos outros Recursos de DSC na [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Você instala esses módulos no servidor de tração do estado de automação do Azure para uso por suas configurações.

Os modelos do Gerenciador de Recursos fornecem uma maneira declarativa de gerar sua infra-estrutura, por exemplo, redes, sub-redes, segurança de rede e roteamento, balanceadores de carga, NICs, VMs e assim por diante. Aqui está um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implantação do Gerenciador de Recursos (declarativo) com o modelo de implantação do Azure Service Management (ASM ou clássico) (imperativo). Este artigo inclui uma discussão sobre os principais provedores de recursos: computação, armazenamento e rede.

Uma característica chave de um modelo de Gerenciador de recursos é sua capacidade de instalar uma extensão de VM na VM como ela é provisionada. Uma extensão de VM tem recursos específicos, como executar um script personalizado, instalar software antivírus e executar um script de configuração DSC. Há muitos outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Explicação rápida do diagrama

Começando por cima, você escreve seu código, constrói-o, testa-o e cria um pacote de instalação. O Chocolatey pode manipular vários tipos de pacotes de instalação, como MSI, MSU e ZIP. E você tem todo o poder da PowerShell para fazer a instalação real se as capacidades nativas do Chocolatey não forem capazes. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Este exemplo de uso usa uma pasta pública em uma conta de armazenamento de blob do Azure, mas pode estar em qualquer outro lugar. O Chocolatey funciona nativamente com servidores do NuGet e alguns outras para o gerenciamento de metadados de pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. O exemplo de uso usa NuGet. Um Nuspec consiste em metadados sobre seus pacotes. As informações do Nuspec são compiladas em um NuPkg e armazenadas em um servidor NuGet. Quando sua configuração solicita um pacote pelo nome e faz referência a um servidor NuGet, o recurso Chocolatey DSC na VM pega o pacote e o instala. Você também pode solicitar uma versão específica de um pacote.

No canto inferior esquerdo da imagem, há um modelo do Azure Resource Manager. Neste exemplo de uso, a extensão VM registra a VM com o servidor de tração do Estado de Automação do Azure como um nó. A configuração é armazenada no servidor de tração duas vezes: uma vez como texto simples e uma vez compilado como um arquivo MOF. No portal Azure, o MOF representa uma configuração de nó, em oposição a uma configuração simples. É o artefato que está associado a um nó para que o nó saiba sua configuração. Os detalhes a seguir mostram como atribuir a configuração de nó ao nó.

Criar o Nuspec, compilá-lo e armazená-lo em um servidor NuGet é uma coisa pequena. E você já está gerenciando VMs. 

Dar o próximo passo para a implantação contínua requer configurar o servidor pull uma vez, registrar seus nós com ele uma vez e criar e armazenar a configuração inicial no servidor. Como os pacotes são atualizados e implantados no repositório, você só precisa atualizar a configuração e a configuração do nó no servidor de tração conforme necessário.

Se você não está começando com um modelo de Gerenciador de Recursos, tudo bem. Existem comandos PowerShell para ajudá-lo a registrar suas VMs com o servidor pull. Para obter mais informações, consulte [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Sobre o exemplo de uso

O exemplo de uso neste artigo começa com uma VM de uma imagem Genérica do Windows Server 2012 R2 da galeria Azure. Você poderá iniciar de qualquer imagem armazenada e ajustá-la com a configuração da DSC.
No entanto, é muito mais difícil alterar a configuração incorporada a uma imagem do que atualizar de forma dinâmica a configuração usando a DSC.

Não é necessário usar um modelo do Resource Manager e a extensão da VM para usar essa técnica com as VMs. E as VMs não precisam estar no Azure para estar sob gerenciamento de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado na VM para que reconheça onde está o servidor de pull.

Quando você atualiza um pacote em uma VM que está em produção, você precisa tirar essa VM da rotação enquanto a atualização é instalada. A maneira de fazer isso varia muito. Por exemplo, com uma VM por trás de um Balanceador de Carga do Azure, você pode adicionar uma Sonda Personalizada. Ao atualizar a VM, faça com que o ponto de extremidade da sonda retorne um 400. O ajuste necessário para fazer essa alteração pode ser dentro de sua configuração, da mesma forma como o ajuste para que 200 volte a ser retornado depois que a atualização for concluída.

O código-fonte completo deste exemplo de uso está [neste projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passo 1: Configure a conta de servidor pull e automação

Em uma linha de comando do PowerShell autenticada (`Connect-AzAccount`): (pode demorar alguns minutos enquanto o servidor de pull é configurado)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar sua conta de Automação em qualquer uma das seguintes regiões (também conhecidas como localizações): Leste dos EUA 2, Centro-Sul dos EUA, EUA Gov Virgínia, Europa Ocidental, Sudeste Asiático, Japão Leste, Índia Central e Austrália Sudeste, Canadá Central, Norte da Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passo 2: Faça ajustes de extensão de VM no modelo do Gerenciador de Recursos

Detalhes do registro de VM (usando a extensão de VM de DSC do PowerShell) são fornecidos neste [Modelo de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Esta etapa registra a nova VM com o servidor de pull na lista de Nós da Configuração de Estado. Parte do registro especifica a configuração de nó a ser aplicada ao nó. Essa configuração de nó ainda não precisa existir no servidor pull, então é bom que o passo 4 seja onde isso é feito pela primeira vez. Mas aqui na Etapa 2, é necessário decidir o nome do nó e o nome da configuração. Neste exemplo de uso, o nó é “isvbox” e a configuração é “ISVBoxConfig”. Portanto, o nome da configuração de nó (a ser especificado em DeploymentTemplate.json) é “ISVBoxConfig.isvbox”.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passo 3: Adicionar recursos DSC necessários ao servidor de tração

A Galeria do PowerShell é instrumentada para instalar recursos de DSC em sua conta da Automação do Azure.
Navegue até o recurso que deseja e clique no botão "Implantar no Azure Automation".

![Exemplo da Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica recentemente adicionada ao portal Azure permite que você puxe novos módulos ou atualize módulos existentes. Clique no recurso da conta automação, no azulejo assets e, finalmente, no azulejo Módulos. O ícone da Galeria de Navegação permite que você veja a lista de módulos na galeria, aprofunde detalhes e, finalmente, importe para sua conta de Automação. Isso é uma ótima maneira de manter atualizados os módulos periodicamente. Além disso, o recurso de importação verifica dependências com outros módulos para garantir que nada esteja fora de sincronização.

Ou então, há a abordagem manual. Essa abordagem é usada apenas uma vez por recurso, a menos que você queira atualizá-la mais tarde. Para obter mais informações sobre a autoria dos módulos de integração do PowerShell, consulte [Módulos de Integração de Autoria para Automação Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>A estrutura de pasta de um módulo de integração PowerShell para um computador Windows é um pouco diferente da estrutura de pastas esperada pela Automação Azure. 

1. Instale [o Windows Management Framework v5](https://aka.ms/wmf5latest) (não necessário para o Windows 10).

2. Instale o módulo de integração.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copie a pasta do módulo de **c:\Arquivos do programa\WindowsPowerShell\Modules\MODULE-NAME** para uma pasta temporária.

4. Exclua amostras e documentação da pasta principal.

5. Feche a pasta principal, nomeando o arquivo ZIP com o nome da pasta.

6. Coloque o arquivo ZIP em um local HTTP acessível, como armazenamento blob em uma conta do Azure Storage.

7. Execute o comando a seguir.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

O exemplo incluído implementa essas etapas para cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passo 4: Adicione a configuração do nó ao servidor de tração

Não há nada de especial na primeira vez que você importa a configuração para o servidor de pull e compila. Todas as importações ou compilações posteriores da mesma configuração parecem exatamente iguais. Sempre que atualiza o pacote e precisa enviá-lo para produção, você realiza esta etapa após garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote. Aqui está o arquivo de configuração **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Aqui está o script **New-ConfigurationScript.ps1** (modificado para usar o módulo Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Essas etapas resultam em uma nova configuração de nó chamada **ISVBoxConfig.isvbox** sendo colocada no servidor pull. O nome de configuração `configurationName.nodeName`do nó é construído como .

## <a name="step-5-create-and-maintain-package-metadata"></a>Passo 5: Criar e manter metadados do pacote

Para cada pacote que você coloca no repositório de pacotes, é necessário um nuspec que o descreve.
Esse nuspec deve ser compilado e armazenado em seu servidor do NuGet. Este processo é descrito [aqui](https://docs.nuget.org/create/creating-and-publishing-a-package). Você pode usar MyGet.org como um servidor do NuGet. Esse serviço é vendido, porém, há uma SKU inicial que é gratuita. Ao NuGet.org, você encontrará instruções sobre como instalar seu próprio servidor NuGet para seus pacotes privados.

## <a name="step-6-tie-it-all-together"></a>Passo 6: Amarre tudo junto

Cada vez que uma versão passa qa e é aprovada para implantação, o pacote é criado, e nuspec e nupkg são atualizados e implantados no servidor NuGet. A configuração (Passo 4 acima) também deve ser atualizada para concordar com o novo número da versão. Em seguida, ele deve ser enviado para o servidor de tração e compilado.

Daí em diante, as VMs que dependem dessa configuração serão responsáveis por receber a atualização e instalá-la. Cada uma dessas atualizações é simples - apenas uma linha ou duas de PowerShell. Para o Azure DevOps, alguns deles são encapsulados em tarefas de construção que podem ser acorrentadas em uma compilação. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repo do GitHub](https://github.com/Microsoft/vso-agent-tasks) detalha as tarefas de compilação disponíveis.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do DSC da Automação do Azure](automation-dsc-overview.md)
* [cmdlets do DSC de Automação do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Integração de computadores para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Configuração do estado de automação do Azure](automation-dsc-overview.md).
- Para começar, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso da configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
