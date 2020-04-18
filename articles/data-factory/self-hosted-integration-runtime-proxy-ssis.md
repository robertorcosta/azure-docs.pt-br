---
title: Configure um tempo de execução de integração auto-hospedado como um proxy para SSIS
description: Aprenda a configurar um tempo de execução de integração auto-hospedado como um proxy para um Runtime de Integração Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605503"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configure um IR auto-hospedado como um proxy para um IR Azure-SSIS na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como executar pacotes SQL Server Integration Services (SSIS) em um Azure-SSIS Integration Runtime (Azure-SSIS IR) na Fábrica de Dados Azure com um tempo de execução de integração auto-hospedado (IR auto-hospedado) configurado como um proxy. 

Com esse recurso, você pode acessar dados no local sem ter que aderir ao [seu IR Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). O recurso é útil quando sua rede corporativa tem uma configuração muito complexa ou uma política muito restritiva para você injetar seu IR Azure-SSIS nele.

Esse recurso divide qualquer tarefa de fluxo de dados SSIS que tenha uma fonte de dados local em duas tarefas de preparação: 
* A primeira tarefa, que é executada em seu RI auto-hospedado, primeiro move os dados da fonte de dados local para uma área de preparação no armazenamento Do Azure Blob.
* A segunda tarefa, que é executada no seu IR Azure-SSIS, move os dados da área de preparação para o destino dos dados pretendidos.

Outros benefícios e recursos desse recurso permitem, por exemplo, configurar seu IR auto-hospedado em regiões que ainda não são suportadas por um IR Azure-SSIS e permitir que o endereço IP estático público do seu IR auto-hospedado no firewall de suas fontes de dados.

## <a name="prepare-the-self-hosted-ir"></a>Prepare o IR auto-hospedado

Para usar esse recurso, primeiro crie uma fábrica de dados e configure um IR Azure-SSIS nele. Se você ainda não fez isso, siga as instruções em [Configurar um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Em seguida, você configura seu IR auto-hospedado na mesma fábrica de dados onde seu IR Azure-SSIS está configurado. Para isso, consulte [Criar um IR auto-hospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Finalmente, você baixa e instala a versão mais recente do IR auto-hospedado, bem como os drivers adicionais e tempo de execução, em sua máquina local ou máquina virtual Azure (VM), da seguinte forma:
- Baixe e instale a versão mais recente do [IR auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717).
- Se você usar conectores OLEDB (Object Linking and Inbedding Database) em seus pacotes, baixe e instale os drivers OLEDB relevantes na mesma máquina onde seu IR auto-hospedado está instalado, se você ainda não o fez.  

  Se você usar a versão anterior do driver OLEDB para SQL Server (SQL Server Native Client [SQLNCLI]), [baixe a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se você usar a versão mais recente do driver OLEDB para SQL Server (MSOLEDBSQL), [baixe a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se você usa drivers OLEDB para outros sistemas de banco de dados, como PostgreSQL, MySQL, Oracle e assim por diante, você pode baixar as versões de 64 bits de seus sites.
- Se você ainda não fez isso, [baixe e instale a versão de 64 bits do tempo de execução do Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) na mesma máquina onde seu IR auto-hospedado está instalado.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Prepare o serviço de armazenamento Azure Blob para a encenação

Se você ainda não fez isso, crie um serviço vinculado ao armazenamento Azure Blob na mesma fábrica de dados onde o SEU IR Azure-SSIS está configurado. Para isso, consulte [Criar um serviço vinculado à fábrica de dados do Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Certifique-se de fazer o seguinte:
- Para **armazenamento de dados,** selecione **Armazenamento Azure Blob**.  
- Para **conectar via tempo de execução de integração,** selecione **AutoResolveIntegrationRuntime** (não seu IR Azure-SSIS nem seu IR auto-hospedado), porque usamos o IR padrão do Azure para buscar credenciais de acesso para o seu Armazenamento Azure Blob.
- Para **o método de autenticação,** selecione a chave **Conta,** **SAS URI**ou Diretor de **Serviço.**  

    >[!TIP]
    >Se você selecionar o método **Principal do serviço,** conceda ao seu principal de serviço pelo menos uma função de Contribuinte de  *Dados blob de armazenamento.* Para obter mais informações, consulte [o conector de armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

![Prepare o serviço de armazenamento Azure Blob para a encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configure um IR Azure-SSIS com seu IR auto-hospedado como um proxy

Tendo preparado seu serviço de ir e armazenamento Azure Blob auto-hospedado para a encenação, agora você pode configurar seu NOVO OU existente Azure-SSIS IR com o IR auto-hospedado como um proxy em seu portal ou aplicativo de fábrica de dados. Antes de fazê-lo, porém, se o seu IR Azure-SSIS existente já estiver em execução, pare-o e, em seguida, reinicie-o.

1. No painel **de configuração de tempo de execução Integração,** pule as seções **Configurações Gerais** e **Configurações SQL** selecionando **Next**. 

1. Na seção **Configurações Avançadas,** faça o seguinte:

   1. Selecione o Executar o **Runtime de integração auto-hospedado configurado como um proxy para a caixa de seleção de tempo de execução de integração Azure-SSIS.** 

   1. Na lista suspensa **de execução de integração auto-hospedada,** selecione seu IR auto-hospedado existente como um proxy para o IR Azure-SSIS.

   1. Na lista de paradas de **serviço vinculado satisfazendo** o armazenamento Staging, selecione o serviço vinculado ao armazenamento Azure Blob existente ou crie um novo para estágio.

   1. Na caixa **de caminho Staging,** especifique um recipiente blob na conta de armazenamento Azure Blob selecionada ou deixe-a vazia para usar uma padrão para encenar.

   1. Selecione **Continuar**.

   ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Você também pode configurar seu IR Azure-SSIS novo ou existente com o IR auto-hospedado como proxy usando o PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Habilite que os pacotes SSIS se conectem por proxy

Usando a mais recente extensão de SSDT com projetos SSIS para o `ConnectByProxy` Visual Studio ou um instalador autônomo, você pode encontrar uma nova propriedade que foi adicionada nos gerentes de conexão OLEDB ou Flat File.
* [Baixe o SSDT com extensão de Projetos SSIS para Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Baixe o instalador autônomo](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Quando você projeta novos pacotes que contêm tarefas de fluxo de dados com fontes OLEDB ou Flat File, que permitem acessar bancos de dados ou arquivos no local, você pode habilitar essa propriedade definindo-a como *True* no painel **Propriedades** dos gerentes de conexão relevantes.

![Habilitar a propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Você também pode habilitar essa propriedade quando executar pacotes existentes, sem ter que alterá-los manualmente um a um.  Há duas opções:
- **Opção A**: Abra, reconstrua e reimplante o projeto que contém esses pacotes com o SSDT mais recente para executar em seu IR Azure-SSIS. Em seguida, você pode habilitar a propriedade definindo-a *como True* para os gerentes de conexão relevantes. Quando estão executando pacotes de SSMS, esses gerentes de conexão aparecem na guia **Gerentes** de conexão da janela pop-up **Execute Package.**

  ![Ativar propriedade ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Você também pode habilitar a propriedade definindo-a como *True* para os gerentes de conexão relevantes que aparecem na guia **Gerentes** de conexão da [atividade execute ssis package](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando eles estão executando pacotes em pipelines de Fábrica de Dados.
  
  ![Ativar propriedade ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opção B:** Reimplante o projeto que contém esses pacotes para executar em seu SSIS IR. Em seguida, você pode habilitar `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a propriedade fornecendo seu caminho de propriedade e definindo-o *True* como uma substituição de propriedade na guia **Avançado** da janela pop-up Execute **Package** quando você estiver executando pacotes de SSMS.

  ![Ativar propriedade ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Você também pode habilitar a propriedade `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`fornecendo seu caminho de propriedade e definindo-o como uma substituição de propriedade na guia **Substituições** de propriedades da [atividade execute ssis package](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) quando você estiver executando pacotes em pipelines da Fábrica de Dados. *True*
  
  ![Ativar a propriedade ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Depurar a primeira e segunda tarefas de encenação

Em seu IR auto-hospedado, você pode encontrar os logs de tempo de execução na pasta *C:\ProgramData\SSISTelemetry* e os registros de execução das primeiras tarefas de estágio na pasta *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Você pode encontrar os registros de execução de tarefas de segunda execução em seu SSISDB ou caminhos de registro especificados, dependendo se você armazena seus pacotes no SSISDB ou no sistema de arquivos, compartilhamentos de arquivos ou arquivos do Azure. Você também pode encontrar os IDs exclusivos das primeiras tarefas de encenação nos registros de execução de segundas tarefas de encenação. 

![ID único da primeira tarefa de encenação](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Use a autenticação do Windows em tarefas de preparação

Se as tarefas de estágio em seu IR auto-hospedado exigirem autenticação do Windows, [configure seus pacotes SSIS para usar a mesma autenticação do Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Suas tarefas de preparação serão invocadas com a conta de serviço de IR auto-hospedada *(NT SERVICE\DIAHostService,* por padrão), e seus armazenamentos de dados serão acessados com a conta de autenticação do Windows. Ambas as contas exigem que certas políticas de segurança sejam atribuídas a elas. Na máquina de IR auto-hospedada, vá para a **Política de Segurança Local Atribuição** > **de Direitos de Usuário de****Políticas** > locais e faça o seguinte:

1. Atribua as *cotas de memória de ajuste para um processo* e substitua as políticas de *token de nível de processo* à conta de serviço de RI auto-hospedada. Isso deve ocorrer automaticamente quando você instala seu IR auto-hospedado com a conta de serviço padrão. Se isso não funcionar, atribua essas políticas manualmente. Se você usar uma conta de serviço diferente, atribua as mesmas políticas a ela.

1. Atribuir o *Log on como uma* política de serviço para a conta de autenticação do Windows.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faturamento para a primeira e segunda tarefas de encenação

As primeiras tarefas de estágio executadas em seu RI auto-hospedado são cobradas separadamente, assim como todas as atividades de movimentação de dados que são executadas em um IR auto-hospedado são cobradas. Isso é especificado no artigo de preços do pipeline de dados da [Fábrica de Dados do Azure.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

As segundas tarefas de preparação que são executadas no seu IR Azure-SSIS não são cobradas separadamente, mas o seu IR Azure-SSIS em execução é cobrado conforme especificado no artigo [de preços do Azure-SSIS IR.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Como habilitar o TLS 1.2

Se você precisar usar criptografia forte/protocolo de rede mais seguro (TLS 1.2) e desativar versões SSL/TLS mais antigas em seu IR auto-hospedado, você pode baixar e executar o script *principal.cmd* que pode ser encontrado na pasta *CustomSetupScript/UserScenarios/TLS 1.2* do nosso contêiner de visualização pública.  Usando [o Azure Storage Explorer,](https://storageexplorer.com/)você pode se conectar ao nosso contêiner de visualização pública inserindo o seguinte Uri SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitações atuais

- Atualmente, são suportadas apenas as tarefas de fluxo de dados com o ODBC (Open Database Connectivity)/OLEDB/Flat File sources ou o destino OLEDB. 
- Atualmente, apenas os serviços vinculados ao armazenamento Azure Blob configurados com *a chave Account,* *o URI (SAS) (SAS) (SAS)* ou a autenticação *do Service Principal* são suportados no momento.
- *O mapeamento de parâmetros* na fonte OLEDB ainda não é suportado. Como solução de solução, use *o comando SQL From Variable* como *accessMode* e use *Expression* para inserir suas variáveis/parâmetros em um comando SQL. Como ilustração, consulte o pacote *ParameterMappingSample.dtsx* que pode ser encontrado na pasta *SelfHostedIRProxy/Limitações* do nosso contêiner de visualização pública. Usando o Azure Storage Explorer, você pode se conectar ao nosso contêiner de visualização pública inserindo o Uri SAS acima.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar seu IR auto-hospedado como um proxy para o seu IR Azure-SSIS, você pode implantar e executar seus pacotes para acessar dados no local como executar atividades do Pacote SSIS em pipelines de Fábrica de Dados. Para saber como, consulte [executar pacotes SSIS como executar atividades do pacote SSIS em pipelines de fábrica de dados](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
