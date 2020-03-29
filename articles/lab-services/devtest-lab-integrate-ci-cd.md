---
title: Integre o Azure DevTest Labs em seus pipelines azure
description: Saiba como integrar Azure DevTest Labs no pipeline de entrega e na integração contínua de seu Azure Pipelines
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293208"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integre o Azure DevTest Labs em seu pipeline CI/CD do Azure Pipelines

Você pode usar a extensão *Azure DevTest Labs Tasks* para integrar os gasodutos de integração contínua e entrega contínua (CI/CD) do Azure Pipelines para integrar os pipelines de construção e liberação contínua do Azure Pipelines com o Azure DevTest Labs. A extensão instala várias tarefas, incluindo: 

- Criar uma VM (máquina virtual)
- Criar uma imagem personalizada de uma VM
- Excluir uma VM 

Essas tarefas facilitam, por exemplo, implantar rapidamente uma VM *de imagem dourada* para uma tarefa de teste específica e, em seguida, excluir a VM quando o teste estiver concluído.

Este artigo mostra como usar o Azure DevTest Labs Tasks para criar e implantar uma VM, criar uma imagem personalizada e, em seguida, excluir a VM, tudo como um pipeline de versão. Você normalmente executaria as tarefas individualmente em sua própria compilação, teste e implantação de pipelines personalizados.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Registre-se ou faça login na organização [Azure DevOps](https://dev.azure.com) e [crie um projeto](/vsts/organizations/projects/create-project) na organização. 
  
- Instale a extensão Azure DevTest Labs Tasks do Visual Studio Marketplace.
  
  1. Acesse [Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selecione **Obter gratuitamente**.
  1. Selecione a organização Azure DevOps a partir da parada e selecione **Instalar**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Crie o modelo para construir uma VM Azure 

Esta seção descreve como criar o modelo do Azure Resource Manager que você usa para criar uma VM do Azure demanda.

1. Para criar um modelo de Gerenciador de recursos em sua assinatura, siga o procedimento em [Use um modelo de Gerenciador de recursos](devtest-lab-use-resource-manager-template.md).
   
1. Antes de gerar o modelo do Resource Manager, adicione o [artefato WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) como parte da criação da VM. Tarefas de implantação como *a cópia de arquivos Do Zure* e o *PowerShell nas máquinas de destino* precisam de acesso WinRM. O artefato WinRM requer um nome de host como parâmetro, que deve ser o nome de domínio totalmente qualificado (FQDN) da VM. 
   
   > [!NOTE]
   > Quando usar o WinRM com um endereço IP compartilhado, você deverá adicionar uma regra NAT para mapear uma porta externa para a porta do WinRM. Você não precisa da regra NAT se criar a VM com um endereço IP público.
   
   
1. Salve o modelo no computador como um arquivo chamado *CreateVMTemplate.json*.
   
1. Verifique o modelo no sistema de controle de origem.

## <a name="create-a-script-to-get-vm-properties"></a>Crie um script para obter propriedades VM

Quando você executa etapas de tarefas como *Azure File Copy* ou *PowerShell on Target Machines* no pipeline de versão, o script a seguir coleta os valores necessários para implantar um aplicativo em uma VM. Você normalmente usaria essas tarefas para implantar seu aplicativo em uma VM Do Zure. As tarefas exigem valores como o nome do grupo de recursos VM, endereço IP e FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar o arquivo de script:

1. Abra um editor de texto e cole o seguinte script nele.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Salve o arquivo com um nome como *GetLabVMParams.ps1*e faça o check-in no sistema de controle de origem. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Criar um pipeline de lançamento no Azure Pipelines

Para criar um novo pipeline de lançamento:

1. Na página do projeto Azure DevOps, selecione **Lançamentos** > de**pipelines** na navegação à esquerda.
1. Selecione **Novo Pipeline**.
1. Em **Selecionar um modelo,** rolar para baixo e selecionar **Esvaziar o trabalho**e, em seguida, selecionar **Aplicar**.

### <a name="add-and-set-variables"></a>Adicionar e definir variáveis

As tarefas do pipeline usam os valores atribuídos à VM quando você criou o modelo Gerenciador de recursos no portal Azure. 

Para adicionar variáveis para os valores: 

1. Na página do pipeline, selecione a guia **Variáveis.**
   
1. Para cada variável, **selecione Adicionar** e digitar o nome e o valor:
   
   |Nome|Valor|
   |---|---|
   |*vmName*|Nome vm que você atribuiu no modelo Gerenciador de recursos|
   |*Username*|Nome de usuário para acessar a VM|
   |*senha*|Senha para o nome de usuário. Selecione o ícone de bloqueio para ocultar e proteger a senha.

### <a name="create-a-devtest-labs-vm"></a>Crie um VM de DevTest Labs

O próximo passo é criar a VM de imagem dourada para usar para futuras implantações. Você cria a VM dentro da instância Azure DevTest Labs usando a tarefa *Azure DevTest Labs Create VM.*

1. Na guia **'Pipeline'** de liberação, selecione o texto hiperligado no **Estágio 1** para exibir **tarefas de estágio**e, em seguida, selecione o sinal de adesão **+** ao lado do trabalho do **Agente**. 
   
1. Em **Adicionar tarefas,** selecione **Azure DevTest Labs Criar VM**e selecionar **Adicionar**. 
   
1. Selecione **Criar Azure DevTest Labs VM** no painel esquerdo. 

1. No painel direito, preencha o formulário da seguinte maneira:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione uma conexão de serviço ou assinatura das **Conexões de Serviço Disponíveis do Azure** ou assinaturas disponíveis do **Azure** no dropdown e selecione **Autorizar,** se necessário.<br /><br />**Nota:** Para obter informações sobre como criar uma conexão de permissões mais restrita susce a sua assinatura do Azure, consulte [o ponto final do serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual o laboratório VM será criado.|
   |**Nome do modelo**|Digite o caminho completo e o nome do arquivo de modelo que você salvou no seu repositório de código fonte. Você pode usar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parâmetros do modelo**|Digite os parâmetros das variáveis definidas anteriormente:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Variáveis de** > saída**Laboratório VM ID**|Digite a variável para o Laboratório VM ID criado. Se você usar o **labVMId**padrão, você pode consultar a variável em tarefas subseqüentes como *$(labVMId)*.<br /><br />Você pode criar um nome diferente do padrão, mas lembre-se de usar o nome correto em tarefas subseqüentes. Você pode escrever o ID VM do laboratório da seguinte forma:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Colete os detalhes do DevTest Labs VM

Execute o script que você criou anteriormente para coletar os detalhes da VM do DevTest Labs. 

1. Na guia **'Pipeline'** de liberação, selecione o texto hiperligado no **Estágio 1** para exibir **tarefas de estágio**e, em seguida, selecione o sinal de adesão **+** ao lado do trabalho do **Agente**. 
   
1. Em **Adicionar tarefas,** selecione **Azure PowerShell**e selecione **Adicionar**. 
   
1. Selecione **o script Azure PowerShell: FilePath** no painel esquerdo. 
   
1. No painel direito, preencha o formulário da seguinte maneira:
   
   |Campo|Valor|
   |---|---|
   |**Tipo de conexão Azure**|Selecione **o Gerenciador de Recursos do Azure**.|
   |**Assinatura do Azure**|Selecione sua conexão de serviço ou assinatura.| 
   |**Tipo de script**|Selecione **Caminho de arquivo de script**.|
   |**Caminho do script**|Digite o caminho completo e o nome do script PowerShell que você salvou no seu repositório de código-fonte. Você pode usar propriedades incorporadas para simplificar o caminho, por exemplo:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumentos de script**|Digite o nome da variável *labVmId* que foi povoada pela tarefa anterior, por exemplo:<br /><br />`-labVmId '$(labVMId)'`|

O script coleta os valores necessários e os armazena em variáveis de ambiente dentro do pipeline de liberação, para que você possa facilmente encaminhá-los em etapas subseqüentes.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Crie uma imagem VM do VM do DevTest Labs

A próxima tarefa é criar uma imagem da VM recém-implantada na instância do Azure DevTest Labs. Você pode usar a imagem para criar cópias da VM sob demanda sempre que desejar executar uma tarefa de desenvolvimento ou executar alguns testes. 

1. Na guia **'Pipeline'** de liberação, selecione o texto hiperligado no **Estágio 1** para exibir **tarefas de estágio**e, em seguida, selecione o sinal de adesão **+** ao lado do trabalho do **Agente**. 
   
1. Em **Adicionar tarefas,** selecione **Azure DevTest Labs Criar imagem personalizada**e selecionar **Adicionar**. 
   
1. Configure as tarefas da seguinte forma:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura Azure RM**|Selecione sua conexão de serviço ou assinatura.|
   |**Nome do laboratório**|Selecione o nome de um laboratório existente no qual a imagem será criada.|
   |**Nome da imagem personalizada**|Digite um nome para a imagem personalizada.|
   |**Descrição** (opcional)|Digite uma descrição para facilitar a seleção da imagem correta mais tarde.|
   |**Fonte Lab VM** > **Fonte Lab VM ID**|Se você alterou o nome padrão da variável LabVMId, digite-o aqui. O valor padrão é **$(labVMId)**.|
   |**Variáveis de** > saída**ID de imagem personalizado**|Você pode editar o nome padrão da variável, se necessário.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Implante seu aplicativo no DevTest Labs VM (opcional)

Você pode adicionar tarefas para implantar seu aplicativo no novo DevTest Labs VM. As tarefas que você geralmente usa para implantar o aplicativo são *Azure File Copy* e *PowerShell em Target Machines*.

As informações de VM necessárias para os parâmetros dessas tarefas são armazenadas em três variáveis de configuração chamadas **labVmRgName,** **labVMIpAddress**e **labVMFqdn** dentro do pipeline de liberação. Se você quiser apenas experimentar com a criação de uma VM do DevTest Labs e uma imagem personalizada, sem implantar um aplicativo nela, poderá ignorar essa etapa.

### <a name="delete-the-vm"></a>Excluir a VM

A tarefa final é excluir a VM que você implantou na instância do Azure DevTest Labs. Normalmente, você excluiria a VM após executar as tarefas de desenvolvimento ou executaria os testes necessários na VM implantada. 

1. Na guia **'Pipeline'** de liberação, selecione o texto hiperligado no **Estágio 1** para exibir **tarefas de estágio**e, em seguida, selecione o sinal de adesão **+** ao lado do trabalho do **Agente**. 
   
1. Em **Adicionar tarefas,** selecione **Azure DevTest Labs Delete VM**e selecione **Adicionar**. 
   
1. Configure as tarefas da seguinte forma:
   
   - Em **Assinatura Azure RM,** selecione sua conexão de serviço ou assinatura. 
   - Para **ID VM do Laboratório,** se você alterou o nome padrão da variável LabVMId, digite-o aqui. O valor padrão é **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Salvar o pipeline de liberação

Para salvar o novo pipeline de lançamento:

1. Selecione o nome **Novo pipeline de lançamento** na página de pipeline de lançamento e digite um novo nome para o pipeline. 
   
1. Selecione o ícone **Salvar** no canto superior direito. 

## <a name="create-and-run-a-release"></a>Criar e executar uma versão

Para criar e executar uma versão usando o novo pipeline:

1. Selecione **Criar a versão** no canto superior direito na página do pipeline de liberação. 
   
1. Em **Artefatos,** selecione a compilação mais recente e, em seguida, selecione **Criar**.
   
1. Em cada fase de lançamento, atualize a visualização da instância do DevTest Labs no portal Azure para visualizar a criação de VM, criação de imagens e exclusão de VM.

Você pode usar a imagem personalizada para criar VMs sempre que precisar delas.

## <a name="next-steps"></a>Próximas etapas
- Aprenda como [Criar ambientes de várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
- Explore modelos mais rápidos do Gerenciador de recursos para automação do DevTest Labs a partir do [repo público do DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- Se necessário, consulte a página [de solução de problemas do Azure DevOps.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
