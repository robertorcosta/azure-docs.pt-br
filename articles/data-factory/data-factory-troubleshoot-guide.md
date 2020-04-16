---
title: Solução de problemas Azure Data Factory | Microsoft Docs
description: Saiba como solucionar as atividades de controle externo na Fábrica de Dados do Azure.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c9a1ac831c4300c0523717fddc1fa53417068b89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416565"
---
# <a name="troubleshoot-azure-data-factory"></a>Solucionar problemas fábrica de dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de solução de problemas para atividades de controle externo na Fábrica de Dados Do Azure.

## <a name="connector-and-copy-activity"></a>Atividade do conector e cópia

Para obter problemas de conector, por exemplo, erro de encontro usando atividade de cópia, consulte [Os conectores de fábrica de dados do Azure](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Código de erro: 3200

- **Mensagem**: Erro 403.

- **Porque:**`The Databricks access token has expired.`

- **Recomendação**: Por padrão, o token de acesso Azure Databricks é válido por 90 dias. Crie um novo token e atualize o serviço vinculado.


### <a name="error-code--3201"></a>Código de erro: 3201

- **Mensagem:**`Missing required field: settings.task.notebook_task.notebook_path.`

- **Porque:**`Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: Especifique o caminho do notebook na atividade Databricks.

<br/>  

- **Mensagem:**`Cluster... does not exist.`

- **Porque:**`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se existe o cluster Databricks.

<br/>  

- **Mensagem:**`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Porque:**`Bad authoring.`

- **Recomendação**: Especifique caminhos absolutos `dbfs:/folder/subfolder/foo.py` para esquemas de endereçamento de espaço de trabalho ou para arquivos armazenados no Sistema de Arquivos Databricks.

<br/>  

- **Mensagem:**`{0} LinkedService should have domain and accessToken as required properties.`

- **Porque:**`Bad authoring.`

- **Recomendação**: Verifique a definição de [serviço vinculado.](compute-linked-services.md#azure-databricks-linked-service)

<br/>  

- **Mensagem:**`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Porque:**`Bad authoring.`

- **Recomendação**: Verifique a definição de [serviço vinculado.](compute-linked-services.md#azure-databricks-linked-service)

<br/>  

- **Mensagem:**`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Porque:**`Bad authoring.`

- **Recomendação**: Consulte a mensagem de erro.

<br/>

### <a name="error-code--3202"></a>Código de erro: 3202

- **Mensagem:**`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Porque:**`Too many Databricks runs in an hour.`

- **Recomendação**: Verifique todos os pipelines que utilizam este espaço de trabalho databricks para sua taxa de criação de empregos.  Se os pipelines lançarem muitos Databricks executados em conjunto, migraralguns pipelines para um novo espaço de trabalho.

<br/>  

- **Mensagem:**`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Porque:**`Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o pipeline JSON e certifique-se de que todos os parâmetros do caderno baseParâmetros especifiquem um valor não vazio.

<br/>  

- **Mensagem**: `User: `SimpleUserContext{userId=..., nome=user@company.com, orgId=...}` is not   authorized to access cluster.`

- **Causa**: O usuário que gerou o token de acesso não tem permissão para acessar o cluster Databricks especificado no serviço vinculado.

- **Recomendação**: Certifique-se de que o usuário tenha as permissões necessárias no espaço de trabalho.


### <a name="error-code--3203"></a>Código de erro: 3203

- **Mensagem:**`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa:** O cluster foi encerrado. Para clusters interativos, isso pode ser uma condição de raça.

- **Recomendação**: A melhor maneira de evitar esse erro é usar clusters de trabalho.


### <a name="error-code--3204"></a>Código de erro: 3204

- **Mensagem:**`Job execution failed.`

- **Causa**: As mensagens de erro indicam vários problemas, como um estado de cluster inesperado ou uma atividade específica. Na maioria das vezes nenhuma mensagem de erro aparece.

- **Recomendação**: N/A
            

## <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake

A tabela a seguir se aplica ao U-SQL.
      
### <a name="error-code--2709"></a>Código de erro: 2709

- **Mensagem:**`The access token is from the wrong tenant.`

- **Causa**: Incorreto Azure Active Directory (Azure AD) inquilino.

- **Recomendação**: Incorreto Azure Active Directory (Azure AD) inquilino.

<br/>

- **Mensagem:**`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa**: Este erro é causado pelo estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduza o número de empregos enviados ao Data Lake Analytics alterando gatilhos da Fábrica de Dados e configurações de concorrência nas atividades. Ou aumentar os limites do Data Lake Analytics.

<br/>  

- **Mensagem:**`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: Este erro é causado pelo estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduza o número de empregos enviados ao Data Lake Analytics alterando gatilhos da Fábrica de Dados e configurações de concorrência nas atividades. Ou aumentar os limites do Data Lake Analytics.


### <a name="error-code--2705"></a>Código de erro: 2705

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o usuário fornece para os trabalhos do Data Lake Analytics tenha acesso à conta Data Lake Analytics e à instância padrão de armazenamento do Lago de Dados da pasta raiz.


### <a name="error-code--2711"></a>Código de erro: 2711

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o usuário fornece para os trabalhos do Data Lake Analytics tenha acesso à conta Data Lake Analytics e à instância padrão de armazenamento do Lago de Dados da pasta raiz.

<br/>  

- **Mensagem:**`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**: O caminho para o arquivo U-SQL está errado, ou as credenciais de serviço vinculadas não têm acesso.

- **Recomendação**: Verifique o caminho e as credenciais fornecidas no serviço vinculado.


### <a name="error-code--2704"></a>Código de erro: 2704

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o usuário fornece para os trabalhos do Data Lake Analytics tenha acesso à conta Data Lake Analytics e à instância padrão de armazenamento do Lago de Dados da pasta raiz.


### <a name="error-code--2707"></a>Código de erro: 2707

- **Mensagem:**`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa**: A conta Data Lake Analytics no serviço vinculado está errada.

- **Recomendação**: Verifique se a conta certa está fornecida.


### <a name="error-code--2703"></a>Código de erro: 2703

- **Mensagem:**`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Causa**: O erro é do Data Lake Analytics.

- **Recomendação**: Um erro como o exemplo significa que o trabalho foi submetido ao Data Lake Analytics, e o script lá falhou. Investigue em Data Lake Analytics. No portal, vá até a conta Data Lake Analytics e procure o trabalho usando o ID de execução de atividade da Fábrica de Dados (não o ID de execução de pipeline). O trabalho lá fornece mais informações sobre o erro e irá ajudá-lo a solucionar problemas. Se a resolução não estiver clara, entre em contato com a equipe de suporte do Data Lake Analytics e forneça a URL do trabalho, que inclui o nome da sua conta e o ID do trabalho.
          

## <a name="azure-functions"></a>Funções do Azure

### <a name="error-code--3602"></a>Código de erro: 3602

- **Mensagem:**`Invalid HttpMethod: '%method;'.`

- **Causa**: O método http especificado na carga útil da atividade não é suportado pela Atividade de Função Azure.

- **Recomendação**: Os métodos http que são suportados são PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.


### <a name="error-code--3603"></a>Código de erro: 3603

- **Mensagem:**`Response Content is not a valid JObject.`

- **Causa**: A função Azure que foi chamada não retornou uma carga útil JSON na resposta. A atividade de função Do ADF Azure só suporta conteúdo de resposta JSON.

- **Recomendação**: Atualizar a função Azure para retornar uma carga útil JSON válida, por exemplo,\"uma\"função\"C#\"pode retornar (ActionResult)novo OkObjectResult("{ Id : 123 }");


### <a name="error-code--3606"></a>Código de erro: 3606

- **Mensagem**: Aatividade de função azure ausente chave de função ausente.

- **Causa**: A definição de atividade de função do Azure não está completa.

- **Recomendação**: Verifique a entrada AzureFunction atividade JSON definição tem propriedade chamada 'functionKey'.


### <a name="error-code--3607"></a>Código de erro: 3607

- **Mensagem:**`Azure function activity missing function name.`

- **Causa**: A definição de atividade de função do Azure não está completa.

- **Recomendação**: Verifique a entrada AzureFunction atividade JSON definição tem propriedade chamada 'functionName'.


### <a name="error-code--3608"></a>Código de erro: 3608

- **Mensagem:**`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa**: Os detalhes da função Azure na definição da atividade podem estar incorretos.

- **Recomendação**: Corrija os detalhes da função azul e tente novamente.


### <a name="error-code--3609"></a>Código de erro: 3609

- **Mensagem:**`Azure function activity missing functionAppUrl.`

- **Causa**: A definição de atividade de função do Azure não está completa.

- **Recomendação**: Verifique a entrada AzureFunction atividade JSON definição tem propriedade chamada 'functionAppUrl'.


### <a name="error-code--3610"></a>Código de erro: 3610

- **Mensagem:**`There was an error while calling endpoint.`

- **Causa**: A URL da função pode estar incorreta.

- **Recomendação**: Certifique-se de que o valor para 'functionAppUrl' na atividade JSON esteja correto e tente novamente.


### <a name="error-code--3611"></a>Código de erro: 3611

- **Mensagem:**`Azure function activity missing Method in JSON.`

- **Causa**: A definição de atividade de função do Azure não está completa.

- **Recomendação**: Verifique a entrada AzureFunction atividade JSON definição tem propriedade chamada 'método'.


### <a name="error-code--3612"></a>Código de erro: 3612

- **Mensagem:**`Azure function activity missing LinkedService definition in JSON.`

- **Causa**: A definição de atividade de função do Azure não está completa.

- **Recomendação**: Verifique a entrada AzureFunction atividade JSON definição tem detalhes de serviço vinculados.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Código de erro: 4101

- **Mensagem:**`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa**: Formato ruim ou definição ausente de propriedade '%propertyName;'.

- **Recomendação**: Verifique se a atividade '%activityName;' tem propriedade '%propertyName;' definida com dados corretos.


### <a name="error-code--4110"></a>Código de erro: 4110

- **Mensagem:**`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa**: AzureMLExecuteA definição de atividade do Pipeline não está completa.

- **Recomendação**: Verifique se a entrada AzureMLExecuteA atividade JSON atividade tem detalhes de serviço vinculados.


### <a name="error-code--4111"></a>Código de erro: 4111

- **Mensagem:**`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: Definição de atividade incorreta.

- **Recomendação**: Verifique se a entrada AzureMLExecuteA atividade JSON atividade tem detalhes corretos do serviço vinculado.


### <a name="error-code--4112"></a>Código de erro: 4112

- **Mensagem:**`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa**: Formato ruim ou definição ausente de propriedade '%propertyName;'.

- **Recomendação**: Verifique se o serviço vinculado tem propriedade '%propertyName;' definida com dados corretos.


### <a name="error-code--4121"></a>Código de erro: 4121

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial usada para acessar o Azure Machine Learning expirou.

- **Recomendação**: Verifique se a credencial é válida e tente novamente


### <a name="error-code--4122"></a>Código de erro: 4122

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial fornecida no Azure Machine Learning Linked Service é inválida ou não tem permissão para a operação.

- **Recomendação**: Verifique se a credencial no Linked Service é válida e tem permissão para acessar o Azure Machine Learning.


### <a name="error-code--4123"></a>Código de erro: 4123

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: Propriedades da atividade como pipelineOs parâmetros são inválidos para o gasoduto Azure ML.

- **Recomendação**: Verifique o valor das propriedades de atividade para corresponder à carga esperada do pipeline Azure ML publicado especificado no Linked Service.


### <a name="error-code--4124"></a>Código de erro: 4124

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: O ponto final do gasoduto Azure ML publicado não existe.

- **Recomendação**: Verifique o ponto final do pipeline de aprendizado de máquina publicado especificado no Linked Service existe no Azure Machine Learning.


### <a name="error-code--4125"></a>Código de erro: 4125

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: Erro do servidor no Azure Machine Learning.

- **Recomendação**: Por favor, tente novamente mais tarde. Entre em contato com a equipe de Aprendizado de Máquina do Azure para obter ajuda se o problema permanecer.


### <a name="error-code--4126"></a>Código de erro: 4126

- **Mensagem:**`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa:** A corrida do gasoduto Azure ML falhou.

- **Recomendação**: Verifique no Azure Machine Learning se há mais registros de erros e corrija o pipeline ML.



## <a name="common"></a>Comum

### <a name="error-code--2103"></a>Código de erro: 2103

- **Mensagem:**`Please provide value for the required property '%propertyName;'.`

- **Causa**: O valor do imóvel não foi fornecido, porém é exigido no cenário.

- **Recomendação**: Forneça o valor da mensagem e tente novamente.


### <a name="error-code--2104"></a>Código de erro: 2104

- **Mensagem:**`The type of the property '%propertyName;' is incorrect.`

- **Causa**: O tipo de propriedade fornecida não é como o esperado.

- **Recomendação**: Por favor, conserte o tipo da propriedade e tente novamente.


### <a name="error-code--2105"></a>Código de erro: 2105

- **Mensagem:**`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa**: O valor da propriedade é inválido ou não tem o formato esperado.

- **Recomendação**: Procure a documentação da propriedade e certifique-se de que o valor fornecido tenha o formato e o tipo esperados.


### <a name="error-code--2106"></a>Código de erro: 2106

- **Mensagem:**`The storage connection string is invalid. %errorMessage;`

- **Causa**: A seqüência de conexão para o armazenamento é inválida ou tem formato incorreto.

- **Recomendação**: Por favor, vá para o portal Azure, encontre seu armazenamento, copie a seqüência de conexões e cole em seu serviço vinculado e tente novamente.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: A solicitação falhou devido a um problema subjacente, como conectividade de rede, falha do DNS, validação do certificado do servidor ou tempo hábit.

- **Recomendação**: Use Violinista/Carteiro para validar a solicitação.


### <a name="error-code--2110"></a>Código de erro: 2110

- **Mensagem:**`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa**: O serviço vinculado especificado na atividade estava errado.

- **Recomendação**: Certifique-se de que o tipo de serviço vinculado é um dos tipos suportados para a atividade. Por exemplo, para atividades de HDI, o tipo de serviço vinculado pode ser HDInsight ou HDInsightOnDemand.


### <a name="error-code--2111"></a>Código de erro: 2111

- **Mensagem:**`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa**: O tipo de propriedade fornecida não é como o esperado.

- **Recomendação**: Por favor, conserte o tipo da propriedade e tente novamente.


### <a name="error-code--2112"></a>Código de erro: 2112

- **Mensagem:**`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa**: O tipo de nuvem não tem suporte ou não pode ser determinado para armazenamento a partir do EndpointSufix.

- **Recomendação**: Por favor, use o armazenamento em outra nuvem e tente novamente.


### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Conectividade de rede, falha de DNS, validação do certificado de servidor ou tempo há10.

- **Recomendação**: Valide que o ponto final que você está tentando acertar está respondendo às solicitações. Você pode usar ferramentas como Fiddler/Carteiro.



## <a name="custom"></a>Personalizado

A tabela a seguir se aplica ao Azure Batch.
      
### <a name="error-code--2500"></a>Código de erro: 2500

- **Mensagem:**`Hit unexpected exception and execution failed.`

- **Porque:**`Can't launch command, or the program returned an error code.`

- **Recomendação**: Certifique-se de que o arquivo executável existe. Se o programa começou, certifique-se de que *stdout.txt* e *stderr.txt* foram carregados para a conta de armazenamento. É uma boa prática para emitir registros abundantes em seu código para depuração.


### <a name="error-code--2501"></a>Código de erro: 2501

- **Mensagem:**`Cannot access user batch account; please check batch account settings.`

- **Causa**: Chave de acesso em lote incorreto ou nome do pool.

- **Recomendação**: Verifique o nome do pool e a chave de acesso em lote no serviço vinculado.


### <a name="error-code--2502"></a>Código de erro: 2502

- **Mensagem:**`Cannot access user storage account; please check storage account settings.`

- **Causa**: Nome da conta de armazenamento incorreto ou chave de acesso.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço vinculado.


### <a name="error-code--2504"></a>Código de erro: 2504

- **Mensagem:**`Operation returned an invalid status code 'BadRequest'.`

- **Causa:** Muitos arquivos na pastaPath da atividade personalizada. O tamanho total dos recursosFiles não pode ser superior a 32.768 caracteres.

- **Recomendação**: Remover arquivos desnecessários. Ou feche-os e adicione um comando unzip para extraí-los. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Código de erro: 2505

- **Mensagem:**`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa**: Atividades personalizadas suportam apenas contas de armazenamento que usam uma chave de acesso.

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2507"></a>Código de erro: 2507

- **Mensagem:**`The folder path does not exist or is empty: ...`

- **Causa**: Nenhum arquivo está na conta de armazenamento no caminho especificado.

- **Recomendação**: O caminho da pasta deve conter os arquivos executáveis que você deseja executar.


### <a name="error-code--2508"></a>Código de erro: 2508

- **Mensagem:**`There are duplicate files in the resource folder.`

- **Causa**: Vários arquivos de mesmo nome estão em diferentes subpastas do folderPath.

- **Recomendação**: Atividades personalizadas achatam a estrutura da pasta em folderPath.  Se você precisar preservar a estrutura da pasta, feche os arquivos e extraia-os no Azure Batch usando um comando unzip. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Código de erro: 2509

- **Mensagem:**`Batch   url ... is invalid; it must be in Uri format.`

- **Causa**: URLs de lote devem ser semelhantes a`https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2510"></a>Código de erro: 2510

- **Mensagem:**`An   error occurred while sending the request.`

- **Causa**: A URL do lote é inválida.

- **Recomendação**: Verifique a URL do lote.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Código de erro: 200

- **Mensagem:**`Unexpected error happened: '%error;'.`

- **Causa:** Há um problema de serviço interno.

- **Recomendação**: Entre em contato com o suporte da ADF para obter assistência adicional.


### <a name="error-code--201"></a>Código de erro: 201

- **Mensagem:**`JobType %jobType; is not found.`

- **Causa**: Há um novo tipo de trabalho que não é suportado pela ADF.

- **Recomendação**: Entre em contato com a equipe de suporte da ADF para obter mais assistência.


### <a name="error-code--202"></a>Código de erro: 202

- **Mensagem:**`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa:** A mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: A mensagem de erro deve ajudar a solucionar problemas. Se não houver informações suficientes, entre em contato com o suporte da ADF para obter mais ajuda.


### <a name="error-code--203"></a>Código de erro: 203

- **Mensagem:**`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa:** A mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: A mensagem de erro deve ajudar a solucionar problemas. Se não houver informações suficientes, entre em contato com o suporte da ADF para obter mais ajuda.


### <a name="error-code--204"></a>Código de erro: 204

- **Mensagem:**`The resumption token is missing for runId '%runId;'.`

- **Causa:** Há um problema de serviço interno.

- **Recomendação**: Entre em contato com o suporte da ADF para obter assistência adicional.


### <a name="error-code--205"></a>Código de erro: 205

- **Mensagem:**`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Causa**: Houve um erro ao criar o cluster HDI on demand.

- **Recomendação**: Entre em contato com o suporte da ADF para obter assistência adicional.


### <a name="error-code--206"></a>Código de erro: 206

- **Mensagem:**`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Causa:** Houve um problema interno com o serviço que causou isso.

- **Recomendação**: Pode ser uma questão transitória. Por favor, tente novamente o seu trabalho, e se o problema persistir, entre em contato com o suporte da ADF para obter mais assistência.


### <a name="error-code--207"></a>Código de erro: 207

- **Mensagem:**`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Causa**: Houve um erro interno ao tentar determinar a região a partir da conta de armazenamento principal.

- **Recomendação**: Tente outro armazenamento. Caso esta não seja uma solução aceitável, entre em contato com a equipe de suporte da ADF para obter mais assistência.


### <a name="error-code--208"></a>Código de erro: 208

- **Mensagem:**`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Causa**: Houve um erro interno ao tentar ler o Service Principal ou instanciar a autenticação msi.

- **Recomendação**: Por favor, considere fornecer um Diretor de Serviço que tenha permissões para criar um cluster HDInsight na assinatura fornecida e tente novamente. Caso essa não seja uma solução aceitável, entre em contato com a equipe de suporte da ADF para obter mais assistência.


### <a name="error-code--2300"></a>Código de erro: 2300

- **Mensagem:**`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'O nome remoto não pôde ser resolvido.', isso pode significar que o URI do cluster fornecido é inválido.


- **Recomendação**: Certifique-se de que o cluster não foi excluído e que o URI fornecido está correto. Quando você abre o URI em um navegador, você deve ver a interface do usuário ambari. Se o cluster estiver em uma rede virtual, o URI deve ser o URI privado. Para abri-lo, use uma VM que faz parte da mesma rede virtual. Para obter mais informações, consulte [este](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services) tópico.
                  

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'Uma tarefa foi cancelada.', isso significa que a submissão de trabalho foi desagendada.

- **Recomendação**: O problema pode ser a conectividade geral do HDInsight ou a conectividade de rede. Primeiro confirme que a interface do usuário HDInsight Ambari está disponível em qualquer navegador. Confirme se suas credenciais ainda são válidas. Se você estiver usando o IR (Self-Hosted Integrated Runtime, tempo de execução integrado) auto-hospedado, certifique-se de fazer isso a partir da VM ou da máquina onde o IR auto-hospedado está instalado. Em seguida, tente enviar o trabalho da Data Factory novamente. Se ainda falhar, entre em contato com a equipe da Fábrica de Dados para obter suporte.

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'O anúncio do usuário está bloqueado no Ambari' ou 'Não autorizado: o nome de usuário ou senha da Ambari está incorreto', isso significa que as credenciais do HDInsight estão incorretas ou expiraram.

- **Recomendação**: Corrija as credenciais e reimplante o serviço vinculado. Primeiro certifique-se de que as credenciais funcionam no HDInsight abrindo o URI do cluster em qualquer navegador e tentando fazer login. Se as credenciais não funcionarem, você pode redefini-las a partir do portal Azure.

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a '502 - o servidor da Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy', esse erro é retornado pelo serviço HDInsight.


- **Recomendação**: Veja a documentação de solução https://hdinsight.github.io/ambari/ambari-ui-502-error.htmlde https://hdinsight.github.io/spark/spark-thriftserver-errors.html https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502problemas do Azure HDInsight, por exemplo, , .
                  

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'Não é possível atender à solicitação de envio de trabalho, pois o serviço templeton está ocupado com muitas solicitações de emprego de envio' ou 'Queue root.joblauncher já tem 500 aplicativos, não pode aceitar submissão de solicitação', isso significa que muitos trabalhos estão sendo submetidos ao HDInsight ao mesmo tempo.

- **Recomendação**: Considere limitar o número de trabalhos simultâneos submetidos ao HDInsight. Consulte a atividade da Fábrica de Dados se os trabalhos estiverem sendo submetidos pela mesma atividade. Altere os gatilhos para que as corridas simultâneas do pipeline se espalhem ao longo do tempo. Consulte a documentação do HDInsight para ajustar templeton.parallellism.job.submit como o erro sugere.


### <a name="error-code--2301"></a>Código de erro: 2301

- **Mensagem:**`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa**: O cluster ou serviço HDInsight tem problemas.


- **Recomendação**: Esse erro acontece quando o ADF não recebe resposta do cluster HDInsight ao tentar obter o status do trabalho em execução. Pode ser causa de problemas no próprio cluster, ou o serviço HDInsight pode ter uma paralisação. Consulte a documentação de https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidesolução de problemas do HDInsight em , ou entre em contato com seu suporte para obter mais assistência.
                


### <a name="error-code--2302"></a>Código de erro: 2302

- **Mensagem:**`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster HDI e falhou lá.

- **Recomendação**: Siga o link logs de fio na execução de atividades Saída e procure os erros na saída de HDI. Entre em contato com a equipe do HDInsight para obter suporte, se necessário.


### <a name="error-code--2303"></a>Código de erro: 2303

- **Mensagem:**`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster HDI e falhou lá.

- **Recomendação**: Siga o link logs de fio na execução de atividades Saída e procure os erros na saída de HDI. Tente novamente ou entre em contato com a equipe do HDInsight para obter suporte, se necessário.


### <a name="error-code--2304"></a>Código de erro: 2304

- **Mensagem:**`MSI authentication is not supported on storages for HDI activities.`

- **Causa**: Os serviços vinculados de armazenamento usados no serviço vinculado ao HDI ou na atividade HDI são configurados com autenticação MSI que não é suportada.

- **Recomendação**: Forneça strings de conexão completas para contas de armazenamento usadas no serviço vinculado ao HDI ou na atividade de HDI.


### <a name="error-code--2305"></a>Código de erro: 2305

- **Mensagem:**`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: As informações de conexão para o cluster HDI estão incorretas, o usuário fornecido não tem permissões para executar a ação necessária ou o serviço HDInsight teve problemas em responder às solicitações do ADF.

- **Recomendação**: Certifique-se de que as informações do usuário estão corretas. Verifique também se a Interface do Ambari para o cluster HDI pode ser aberta em um navegador da VM onde o IR está instalado no caso de IR auto-hospedado, ou pode ser aberta a partir de qualquer máquina no caso do Azure IR.


### <a name="error-code--2306"></a>Código de erro: 2306

- **Mensagem:**`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: O json fornecido para a ação do script é inválido.


- **Recomendação**: A mensagem de erro deve ajudar a identificar o problema. Por favor, conserte a configuração json e tente novamente. Procure https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service mais informações.
                


### <a name="error-code--2310"></a>Código de erro: 2310

- **Mensagem:**`Failed to submit Spark job. Error: '%message;'`

- **Causa**: A ADF tentou criar um lote em um cluster Spark usando a API Livy (livy/batch), mas recebeu um erro.

- **Recomendação**: Siga a mensagem de erro para corrigir o problema. Se não houver informações suficientes para resolvê-lo, entre em contato com a equipe do HDI e forneça-lhes o ID de lote e o ID de trabalho que podem ser encontrados na página de execução de atividades Saída na página de monitoramento do ADF.


### <a name="error-code--2312"></a>Código de erro: 2312

- **Mensagem:**`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa**: O trabalho falhou no cluster HDInsight Spark.

- **Recomendação**: Siga os links na página de execução de atividades Saída na página de monitoramento do ADF para solucionar problemas no cluster HDInsight Spark. Entre em contato com a equipe de suporte da HDInsight para obter mais assistência.


### <a name="error-code--2313"></a>Código de erro: 2313

- **Mensagem:**`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa**: O lote foi excluído no cluster HDInsight Spark.

- **Recomendação**: Solucionar problemas em lotes no cluster HDInsight Spark. Entre em contato com o suporte do HDInsight para obter mais assistência. 


### <a name="error-code--2328"></a>Código de erro: 2328

- **Mensagem:**`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Porque:**`The error message should show the details of what went wrong.`

- **Recomendação**: A mensagem de erro deve ajudar a solucionar problemas.


### <a name="error-code--2329"></a>Código de erro: 2329

- **Mensagem:**`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa:** A mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: A mensagem de erro deve ajudar a solucionar problemas.


### <a name="error-code--2331"></a>Código de erro: 2331

- **Mensagem:**`The file path should not be null or empty.`

- **Causa:** O caminho do arquivo fornecido está vazio.

- **Recomendação**: Por favor, forneça um caminho para um arquivo que existe.


### <a name="error-code--2340"></a>Código de erro: 2340

- **Mensagem:**`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa**: O serviço vinculado HDInsightOnDemand não suporta execução via IR AutoHosted.

- **Recomendação**: Selecione um IR do Azure e tente novamente.


### <a name="error-code--2341"></a>Código de erro: 2341

- **Mensagem:**`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa**: A URL fornecida não está no formato correto.

- **Recomendação**: Por favor, corrija a URL do cluster e tente novamente.


### <a name="error-code--2342"></a>Código de erro: 2342

- **Mensagem:**`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: As credenciais fornecidas estão erradas para o cluster ou houve uma configuração de rede ou problema de conexão ou o IR está tendo problemas de conexão ao cluster.

- **Recomendação**:  
      1. Verifique se as credenciais estão corretas abrindo a interface do usuário ambari do cluster HDInsight em um navegador.
      2. Se o cluster estiver no VNet e o IR auto-hospedado estiver sendo usado, a URL HDI deve ser a URL privada em VNets, o que significa que ele deve ter '-int' após o nome do cluster. Por exemplo, ""https://mycluster.azurehdinsight.net/devehttps://mycluster-int.azurehdinsight.net/ser mudado para ".
      2. Se o cluster estiver no VNet, o IR auto-hospedado estiver sendo usado e a URL privada for usada e a conexão ainda falhar, a VM onde o IR está instalado teve problemas de conexão com o HDI. Conecte-se à VM onde o IR está instalado e abra a Interface do Ambari em um navegador. Use a URL privada para o cluster. Esta conexão deve funcionar a partir do navegador. Caso isso não aconteça, entre em contato com a equipe de suporte do HDInsight para obter mais assistência.
      3. Se o IR auto-hospedado não estiver sendo usado, então o cluster HDI deve ser acessível publicamente. Abra a interface do ambari em um navegador e certifique-se de que ela seja aberta. Se houver algum problema com o cluster ou os serviços nele, entre em contato com a equipe de suporte do HDInsight para obter assistência.
      Assim, em geral, a URL de cluster HDI usada no serviço vinculado ao ADF deve estar acessível para ODF IR (auto-hosted ou Azure) para que a conexão de teste passe e para que as corridas funcionem. Isso pode ser facilmente verificado abrindo essa URL de um navegador da VM ou de qualquer máquina pública.
    


### <a name="error-code--2343"></a>Código de erro: 2343

- **Mensagem:**`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa:** O nome de usuário ou a senha estão vazios.

- **Recomendação:** Forneça credenciais corretas para conectar-se ao HDI e tentar novamente.


### <a name="error-code--2345"></a>Código de erro: 2345

- **Mensagem:**`Failed to read the content of the hive script. Error: '%message;'`

- **Causa**: O arquivo de script não existe ou o ADF não poderia se conectar à localização do script.

- **Recomendação**: Verifique se o script existe e o serviço vinculado associado tem credenciais adequadas para conexão.


### <a name="error-code--2346"></a>Código de erro: 2346

- **Mensagem:**`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa**: A ADF tentou estabelecer uma conexão ODBC com o cluster HDI e falhou com o erro.

- **Recomendação**: A mensagem de erro e o código de erro devem ajudar a solucionar os erros de conexão ODBC. Caso eles não sejam suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte.


### <a name="error-code--2347"></a>Código de erro: 2347

- **Mensagem:**`Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: A ADF submeteu o script de colmeia para execução ao cluster HDI via conexão ODBC e o script falhou no HDI.

- **Recomendação**: A execução do script colmeia falhou no cluster HDI e a mensagem de erro e o código de erro devem ajudar na solução de problemas. Caso eles não sejam suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte.


### <a name="error-code--2348"></a>Código de erro: 2348

- **Mensagem:**`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa**: As propriedades de serviço vinculadas ao armazenamento não estão definidas corretamente.

- **Recomendação**: Apenas as strings de conexão completas são suportadas no serviço principal vinculado ao armazenamento para atividades de HDI. Certifique-se de que não está usando auth MSI ou aplicativos.


### <a name="error-code--2350"></a>Código de erro: 2350

- **Mensagem:**`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: As credenciais fornecidas para se conectar ao armazenamento onde os arquivos devem ser localizados estão incorretas, ou os arquivos não existem lá.

- **Recomendação**: Este erro acontece quando a ADF faz etapas de preparação para atividades de IDH. Ele tenta copiar arquivos para o armazenamento principal antes de enviar o trabalho para o HDI. Certifique-se de que os arquivos existem no local fornecido, a conexão de armazenamento está correta. As atividades do ADF HDI não suportam a autenticação msi em contas de armazenamento relacionadas a atividades de HDI, portanto, certifique-se de que esses serviços vinculados tenham chaves completas ou estejam usando o Azure Key Vault.


### <a name="error-code--2351"></a>Código de erro: 2351

- **Mensagem:**`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa**: O arquivo não existe no caminho especificado.

- **Recomendação**: Verifique se o arquivo realmente existe, e o serviço vinculado com informações de conexão apontando para este arquivo tem credenciais corretas.


### <a name="error-code--2352"></a>Código de erro: 2352

- **Mensagem:**`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa**: As propriedades de serviço vinculadas ao armazenamento de arquivos não estão definidas corretamente.

- **Recomendação**: Certifique-se de que as propriedades do serviço vinculado ao armazenamento de arquivos estão configuradas corretamente.


### <a name="error-code--2353"></a>Código de erro: 2353

- **Mensagem:**`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa**: As propriedades de serviço vinculadas ao armazenamento de script não estão definidas corretamente.

- **Recomendação**: Certifique-se de que as propriedades do serviço vinculado ao armazenamento de script estão configuradas corretamente.


### <a name="error-code--2354"></a>Código de erro: 2354

- **Mensagem:**`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa**: O tipo de serviço vinculado ao armazenamento não é suportado pela atividade.

- **Recomendação**: Certifique-se de que o serviço vinculado selecionado tenha um dos tipos suportados para a atividade. As atividades do HDI suportam serviços vinculados ao AzureBlobStorage e ao AzureBlobFSStorage.


### <a name="error-code--2355"></a>Código de erro: 2355

- **Mensagem:**`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: O ambiente de comando fornecido está incorreto.

- **Recomendação**:  
      Certifique-se de que o valor \"fornecido\"é \"semelhante a:\" commandEnvironment : [ variableName=variableValue ] E cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2356"></a>Código de erro: 2356

- **Mensagem:**`The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: A variável foi fornecida duas vezes no comandoAmbiente .

- **Recomendação**:  
      Certifique-se de que o valor \"fornecido\"é \"semelhante a:\" commandEnvironment : [ variableName=variableValue ] E cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2357"></a>Código de erro: 2357

- **Mensagem:**`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa:** As credenciais fornecidas estão incorretas.

- **Recomendação**: Verifique as informações de conexão no serviço vinculado ao ADLS Gen 1 e certifique-se de que a conexão de teste seja bem sucedida.


### <a name="error-code--2358"></a>Código de erro: 2358

- **Mensagem:**`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: O valor fornecido para a propriedade necessária 'TimeToLive' tem formato inválido. 

- **Recomendação**: Atualize o valor para estar na faixa sugerida e tente novamente.


### <a name="error-code--2359"></a>Código de erro: 2359

- **Mensagem:**`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: O valor fornecido para os 'papéis' da propriedade é inválido.

- **Recomendação**: Atualize o valor para ser uma das sugestões e tente novamente.


### <a name="error-code--2360"></a>Código de erro: 2360

- **Mensagem:**`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa**: A seqüência de conexão fornecida para o HCatalogLinkedService é inválida.

- **Recomendação**: Atualize o valor para uma seqüência de conexão Azure SQL correta e tente novamente.


### <a name="error-code--2361"></a>Código de erro: 2361

- **Mensagem:**`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa**: A criação de cluster falhou e o ADF não teve um erro de volta do serviço HDInsight.

- **Recomendação**: Abra o portal Dozure e tente encontrar o recurso HDI com o nome fornecido e verifique o status do provisionamento. Entre em contato com a equipe de suporte da HDInsight para obter mais assistência.


### <a name="error-code--2362"></a>Código de erro: 2362

- **Mensagem:**`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa**: O armazenamento adicional fornecido não foi o armazenamento DoZure Blob.

- **Recomendação**: Forneça a conta de armazenamento Azure Blob como um armazenamento adicional para o hdinsight sob demanda.



## <a name="web-activity"></a>Atividade da Web

### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Conectividade de rede, falha de DNS, validação do certificado de servidor ou tempo há10.

- **Recomendação**: Valide que o ponto final que você está tentando acertar está respondendo às solicitações. Você pode usar ferramentas como Fiddler/Carteiro.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: A solicitação falhou devido a um problema subjacente, como conectividade de rede, falha do DNS, validação do certificado do servidor ou tempo hábit.

- **Recomendação**: Use Violinista/Carteiro para validar a solicitação.
<br>


#### <a name="more-details"></a>Mais detalhes
Para usar o Fiddler para criar uma sessão HTTP do aplicativo web monitorado:

1. Baixe, instale e abra [o Fiddler](https://www.telerik.com/download/fiddler).

1. Se o seu aplicativo web usar HTTPS, vá para **Tools** > **Fiddler Options** > **HTTPS**. Selecione **Capturar CONNECTs HTTPS** e **Descriptografar o tráfego HTTPS**.

   ![Opções de violinista](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se o aplicativo usar certificados TLS/SSL, adicione o certificado Fiddler ao seu dispositivo. Vá para **ferramentas** > **opções de fiddler HTTPS** > **HTTPS** > **Ações** > **exportar certificado raiz para desktop**.

1. Desative a captura indo para **File** > **Capture Traffic**. Ou pressione **F12**.

1. Limpe o cache do seu navegador para que todos os itens armazenados em cache sejam removidos e devem ser baixados novamente.

1. Crie uma solicitação:

   1. Selecione a guia **Compositor.**

   1. Defina o método HTTP e a URL.
   
   1. Adicione cabeçalhos e um corpo de solicitação, se necessário.

   1. Selecione **Executar**.

1. Ative a captura de tráfego novamente e complete a transação problemática em sua página.

1. Vá para **Registrar** > **Salvar** > **Todas as Sessões**.

Para obter mais informações, consulte [Getting started with Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda para solucionar problemas, tente esses recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recursos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum Stack Overflow para Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)


            
