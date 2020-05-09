---
title: Implantação contínua da configuração de estado da automação do Azure com Chocolatey
description: Descreve a implantação contínua do DevOps usando a configuração de estado da automação do Azure com o gerente de pacotes de Chocolatey. Inclui um exemplo com o modelo do Gerenciador de recursos JSON completo e a origem do PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 278c6ee05fdf78cbfa8653381b65233fbb513593
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996113"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Fornecer implantação contínua para máquinas virtuais usando configuração de estado de automação e Chocolatey

Em um mundo DevOps, há muitas ferramentas para ajudá-lo com vários pontos no pipeline de integração contínua. A [configuração de estado](automation-dsc-overview.md) da automação do Azure é uma nova adição de boas-vindas às opções que as equipes de DevOps podem empregar. 

A automação do Azure é um serviço gerenciado no Microsoft Azure que permite automatizar várias tarefas usando runbooks, nós e recursos compartilhados, como credenciais, agendas e variáveis globais. A configuração de estado da automação do Azure estende esse recurso de automação para incluir as ferramentas de configuração de estado desejado (DSC) do PowerShell. Confira uma excelente [visão geral](automation-dsc-overview.md).

Este artigo demonstra como configurar a implantação contínua (CD) para um computador Windows. Você pode estender facilmente a técnica para incluir quantos computadores Windows forem necessários na função, por exemplo, um site e ir de lá para funções adicionais.

![Implantação contínua para VMs do IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Em um alto nível

Há muito pouco acontecendo aqui, mas felizmente ele pode ser dividido em dois processos principais:

- Escrever código e testá-lo e, depois, criar e publicar pacotes de instalação para as versões principais e secundárias do sistema.
- Criar e gerenciar VMs que instalam e executam o código nos pacotes.  

Uma vez que ambos os processos principais estejam em vigor, é fácil atualizar automaticamente o pacote em suas VMs à medida que novas versões são criadas e implantadas.

## <a name="component-overview"></a>Visão geral do componente

Os gerenciadores de pacotes, como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , são bem conhecidos no mundo Linux, mas não muito no mundo do Windows.
O [Chocolatey](https://chocolatey.org/) é tal coisa, e o [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman sobre o assunto é uma excelente introdução. Resumindo, Chocolatey permite que você use a linha de comando para instalar pacotes de um repositório central em um sistema operacional Windows. Você pode criar e gerenciar seu próprio repositório, e o Chocolatey pode instalar pacotes de qualquer repositório que você designar.

O [DSC do PowerShell](/powershell/scripting/dsc/overview/overview) é uma ferramenta do PowerShell que permite declarar a configuração desejada para um computador. Por exemplo, se você quiser que o Chocolatey esteja instalado, o IIS instalado, a porta 80 aberta e a versão 1.0.0 do seu site instalado, o LCM local de DSC (Configuration Manager) implementa essa configuração. Um servidor de pull de DSC mantém um repositório de configurações para seus computadores. O LCM em cada computador verifica periodicamente se sua configuração corresponde à configuração armazenada. Ele pode relatar o status ou tentar realinhar o computador com a configuração armazenada. Você pode editar a configuração armazenada no servidor de recepção para alinhar um computador ou um conjunto de computadores com a configuração alterada.

Um recurso de DSC é um módulo de código que tem recursos específicos, como o gerenciamento de rede, Active Directory ou SQL Server. O Recurso DSC do Chocolatey sabe como acessar um Servidor do NuGet (entre outros), baixar e instalar pacotes e assim por diante. Há muitos outros Recursos de DSC na [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Você instala esses módulos em seu servidor de pull de configuração de estado da automação do Azure para uso por suas configurações.

Os modelos do Resource Manager fornecem uma maneira declarativa de gerar sua infraestrutura, por exemplo, redes, sub-redes, segurança de rede e roteamento, balanceadores de carga, NICs, VMs e assim por diante. Veja um [artigo](../azure-resource-manager/management/deployment-models.md) que compara o modelo de implantação do Gerenciador de recursos (declarativo) com o modelo de implantação do gerenciamento de serviços do Azure (ASM ou clássico) (imperativo). Este artigo inclui uma discussão sobre os principais provedores de recursos: computação, armazenamento e rede.

Um recurso importante de um modelo do Resource Manager é sua capacidade de instalar uma extensão de VM na VM à medida que ela é provisionada. Uma extensão de VM tem recursos específicos, como a execução de um script personalizado, a instalação de software antivírus e a execução de um script de configuração DSC. Há muitos outros tipos de extensões de VM.

## <a name="quick-trip-around-the-diagram"></a>Explicação rápida do diagrama

Começando na parte superior, você escreve seu código, cria-o, testa-o e, em seguida, cria um pacote de instalação. O Chocolatey pode manipular vários tipos de pacotes de instalação, como MSI, MSU e ZIP. E você tem todo o poder do PowerShell para fazer a instalação real se as funcionalidades nativas de Chocolatem não possuírem. Coloque o pacote em algum lugar acessível – um repositório de pacotes. Este exemplo de uso usa uma pasta pública em uma conta de armazenamento de blob do Azure, mas pode estar em qualquer outro lugar. O Chocolatey funciona nativamente com servidores do NuGet e alguns outras para o gerenciamento de metadados de pacote. [Este artigo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descreve as opções. O exemplo de uso usa o NuGet. Um Nuspec consiste em metadados sobre seus pacotes. As informações do Nuspec são compiladas em um NuPkg e armazenadas em um servidor NuGet. Quando sua configuração solicita um pacote por nome e faz referência a um servidor NuGet, o recurso de DSC do Chocolatey na VM captura o pacote e o instala. Você também pode solicitar uma versão específica de um pacote.

Na parte inferior esquerda da imagem, há um modelo de Azure Resource Manager. Neste exemplo de uso, a extensão de VM registra a VM com o servidor de pull de configuração de estado da automação do Azure como um nó. A configuração é armazenada no servidor de pull duas vezes: uma vez como texto sem formatação e uma vez compilada como um arquivo MOF. Na portal do Azure, o MOF representa uma configuração de nó, em oposição a uma configuração simples. É o artefato associado a um nó para que o nó saiba sua configuração. Os detalhes a seguir mostram como atribuir a configuração de nó ao nó.

Criar o Nuspec, compilá-lo e armazená-lo em um servidor NuGet é uma coisa pequena. E você já está gerenciando VMs. 

Fazer o próximo passo para a implantação contínua requer a configuração do servidor de pull uma vez, o registro de seus nós com ele uma vez e a criação e o armazenamento da configuração inicial no servidor. À medida que os pacotes são atualizados e implantados no repositório, você só precisa atualizar a configuração e a configuração de nó no servidor de pull, conforme necessário.

Se você não estiver começando com um modelo do Resource Manager, tudo bem. Há comandos do PowerShell para ajudá-lo a registrar suas VMs com o servidor de pull. Para obter mais informações, consulte [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Sobre o exemplo de uso

O exemplo de uso neste artigo começa com uma VM de uma imagem genérica do Windows Server 2012 R2 da galeria do Azure. Você poderá iniciar de qualquer imagem armazenada e ajustá-la com a configuração da DSC.
No entanto, é muito mais difícil alterar a configuração incorporada a uma imagem do que atualizar de forma dinâmica a configuração usando a DSC.

Não é necessário usar um modelo do Resource Manager e a extensão da VM para usar essa técnica com as VMs. E as VMs não precisam estar no Azure para estar sob gerenciamento de CD. Tudo o que é necessário é que o Chocolatey seja instalado e o LCM configurado na VM para que reconheça onde está o servidor de pull.

Quando você atualiza um pacote em uma VM que está em produção, precisa levar essa VM fora de rotação enquanto a atualização é instalada. A maneira de fazer isso varia muito. Por exemplo, com uma VM por trás de um Balanceador de Carga do Azure, você pode adicionar uma Sonda Personalizada. Ao atualizar a VM, faça com que o ponto de extremidade da sonda retorne um 400. O ajuste necessário para fazer essa alteração pode ser dentro de sua configuração, da mesma forma como o ajuste para que 200 volte a ser retornado depois que a atualização for concluída.

O código-fonte completo deste exemplo de uso está [neste projeto do Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) no GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Etapa 1: configurar o servidor de pull e a conta de automação

Em uma linha de comando do PowerShell autenticada (`Connect-AzAccount`): (pode demorar alguns minutos enquanto o servidor de pull é configurado)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Você pode colocar sua conta de automação em qualquer uma das seguintes regiões (também conhecidas como locais): leste dos EUA 2, Sul EUA Central, US Gov-Virgínia, Europa Ocidental, Sudeste Asiático, leste do Japão, Índia central e sudeste da Austrália, Canadá central Europa Setentrional.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Etapa 2: fazer ajustes de extensão de VM para o modelo do Resource Manager

Detalhes do registro de VM (usando a extensão de VM de DSC do PowerShell) são fornecidos neste [Modelo de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Esta etapa registra a nova VM com o servidor de pull na lista de Nós da Configuração de Estado. Parte do registro especifica a configuração de nó a ser aplicada ao nó. Essa configuração de nó ainda não precisa existir no servidor de pull, portanto, é bom que a etapa 4 é onde isso é feito pela primeira vez. Mas aqui na Etapa 2, é necessário decidir o nome do nó e o nome da configuração. Neste exemplo de uso, o nó é “isvbox” e a configuração é “ISVBoxConfig”. Portanto, o nome da configuração de nó (a ser especificado em DeploymentTemplate.json) é “ISVBoxConfig.isvbox”.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Etapa 3: adicionar os recursos de DSC necessários ao servidor de pull

A Galeria do PowerShell é instrumentada para instalar recursos de DSC em sua conta da Automação do Azure.
Navegue até o recurso desejado e clique no botão "implantar na automação do Azure".

![Exemplo da Galeria do PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Outra técnica adicionada recentemente à portal do Azure permite que você extraia novos módulos ou atualize os módulos existentes. Clique no recurso conta de automação, no bloco ativos e, por fim, no bloco módulos. O ícone procurar na Galeria permite que você veja a lista de módulos na Galeria, aprofunde-se em detalhes e, por fim, importe para sua conta de automação. Isso é uma ótima maneira de manter atualizados os módulos periodicamente. Além disso, o recurso de importação verifica dependências com outros módulos para garantir que nada esteja fora de sincronização.

Há também uma abordagem manual, usada apenas uma vez por recurso, a menos que você queira atualizá-la mais tarde. Para obter mais informações sobre a criação de módulos de integração do PowerShell, consulte [criando módulos de integração para a automação do Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>A estrutura de pastas de um módulo de integração do PowerShell para um computador com Windows é um pouco diferente da estrutura de pastas esperada pela automação do Azure. 

1. Instale o [Windows Management Framework V5](https://aka.ms/wmf5latest) (não é necessário para o Windows 10).

2. Instale o módulo de integração.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copie a pasta do módulo de **C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** para uma pasta temporária.

4. Exclua os exemplos e a documentação da pasta principal.

5. Zip a pasta principal, nomeando o arquivo ZIP com o nome da pasta.

6. Coloque o arquivo ZIP em um local HTTP acessível, como o armazenamento de BLOBs em uma conta de armazenamento do Azure.

7. Execute o comando a seguir.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

O exemplo incluído implementa essas etapas para cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Etapa 4: adicionar a configuração de nó ao servidor de pull

Não há nada de especial na primeira vez que você importa a configuração para o servidor de pull e compila. Todas as importações ou compilações posteriores da mesma configuração têm exatamente a mesma aparência. Sempre que atualiza o pacote e precisa enviá-lo para produção, você realiza esta etapa após garantir que o arquivo de configuração está correto, incluindo a nova versão do pacote. Este é o arquivo de configuração **ISVBoxConfig. ps1**:

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

Este é o script **New-ConfigurationScript. ps1** (modificado para usar o módulo AZ):

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

Essas etapas resultam em uma nova configuração de nó chamada **ISVBoxConfig. isvbox** que está sendo colocada no servidor de pull. O nome da configuração do nó é `configurationName.nodeName`criado como.

## <a name="step-5-create-and-maintain-package-metadata"></a>Etapa 5: criar e manter metadados de pacote

Para cada pacote que você coloca no repositório de pacotes, você precisa de um Nuspec que o descreva. Ele deve ser compilado e armazenado em seu servidor NuGet. Este processo é descrito [aqui](https://docs.nuget.org/create/creating-and-publishing-a-package). 

Você pode usar o **MyGet.org** como um servidor NuGet. Você pode comprar esse serviço, mas o três é um SKU de início gratuito. No [NuGet](https://www.nuget.org/), você encontrará instruções sobre como instalar seu próprio servidor NuGet para seus pacotes privados.

## <a name="step-6-tie-it-all-together"></a>Etapa 6: reunir tudo juntos

Cada vez que uma versão passa o controle de qualidade e é aprovada para implantação, o pacote é criado e o nuspec e o nupkg são atualizados e implantados no servidor NuGet. A configuração (etapa 4) também deve ser atualizada para concordar com o novo número de versão. Em seguida, ele deve ser enviado para o servidor de pull e compilado.

Daí em diante, as VMs que dependem dessa configuração serão responsáveis por receber a atualização e instalá-la. Cada uma dessas atualizações é simples-apenas uma linha ou duas do PowerShell. Para o Azure DevOps, alguns deles são encapsulados em tarefas de compilação que podem ser encadeadas em uma compilação. Este [artigo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) fornece mais detalhes. Este [repositório GitHub](https://github.com/Microsoft/vso-agent-tasks) detalha as tarefas de compilação disponíveis.

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do Azure DSC de Automação](automation-dsc-overview.md)
* [Integração de computadores para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [configuração de estado da automação do Azure](automation-dsc-overview.md).
- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
