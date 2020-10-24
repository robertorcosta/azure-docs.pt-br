---
title: Segurança do Enterprise
titleSuffix: Azure Machine Learning
description: 'Use o Azure Machine Learning com segurança: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: fef41a177f653dc67835897a48d734400a37a0d0
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496011"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Segurança empresarial para o Azure Machine Learning

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis para o Azure Machine Learning.

Ao usar um serviço de nuvem, um tipo de melhor prática é restringir o acesso somente aos usuários que precisam dele. Comece entendendo o modelo de autenticação e autorização usado pelo serviço. É possível que você também queira restringir o acesso à rede ou unir recursos de modo seguro em sua rede local com a nuvem. A criptografia de dados também é vital, tanto em repouso quanto durante o tráfego de dados entre os serviços. Por fim, você precisa ser capaz de monitorar o serviço e produzir um log de auditoria de todas as atividades.

> [!NOTE]
> As informações presentes neste artigo funcionam com o SDK do Python do Azure Machine Learning versão 1.0.83.1 ou superior.

## <a name="authentication"></a>Autenticação

A autenticação multifator terá suporte se o Azure AD (Azure Active Directory) estiver configurado para usá-la. Este é o processo de autenticação:

1. O cliente entra no Azure AD e obtém um token do Azure Resource Manager.  Há suporte total para os usuários e para as entidades de serviço.
1. O cliente apresenta o token para o Azure Resource Manager e para todos os Azure Machine Learning.
1. O serviço do Machine Learning fornece um token de serviço do Machine Learning para o destino de computação do usuário (por exemplo, Computação do Machine Learning). Esse token é usado pelo destino de computação do usuário para retornar ao serviço do Machine Learning depois de a execução ter sido concluída. O escopo é limitado ao workspace.

[![Autenticação no Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Para obter mais informações, consulte [Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning](how-to-setup-authentication.md). Este artigo apresenta informações e exemplos de autenticação, incluindo o uso de entidades de serviço e fluxos de trabalho automatizados.

### <a name="authentication-for-web-service-deployment"></a>Autenticação para a implantação de serviço Web

O Azure Machine Learning tem suporte para duas formas de autenticação de serviços Web: chave e token. Um serviço Web só pode habilitar uma forma de autenticação de cada vez.

|Método de autenticação|Descrição|Instâncias de Contêiner do Azure|AKS|
|---|---|---|---|
|Chave|As chaves são estáticas e não precisam ser atualizadas. As chaves podem ser regeneradas manualmente.|Desabilitadas por padrão| Habilitado por padrão|
|Token|Os tokens expiram após um período especificado e precisam ser atualizados.| Não disponível| Desabilitado por padrão |

Para obter exemplos de código, consulte a [seção Autenticação de serviço Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorização

Você pode criar vários workspaces, e cada workspace pode ser compartilhado por várias pessoas. Ao compartilhar um workspace, controle o acesso ao atribuir estas funções para os usuários:

* Proprietário
* Colaborador
* Leitor

A tabela a seguir lista algumas das principais operações do Azure Machine Learning e as funções que podem executá-las:

| Operação do Azure Machine Learning | Proprietário | Colaborador | Leitor |
| ---- |:----:|:----:|:----:|
| Criar workspace | ✓ | ✓ | |
| Compartilhar o workspace | ✓ | |  |
| Criar destino de computação | ✓ | ✓ | |
| Anexar destino de computação | ✓ | ✓ | |
| Anexar armazenamento de dados | ✓ | ✓ | |
| Executar o experimento | ✓ | ✓ | |
| Exibir execuções/métricas | ✓ | ✓ | ✓ |
| Registrar modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Exibir modelos/imagens | ✓ | ✓ | ✓ |
| Chamar serviço Web | ✓ | ✓ | ✓ |

Caso as funções internas não atendam às suas necessidades, você poderá criar funções personalizadas. As funções personalizadas têm suporte para controlar todas as operações dentro de um espaço de trabalho, como a criação de uma computação, o envio de uma execução, o registro de um armazenamento de um datastore ou a implantação de um modelo. As funções personalizadas podem ter permissões de leitura, gravação ou exclusão nos vários recursos de um espaço de trabalho, como clusters, repositórios de armazenamento, modelos e pontos de extremidade. Você pode tornar a função disponível em níveis específicos do workspace, do grupo de recursos ou da assinatura. Para obter mais informações, consulte [Gerenciar usuários e funções em um workspace de Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> O Azure Machine Learning tem suporte com a colaboração entre empresas do Azure Active Directory, mas ainda não tem suporte com a colaboração entre consumidores do Azure Active Directory.

### <a name="securing-compute-targets-and-data"></a>Proteção a dados e destinos de computação

Proprietários e colaboradores podem usar todos os destinos de computação e armazenamentos de dados anexados ao workspace.  

Cada workspace também tem uma identidade gerenciada associada atribuída pelo sistema que tem o mesmo nome do workspace. A identidade gerenciada tem as permissões a seguir nos recursos anexados usados no workspace.

Para obter mais informações sobre identidades gerenciadas, consulte [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Recurso | Permissões |
| ----- | ----- |
| Workspace | Colaborador |
| Conta de armazenamento | Colaborador de dados de blob de armazenamento |
| Cofre de chaves | Acesso a todas as chaves, segredos, certificados |
| Registro de Contêiner do Azure | Colaborador |
| Grupo de recursos que contém os workspace | Colaborador |
| Grupo de recursos que contém o cofre de chaves (caso seja diferente daquele que contém o workspace) | Colaborador |

Não recomendamos que os administradores revoguem o acesso da identidade gerenciada dos recursos mencionados na tabela anterior. É possível restaurar o acesso usando a operação de ressincronização de chaves.

O Azure Machine Learning cria um aplicativo adicional (o nome começa com `aml-` ou `Microsoft-AzureML-Support-App-`) que tenha acesso no nível de colaborador em sua assinatura para cada região do workspace. Por exemplo, caso você tenha um workspace no Leste dos EUA e outro no Norte da Europa na mesma assinatura, verá dois desses aplicativos. Esses aplicativos habilitam o Azure Machine Learning a ajudar você a gerenciar recursos de computação.

## <a name="network-security"></a>Segurança de rede

O Azure Machine Learning depende de outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Você pode criar esses destinos de computação em uma rede virtual. Por exemplo, você pode usar a Máquina Virtual de Ciência de Dados do Azure para treinar um modelo e implantá-lo no AKS.  

Para obter mais informações, confira [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md).

Você também pode habilitar o Link Privado do Azure para seu workspace. O Link Privado permite restringir as comunicações ao seu workspace a partir de uma Rede Virtual do Azure. Para obter mais informações, consulte [Como configurar o Link Privado](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Criptografia de dados

> [!IMPORTANT]
> Para a criptografia de nível de produção durante o __treinamento__, a Microsoft recomenda usar Azure Machine Learning cluster de computação. Para a criptografia de nível de produção durante a __inferência__, a Microsoft recomenda usar o serviço kubernetes do Azure.
>
> Azure Machine Learning instância de computação é um ambiente de desenvolvimento/teste. Ao usá-lo, recomendamos que você armazene seus arquivos, como blocos de anotações e scripts, em um compartilhamento de arquivos. Seus dados devem ser armazenados em um datastore.

### <a name="encryption-at-rest"></a>Criptografia em repouso

> [!IMPORTANT]
> Caso seu workspace contenha dados confidenciais, recomendamos definir o [sinalizador hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) ao criar seu workspace. O `hbi_workspace` sinalizador só pode ser definido quando um espaço de trabalho é criado. Ele não pode ser alterado para um espaço de trabalho existente.

O `hbi_workspace` sinalizador controla a quantidade de [dados que a Microsoft coleta para fins de diagnóstico](#microsoft-collected-data) e habilita a [criptografia adicional em ambientes gerenciados pela Microsoft](../security/fundamentals/encryption-atrest.md). Além disso, ele habilita as seguintes ações:

* Inicia a criptografia do disco de rascunho local em seu Azure Machine Learning cluster de computação, desde que você não tenha criado nenhum cluster anterior nessa assinatura. De outro modo, você precisará gerar um tíquete de suporte para habilitar a criptografia do disco de rascunho dos seus clusters de computação 
* Limpa o disco de rascunho local entre as execuções
* Passa com segurança credenciais para sua conta de armazenamento, registro de contêiner e conta SSH da camada de execução para seus clusters de computação usando o cofre de chaves
* Habilita a filtragem de IP para garantir que os pools de lote subjacentes não possam ser chamados por nenhum serviço externo que não seja o AzureMachineLearningService

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Azure Machine Learning armazena instantâneos, a saída e logs na conta de armazenamento de blobs do Azure que está vinculada ao workspace do Azure Machine Learning e à sua assinatura. Todos os dados armazenados no armazenamento de blobs do Azure são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para obter informações sobre como usar suas próprias chaves para dados armazenados no armazenamento de blobs do Azure, consulte [Criptografia do Armazenamento do Microsoft Azure com chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Geralmente, os dados de treinamento também são armazenados no armazenamento de blobs do Azure para que sejam acessíveis ao treinamento de destinos de computação. Esse armazenamento não é gerenciado pelo Azure Machine Learning, ele é montado em destinos de computação como um sistema de arquivos remoto.

Caso precise __girar ou revogar__ sua chave, você poderá fazer isso a qualquer momento. Ao girar uma chave, a conta de armazenamento será iniciada por meio de uma nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desabilitar) uma chave, a conta de armazenamento cuida das solicitações com falha. Geralmente, leva uma hora para que a rotação ou a revogação entrem em vigor.

Para obter informações sobre como regenerar as chaves de acesso, consulte [Regenerar chaves de acesso de armazenamento](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

O Azure Machine Learning armazena métricas e metadados em uma instância do Azure Cosmos DB. Essa instância é associada a uma assinatura da Microsoft gerenciada pelo Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar a instância do Azure Cosmos DB, você pode criar uma instância do Cosmos DB dedicada para usar no seu workspace. Recomendamos essa abordagem caso você queira armazenar seus dados, como informações de histórico de execução, fora da instância de Cosmos DB multilocatária hospedada em nossa assinatura da Microsoft. 

Para habilitar o provisionamento de uma instância do Cosmos DB em sua assinatura com chaves gerenciadas pelo cliente, execute as seguintes ações:

* Registre os provedores de recursos Microsoft. MachineLearning e Microsoft.DocumentDB em sua assinatura, se ainda não tiver feito isso.

* Use os parâmetros a seguir ao criar o workspace do Azure Machine Learning. Ambos os parâmetros são obrigatórios e têm suporte para SDK, CLI, APIs REST e modelos do Resource Manager.

    * `resource_cmk_uri`: Esse parâmetro é o URI do recurso completo da chave gerenciada pelo cliente no cofre de chaves, incluindo as [informações de versão para a chave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Esse parâmetro é a ID do recurso do cofre de chaves em sua assinatura. O cofre de chaves precisa estar na mesma região e assinatura que você usará para o workspace do Azure Machine Learning. 
    
        > [!NOTE]
        > Essa instância do cofre de chaves pode ser diferente do cofre de chaves criado pelo Azure Machine Learning quando você provisionar o workspace. Caso queira usar a mesma instância do cofre de chaves para o workspace, passe o mesmo cofre de chaves ao provisionar o workspace usando o [parâmetro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Essa instância de Cosmos DB é criada em um grupo de recursos gerenciado pela Microsoft em sua assinatura, juntamente com os recursos necessários. O nome do grupo de recursos gerenciados é dado no formato `<AML Workspace Resource Group Name><GUID>`. Se seu espaço de trabalho do Azure Machine Learning usar um ponto de extremidade privado, uma rede virtual também será criada para a instância de Cosmos DB. Essa VNet é usada para proteger a comunicação entre Cosmos DB e Azure Machine Learning.

> [!IMPORTANT]
> * Não exclua o grupo de recursos que contém essa Cosmos DB instância ou qualquer um dos recursos criados automaticamente neste grupo. Se precisar excluir o grupo de recursos, Cosmos DB instância, etc., você deverá excluir o espaço de trabalho Azure Machine Learning que o utiliza. O grupo de recursos, Cosmos DB instância e outros recursos criados automaticamente são excluídos quando o espaço de trabalho associado é excluído.
> * O padrão [__Unidades de Solicitação__](../cosmos-db/request-units.md) dessa conta do Cosmos DB é definido em __8000__. Não há suporte para a alteração desse valor.
> * Você não pode fornecer sua própria VNet para uso com a instância de Cosmos DB que é criada. Você também não pode modificar a rede virtual. Por exemplo, você não pode alterar o intervalo de endereços IP que ele usa.

Caso precise __girar ou revogar__ sua chave, você poderá fazer isso a qualquer momento. Ao girar uma chave, o Cosmos DB será iniciado por meio de uma nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desabilitar) uma chave, o Cosmos DB cuida das solicitações com falha. Geralmente, leva uma hora para que a rotação ou a revogação entrem em vigor.

Para obter mais informações sobre chaves gerenciadas pelo cliente com o Cosmos DB, consulte [Configurar chaves gerenciadas pelo cliente para sua conta do Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Todas as imagens de contêiner no seu registro (Registro de Contêiner do Azure) são criptografadas em repouso. O Azure criptografa uma imagem automaticamente antes de armazená-la e a descriptografa quando o Azure Machine Learning efetua o pull da imagem.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar o Registro de Contêiner do Azure, você precisa criar seu próprio ACR e anexá-lo enquanto estiver provisionando o workspace ou criptografar a instância padrão que é criada no momento do provisionamento do workspace.

> [!IMPORTANT]
> Azure Machine Learning requer que a conta de administrador seja habilitada no registro de contêiner do Azure. Por padrão, essa configuração é desabilitada quando você cria um registro de contêiner. Para obter informações sobre como habilitar a conta de administrador, consulte [conta de administrador](/azure/container-registry/container-registry-authentication#admin-account).
>
> Após a criação de um Registro de Contêiner do Azure para um workspace, não o exclua. Fazer isso interromperá o workspace do Azure Machine Learning.

Para obter um exemplo de como criar um workspace usando um Registro de Contêiner do Azure existente, consulte os seguintes artigos:

* [Criar um workspace para o Azure Machine Learning com a CLI do Azure](how-to-manage-workspace-cli.md).
* [Crie um espaço de trabalho com o SDK do Python](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Usar um modelo do Azure Resource Manager para criar um workspace para o Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

Você pode criptografar um recurso da ACI (Instância de Contêiner do Azure) implantada usando chaves gerenciadas pelo cliente. A chave gerenciada pelo cliente usada para a ACI pode ser armazenada no Azure Key Vault para seu workspace. Para obter informações sobre como gerar uma chave, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para usar a chave ao implantar um modelo na ACI, crie uma nova configuração de implantação usando `AciWebservice.deploy_configuration()`. Forneça as informações da chave usando os seguintes parâmetros:

* `cmk_vault_base_url`: a URL do cofre de chaves que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: a versão da chave.

Para obter mais informações sobre como criar e usar uma configuração de implantação, consulte os seguintes artigos:

* Referência [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Onde e como implantar](how-to-deploy-and-where.md)
* [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre como usar uma chave gerenciada pelo cliente com a ACI, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Você pode criptografar um recurso do Serviço de Kubernetes do Azure implantada usando chaves gerenciadas pelo cliente. Para obter mais informações, consulte [Traga suas próprias chaves com o Serviço de Kubernetes do Azure](../aks/azure-disk-customer-managed-keys.md). 

Esse processo permite que você criptografe os dados e o disco do SO das máquinas virtuais implantadas no cluster do Kubernetes.

> [!IMPORTANT]
> Esse processo funciona apenas com o AKS K8s versão 1.17 ou superior. O Azure Machine Learning agora tem suporte para o AKS 1.17 em 13 de janeiro de 2020.

#### <a name="machine-learning-compute"></a>Computação do Machine Learning

O disco do SO para cada nó de computação armazenado no Armazenamento do Microsoft Azure é criptografado com chaves gerenciadas pela Microsoft em contas de armazenamento do Azure Machine Learning. Esse destino de computação é efêmero e, normalmente, os clusters são reduzidos quando não há execuções na fila. A máquina virtual subjacente é desprovisionada e o disco do SO é excluído. Não há suporte para o Azure Disk Encryption do disco do SO.

Cada máquina virtual também tem um disco temporário local para operações do SO. Caso deseje, você pode usar o disco para preparar os dados de treinamento. O disco é criptografado por padrão para workspaces com o parâmetro `hbi_workspace` definido como `TRUE`. Esse ambiente tem uma curta duração e apenas durante a execução, e o suporte à criptografia é limitado apenas a chaves gerenciadas pelo sistema.

#### <a name="azure-databricks"></a>Azure Databricks

O Azure Databricks pode ser usado em pipelines de Machine Learning. Por padrão, o DBFS (Sistema de Arquivos do Databricks) usado pelo Azure Databricks é criptografado usando uma chave gerenciada pela Microsoft. Para configurar o Azure Databricks para usar chaves gerenciadas pelo cliente, consulte [Configurar chaves gerenciadas pelo cliente no DBFS padrão (raiz)](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Criptografia em trânsito

O Azure Machine Learning usa o TLS para proteger a comunicação interna entre vários microsserviços do Azure Machine Learning. Todo o acesso ao Armazenamento do Microsoft Azure também ocorre em um canal seguro.

Para proteger chamadas externas feitas ao ponto de extremidade de pontuação, Azure Machine Learning usa TLS. Para obter mais informações, consulte [Usar o TLS para proteger um serviço Web por meio do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Como usar o Azure Key Vault

O Azure Machine Learning usa a instância do Azure Key Vault associada ao workspace para armazenar credenciais de vários tipos:

* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do Repositório do Contêiner do Azure
* Cadeias de conexão para armazenamento de dados

As senhas e chaves SSH para computar destinos como o Azure HDInsight e VMs são armazenadas em um cofre de chaves separado associado à assinatura da Microsoft. O Azure Machine Learning não armazena nenhuma senha ou chave fornecida por usuários. Em vez disso, ele gera, autoriza e armazena suas próprias chaves SSH para conectar às VMs e ao HDInsight para executar os experimentos.

Cada workspace tem uma identidade gerenciada associada atribuída pelo sistema que tem o mesmo nome do workspace. Essa identidade gerenciada tem acesso a todas as chaves, segredos e certificados presentes no cofre de chaves.

## <a name="data-collection-and-handling"></a>Coleta e tratamento de dados

### <a name="microsoft-collected-data"></a>Dados coletados da Microsoft

A Microsoft pode coletar informações de identificação que não são de usuário, como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome do experimento de machine learning) ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos esses dados são armazenados usando chaves gerenciadas pela Microsoft no armazenamento hospedado em assinaturas de propriedade da Microsoft e seguem [padrões de tratamento de dados e políticas de privacidade padrão da Microsoft](https://privacy.microsoft.com/privacystatement).

A Microsoft também recomenda não armazenar informações confidenciais (como segredos de chave de conta) em variáveis de ambiente. As variáveis de ambiente são registradas, criptografadas e armazenadas pela Microsoft. De modo semelhante, ao nomear a [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true), evite incluir informações confidenciais, como nomes de usuário ou nomes de projetos secretos. Essas informações podem ser exibidas nos logs de telemetria acessíveis a engenheiros de Suporte da Microsoft.

Para recusar os dados de diagnóstico sendo coletados, defina o parâmetro `hbi_workspace` como `TRUE` ao provisionar o workspace. Essa funcionalidade tem suporte ao usar o SDK do Python do AzureML, a CLI, as APIs REST ou os modelos do Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao usar serviços como o Machine Learning Automatizado, a Microsoft pode gerar dados temporários e pré-processados para treinar vários modelos. Esses dados são armazenados em um datastore em seu workspace, o que permite que você aplique controles de acesso e criptografia adequadamente.

Talvez também seja interessante criptografar [informações de diagnóstico registradas do seu ponto de extremidade implantado](how-to-enable-app-insights.md) em sua instância do Azure Application Insights.

## <a name="monitoring"></a>Monitoramento

### <a name="metrics"></a>Métricas

Você pode usar as métricas do Azure Monitor para exibir e monitorar as métricas do seu workspace do Azure Machine Learning. No [portal do Azure](https://portal.azure.com), selecione seu workspace e depois **Métricas**:

[![Captura de tela mostrando métricas de exemplo de um workspace](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas contam com informações sobre execuções, implantações e registros.

Para obter mais informações, confira [Métricas no Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Log de atividades

Você pode exibir o log de atividades de um workspace para ver várias operações executadas nele. O log inclui informações básicas, como o nome da operação, o iniciador de eventos e o carimbo de data/hora.

Esta captura de tela mostra o log de atividades de um workspace:

[![Captura de tela mostrando o log de atividades de um workspace](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes da solicitação de pontuação são armazenados no Application Insights. O Application Insights é criado em sua assinatura ao criar um workspace. As informações registradas incluem campos como:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Algumas ações no workspace do Azure Machine Learning não registram informações no log de atividades. Por exemplo, não são registrados o início de uma execução de treinamento e o registro de um modelo.
>
> Algumas dessas ações são exibidas na área **Atividades** do workspace, mas essas notificações não indicam quem iniciou a atividade.

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Para o Azure Machine Learning, você deve habilitar a verificação do recurso de registro de contêiner do Azure e os recursos do serviço kubernetes do Azure. Consulte [verificação de imagem do registro de contêiner do Azure por central de segurança](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) e [integração dos serviços Kubernetess do Azure com a central de segurança](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

## <a name="data-flow-diagrams"></a>Diagrama do fluxo de dados

### <a name="create-workspace"></a>Criar workspace

O diagrama a seguir mostra o fluxo de trabalho da criação de um workspace.

* Você entra no Azure AD de um dos clientes do Azure Machine Learning com suporte (CLI do Azure, SDK do Python, portal do Azure) e solicita o token apropriado do Azure Resource Manager.
* Você chama o Azure Resource Manager para criar o workspace. 
* O Azure Resource Manager contata o provedor de recursos do Azure Machine Learning para provisionar o workspace.

Recursos adicionais são criados na assinatura do usuário durante a criação do workspace:

* Key Vault (para armazenar segredos)
* Uma conta de Armazenamento do Microsoft Azure (incluindo compartilhamento de arquivos e de blobs)
* Registro de Contêiner do Azure (para armazenar imagens do Docker para inferência/pontuação e experimentação)
* Application Insights (para armazenar a telemetria)

O usuário também pode provisionar outros destinos de computação anexados a um workspace (como o Serviço de Kubernetes do Azure ou VMs) conforme necessário.

[![Criar fluxo de trabalho do workspace](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salvar o código-fonte (scripts de treinamento)

O diagrama a seguir mostra o fluxo de trabalho do instantâneo do código.

Os diretórios (experimentos) que contêm o código-fonte (scripts de treinamento) estão associados a um workspace do Azure Machine Learning. Esses scripts são armazenados em seu computador local e na nuvem (no armazenamento de blobs do Azure da sua assinatura). Os instantâneos de código são usados para a execução ou inspeção de auditoria histórica.

[![Fluxo de trabalho do instantâneo de código](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Treinamento

O diagrama a seguir mostra o fluxo de trabalho de treinamento.

* O Azure Machine Learning é chamado com a ID de instantâneo do instantâneo de código salvo na seção anterior.
* O Azure Machine Learning cria uma ID de execução (opcional) e um token de serviço do Machine Learning, que é usado posteriormente por destinos de computação como Computação do Machine Learning/VMs para se comunicar com o serviço do Machine Learning.
* Para executar trabalhos de treinamento, você pode escolher um destino de computação gerenciado (como Computação do Machine Learning) ou um destino de computação não gerenciado (como VMs). Veja os fluxos de dados para ambos os cenários:
   * VMs/HDInsight acessadas por credenciais SSH em um cofre de chaves na assinatura da Microsoft. O Azure Machine Learning executa o código de gerenciamento no destino de computação que:

   1. Prepara o ambiente. (O Docker é uma opção para VMs e computadores locais. Consulte as etapas a seguir para a Computação do Machine Learning entender como funciona a execução de experimentos em contêineres do Docker.)
   1. Baixa o código.
   1. Define as configurações e variáveis do ambiente.
   1. Executa scripts de usuário (o instantâneo de código mencionado na seção anterior).

   * A Computação do Machine Learning, acessada por meio de uma identidade gerenciada por workspace.
Como a Computação do Machine Learning é um destino de computação gerenciado (ou seja, é gerenciado pela Microsoft), ela é executada em sua assinatura da Microsoft.

   1. A construção remota do Docker é inicializada, se for necessário.
   1. O código de gerenciamento é gravado no compartilhamento de Arquivos do Azure do usuário.
   1. O contêiner é iniciado com um comando inicial. Ou seja, o código de gerenciamento, conforme descrito na etapa anterior.

#### <a name="querying-runs-and-metrics"></a>Consultas de execuções e métricas

No diagrama de fluxo abaixo, essa etapa ocorre quando o destino de computação de treinamento grava as métricas de execução de volta ao Azure Machine Learning a partir do armazenamento no banco de dados do Cosmos DB. Os clientes podem chamar o Azure Machine Learning. O Machine Learning, por sua vez, transformará as métricas de pull do banco de dados do Cosmos DB e as retornará ao cliente.

[![Fluxo de trabalho do treinamento](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Criar serviços Web

O diagrama a seguir mostra o fluxo de trabalho de inferência. A inferência, ou a pontuação do modelo, é a fase na qual o modelo implantado é usado para previsão, mais comumente em dados de produção.

Estes são os detalhes:

* O usuário registra um modelo por meio de um cliente como o SDK do Azure Machine Learning.
* O usuário cria uma imagem por meio de um modelo, um arquivo de pontuação e outras dependências de modelo.
* A imagem do Docker é criada e armazenada no Registro de Contêiner do Azure.
* O serviço Web é implantado no destino de computação (Instâncias de Contêiner/AKS) usando a imagem criada na etapa anterior.
* Os detalhes da solicitação de pontuação são armazenados no Application Insights, o qual está na assinatura do usuário.
* A telemetria também é enviada por push para a assinatura do Microsoft/Azure.

[![Fluxo de trabalho da inferência](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="audit-and-manage-compliance"></a>Auditar e gerenciar conformidade

[Azure Policy](/azure/governance/policy) é uma ferramenta de governança que permite garantir que os recursos do Azure estejam em conformidade com suas políticas. Com Azure Machine Learning, você pode atribuir as seguintes políticas:

* **Chave gerenciada pelo cliente**: auditar ou impor se os espaços de trabalho devem usar uma chave gerenciada pelo cliente.
* **Link privado**: auditar se os espaços de trabalho usam um ponto de extremidade privado para se comunicar com uma rede virtual.

Para obter mais informações sobre Azure Policy, consulte a [documentação do Azure Policy](/azure/governance/policy/overview).

Para obter mais informações sobre as políticas específicas para Azure Machine Learning, consulte [auditar e gerenciar a conformidade com o Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Bloqueios de recurso

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Próximas etapas

* [Proteger serviços Web do Azure Machine Learning com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Usar o Azure Machine Learning com o Firewall do Azure](how-to-access-azureml-behind-firewall.md)
* [Usar o Azure Machine Learning com a Rede Virtual do Azure](how-to-network-security-overview.md)
* [Melhores práticas para criar sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Compilar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
