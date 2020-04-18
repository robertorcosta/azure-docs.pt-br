---
title: Automatizar a implantação de uma VM no Amazon Web Services
description: Este artigo demonstra como usar a Automação do Azure para automatizar a criação de uma VM do Amazon Web Services
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604840"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de Automação do Azure – provisionar uma máquina virtual do AWS
Neste artigo, você aprenderá a usar a Automação do Azure para provisionar uma máquina virtual na sua assinatura do AWS (Amazon Web Services) e dar um nome específico à VM – o que o AWS chama de "marcar" a VM.

## <a name="prerequisites"></a>Pré-requisitos
Você precisa ter uma conta do Azure Automation e uma assinatura amazon web services (AWS). Para obter mais informações sobre como configurar uma conta da Automação do Azure e configurá-la com suas credenciais de assinatura do AWS, consulte [Configurar a autenticação com a Amazon Web Services](automation-config-aws-account.md). Esta conta deve ser criada ou atualizada com suas credenciais de assinatura AWS antes de prosseguir, como você faz referência a esta conta nas seções abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implantar Módulo do PowerShell do Amazon Web Services
Seu runbook de provisionamento vm usa o módulo AWS PowerShell para fazer seu trabalho. Use as seguintes etapas para adicionar o módulo à sua conta de automação configurada com suas credenciais de assinatura AWS.  

1. Abra seu navegador da Web, navegue até a [Galeria do PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no botão **Implantar na Automação do Azure**.<br><br> ![Importação de módulo de PS do AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você será levado para a página de logon do Azure e, depois de autenticar, será encaminhado para o portal do Azure e verá a seguinte página:<br><br> ![Página Importar Módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta Automação a ser usada e clique em **OK** para iniciar a implantação.

   > [!NOTE]
   > Quando a Azure Automation importa um módulo PowerShell, ele extrai os cmdlets. As atividades não aparecem até que a Automação tenha terminado completamente a importação do módulo e a extração dos cmdlets. Esse processo pode levar alguns minutos.  
   > <br>

1. No portal do Azure, abra sua conta da Automação.
2. Clique no azulejo **Ativos** e, no painel Ativos, selecione **Módulos**.
3. Na página Módulos, você verá o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar runbook de implantação de VM no AWS
Após implantar o Módulo do PowerShell do AWS, você poderá criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS usando um script do PowerShell. As etapas abaixo demonstram como usar o script nativo do PowerShell no Azure Automation.  

> [!NOTE]
> Para ver mais opções e informações sobre esse script, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Baixe o script PowerShell New-AwsVM da PowerShell Gallery abrindo uma sessão powerShell e digitando o seguinte comando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. No portal Azure, abra sua conta de Automação e selecione **Runbooks** em **Automação de Processos**.  
3. Na página Runbooks, selecione **Adicionar um runbook**.
4. No Painel Adicionar um runbook, selecione **Criar rápido** para criar um novo runbook.
5. No painel de propriedades Runbook, digite um nome para o seu runbook.
6. Na lista de paradas do **tipo Runbook,** selecione **PowerShell**e clique em **Criar**.<br><br> ![Painel Criar runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando a página Editar Runbook do PowerShell aparecer, copie e cole o script do PowerShell na tela de criação do runbook.<br><br> ![Script do PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observe o seguinte ao trabalhar com o script do PowerShell de exemplo:
    > 
    > * O runbook contém diversos valores de parâmetros padrão. Avalie todos os valores padrão e atualize-os conforme necessário.
    > * Se você tiver armazenado suas credenciais AWS como `AWScred`um ativo de credencial chamado de forma diferente, você precisa atualizar o script na linha 57 para corresponder de acordo.  
    > * Ao trabalhar com comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, você deve especificar a região do AWS. Caso contrário, os cmdlets falharão. Para obter mais detalhes, consulte o tópico [Specify AWS Region (Especificar região do AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell (Ferramentas do AWS para PowerShell).  
    >

7. Para recuperar uma lista de nomes de imagem de sua assinatura do AWS, inicie o ISE do PowerShell e importe o Módulo do PowerShell do AWS. Autenticar contra a AWS `Get-AutomationPSCredential` substituindo no `AWScred = Get-Credential`ambiente ISE por . Esta instrução solicita suas credenciais e você pode fornecer seu ID de chave de acesso para o nome de usuário e sua chave de acesso secreta para a senha. 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    A seguinte saída será retornada:<br><br>
   ![Obter imagens do AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copie e cole um dos nomes de imagem em uma variável `$InstanceType`de Automação como referenciado no runbook como . Uma vez que, neste exemplo, você está usando a assinatura hierárquica gratuita da AWS, você usa **t2.micro** para o seu exemplo de runbook.  
9. Salve o runbook, clique em **Publicar** para publicá-lo e em **Sim** quando solicitado.

### <a name="testing-the-aws-vm-runbook"></a>Testando o runbook de VM do AWS
Antes de prosseguir com o teste do manual, você precisa verificar algumas coisas:

* Um recurso `AWScred` chamado para autenticação contra a AWS foi criado, ou o script foi atualizado para referenciar seu nome de ativo de credencial.    
* O módulo AWS PowerShell foi importado na Azure Automation.  
* Um novo runbook foi criado e os valores dos parâmetros foram verificados e atualizados quando necessário.  
* **Registre registros verbose** e, opcionalmente, **Registre registros de progresso** sob a operação de runbook O registro e o **rastreamento** foram definidos **como On**.<br><br> ![Registro e rastreamento](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)de runbook .  

1. Clique **em Iniciar** para iniciar o manual e, em seguida, clique em **OK** quando o painel Iniciar runbook for aberto.
2. No painel Start Runbook, forneça um nome VM. Aceite os valores padrão para os outros parâmetros que você pré-configurou no script. Clique em **OK** para iniciar o trabalho de runbook.<br><br> ![Iniciar runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Um painel de emprego é aberto para o trabalho de runbook que você criou. Feche esse painel.
4. Você pode ver o progresso do trabalho e visualizar fluxos de saída selecionando **Todos os Logs** do painel de trabalho do runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar se a VM está sendo provisionada, entre no Console de Gerenciamento da AWS se você não estiver conectado no momento.<br><br> ![VM implantada por console do AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar com runbooks gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar com os runbooks do PowerShell Workflow, consulte [Meu primeiro runbook de fluxo de trabalho PowerShell](automation-first-runbook-textual.md).
* Para saber mais sobre os tipos de runbook e suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).
* Para obter mais informações sobre o recurso de suporte ao script PowerShell, consulte [o suporte ao script Native PowerShell no Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).