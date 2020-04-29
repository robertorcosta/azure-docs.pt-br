---
title: Automatizar a implantação de uma VM no Amazon Web Services
description: Este artigo demonstra como usar a Automação do Azure para automatizar a criação de uma VM do Amazon Web Services
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604840"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de Automação do Azure – provisionar uma máquina virtual do AWS
Neste artigo, você aprenderá a usar a Automação do Azure para provisionar uma máquina virtual na sua assinatura do AWS (Amazon Web Services) e dar um nome específico à VM – o que o AWS chama de "marcar" a VM.

## <a name="prerequisites"></a>Pré-requisitos
Você precisa ter uma conta de automação do Azure e uma assinatura Amazon Web Services (AWS). Para obter mais informações sobre como configurar uma conta da Automação do Azure e configurá-la com suas credenciais de assinatura do AWS, consulte [Configurar a autenticação com a Amazon Web Services](automation-config-aws-account.md). Essa conta deve ser criada ou atualizada com suas credenciais de assinatura do AWS antes de continuar, conforme você faz referência a essa conta nas seções a seguir.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implantar Módulo do PowerShell do Amazon Web Services
Seu runbook de provisionamento de VM usa o módulo do PowerShell do AWS para fazer seu trabalho. Use as etapas a seguir para adicionar o módulo à sua conta de automação configurada com suas credenciais de assinatura do AWS.  

1. Abra seu navegador da Web, navegue até a [Galeria do PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no botão **Implantar na Automação do Azure**.<br><br> ![Importação de módulo de PS do AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você será levado para a página de logon do Azure e, depois de autenticar, será encaminhado para o portal do Azure e verá a seguinte página:<br><br> ![Página Importar Módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta de automação a ser usada e clique em **OK** para iniciar a implantação.

   > [!NOTE]
   > Quando a automação do Azure importa um módulo do PowerShell, ele extrai os cmdlets. As atividades não aparecem até que a automação tenha concluído completamente a importação do módulo e a extração dos cmdlets. Esse processo pode levar alguns minutos.  
   > <br>

1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **ativos** e, no painel ativos, selecione **módulos**.
3. Na página Módulos, você verá o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar runbook de implantação de VM no AWS
Após implantar o Módulo do PowerShell do AWS, você poderá criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS usando um script do PowerShell. As etapas a seguir demonstram como usar o script nativo do PowerShell na automação do Azure.  

> [!NOTE]
> Para ver mais opções e informações sobre esse script, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Baixe o script New-AwsVM do PowerShell no Galeria do PowerShell abrindo uma sessão do PowerShell e digitando o seguinte comando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. No portal do Azure, abra sua conta de automação e selecione **Runbooks** em **automação de processo**.  
3. Na página Runbooks, selecione **Adicionar um runbook**.
4. No painel Adicionar um runbook, selecione **criação rápida** para criar um novo runbook.
5. No painel Propriedades do runbook, digite um nome para o runbook.
6. Na lista suspensa **tipo de runbook** , selecione **PowerShell**e, em seguida, clique em **criar**.<br><br> ![Painel Criar runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando a página Editar Runbook do PowerShell aparecer, copie e cole o script do PowerShell na tela de criação do runbook.<br><br> ![Script do PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observe o seguinte ao trabalhar com o script do PowerShell de exemplo:
    > 
    > * O runbook contém diversos valores de parâmetros padrão. Avalie todos os valores padrão e atualize-os conforme necessário.
    > * Se você armazenou suas credenciais do AWS como um ativo de credencial `AWScred`chamado de forma diferente do, precisará atualizar o script na linha 57 para que ele seja correspondente.  
    > * Ao trabalhar com comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, você deve especificar a região do AWS. Caso contrário, os cmdlets falharão. Para obter mais detalhes, consulte o tópico [Specify AWS Region (Especificar região do AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell (Ferramentas do AWS para PowerShell).  
    >

7. Para recuperar uma lista de nomes de imagem de sua assinatura do AWS, inicie o ISE do PowerShell e importe o Módulo do PowerShell do AWS. Autentique no AWS substituindo `Get-AutomationPSCredential` em seu ambiente do ISE `AWScred = Get-Credential`pelo. Essa instrução solicita suas credenciais e você pode fornecer sua ID de chave de acesso para o nome de usuário e sua chave de acesso secreta para a senha. 

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
8. Copie e cole um dos nomes de imagem em uma variável de automação conforme referenciado no runbook como `$InstanceType`. Como, neste exemplo, você está usando a assinatura em camadas gratuita do AWS, use **T2. micro** para seu exemplo de runbook.  
9. Salve o runbook, clique em **Publicar** para publicá-lo e em **Sim** quando solicitado.

### <a name="testing-the-aws-vm-runbook"></a>Testando o runbook de VM do AWS
Antes de prosseguir com o teste do runbook, você precisa verificar algumas coisas:

* Um ativo chamado `AWScred` para autenticação no AWS foi criado ou o script foi atualizado para referenciar o nome do seu ativo de credencial.    
* O módulo do PowerShell do AWS foi importado na automação do Azure.  
* Um novo runbook foi criado e os valores de parâmetro foram verificados e atualizados quando necessário.  
* **Registros detalhados de log** e, opcionalmente, **registros de progresso** em log **e rastreamento** de operação de runbook foram definidos como **on**.<br><br> ![Rastreamento](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)e log de runbook.  

1. Clique em **Iniciar** para iniciar o runbook e, em seguida, clique em **OK** quando o painel Iniciar runbook for aberto.
2. No painel Iniciar runbook, forneça um nome de VM. Aceite os valores padrão para os outros parâmetros pré-configurados no script. Clique em **OK** para iniciar o trabalho de runbook.<br><br> ![Iniciar runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Um painel de trabalho é aberto para o trabalho de runbook que você criou. Feche esse painel.
4. Você pode exibir o progresso do trabalho e exibir os fluxos de saída selecionando **todos os logs** no painel trabalho do runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que a VM está sendo provisionada, faça logon no console de gerenciamento do AWS se você não estiver conectado no momento.<br><br> ![VM implantada por console do AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks gráficos, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md).
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para saber mais sobre tipos de runbook e suas vantagens e limitações, confira [tipos de runbook de automação do Azure](automation-runbook-types.md).
* Para obter mais informações sobre o recurso de suporte de script do PowerShell, consulte [Native PowerShell script support na automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).