---
title: Segurança do Enterprise
titleSuffix: Azure Machine Learning
description: 'Use com segurança o Azure Machine Learning: autenticação, autorização, segurança de rede, criptografia de dados e monitoramento.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 1af1a1ccd8bff8fc4b578ecdeec3ac5f7c2352b1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082128"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Segurança corporativa para aprendizado de máquina do Azure

Neste artigo, você aprenderá sobre os recursos de segurança disponíveis para o Azure Machine Learning.

Quando você usa um serviço em nuvem, uma prática recomendada é restringir o acesso apenas aos usuários que precisam dele. Comece entendendo o modelo de autenticação e autorização utilizado pelo serviço. Você também pode querer restringir o acesso à rede ou juntar recursos com segurança em sua rede local com a nuvem. A criptografia de dados também é vital, tanto em repouso quanto enquanto os dados se movem entre os serviços. Finalmente, você precisa ser capaz de monitorar o serviço e produzir um registro de auditoria de todas as atividades.

> [!NOTE]
> As informações deste artigo funcionam com o Azure Machine Learning Python Versão 1.0.83.1 ou superior.

## <a name="authentication"></a>Autenticação

A autenticação de vários fatores é suportada se o Azure Active Directory (Azure AD) estiver configurado para usá-lo. Aqui está o processo de autenticação:

1. O cliente faz o adesão ao Azure AD e recebe um token do Azure Resource Manager.  Os usuários e os diretores de serviços são totalmente suportados.
1. O cliente apresenta o token ao Azure Resource Manager e a todos os Azure Machine Learning.
1. O serviço de Machine Learning fornece um token de serviço de Aprendizado de Máquina para o destino de computação do usuário (por exemplo, Machine Learning Compute). Este token é usado pelo alvo de computação do usuário para chamar de volta para o serviço de Aprendizado de Máquina após a execução ser concluída. O escopo é limitado ao espaço de trabalho.

[![Autenticação no Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Para obter mais informações, consulte [Configurar a autenticação para recursos e fluxos de trabalho do Azure Machine Learning](how-to-setup-authentication.md). Este artigo fornece informações e exemplos sobre autenticação, incluindo o uso de diretores de serviço e fluxos de trabalho automatizados.

### <a name="authentication-for-web-service-deployment"></a>Autenticação para implantação de serviços web

O Azure Machine Learning suporta duas formas de autenticação para serviços web: chave e token. Cada serviço web pode habilitar apenas uma forma de autenticação por vez.

|Método de autenticação|Descrição|Instâncias de Contêiner do Azure|AKS|
|---|---|---|---|
|Chave|As teclas são estáticas e não precisam ser atualizadas. As teclas podem ser regeneradas manualmente.|Desabilitado por padrão| Habilitado por padrão|
|Token|Os tokens expiram após um período de tempo especificado e precisam ser atualizados.| Não disponível| Desabilitado por padrão |

Para exemplos de código, consulte a [seção de autenticação do serviço web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorização

Você pode criar vários workspaces, e cada workspace pode ser compartilhado por várias pessoas. Quando você compartilha um espaço de trabalho, você pode controlar o acesso a ele atribuindo essas funções aos usuários:

* Proprietário
* Colaborador
* Leitor

A tabela a seguir lista algumas das principais operações de Machine Learning do Azure e as funções que podem realizá-las:

| Operação de Aprendizado de Máquina do Azure | Proprietário | Colaborador | Leitor |
| ---- |:----:|:----:|:----:|
| Criar workspace | ✓ | ✓ | |
| Compartilhar o workspace | ✓ | |  |
| Atualizar o espaço de trabalho para a edição Enterprise | ✓ | |
| Criar meta de computação | ✓ | ✓ | |
| Anexar alvo de computação | ✓ | ✓ | |
| Anexar armazenamentos de dados | ✓ | ✓ | |
| Executar o experimento | ✓ | ✓ | |
| Exibir corridas/métricas | ✓ | ✓ | ✓ |
| Registrar modelo | ✓ | ✓ | |
| Criar imagem | ✓ | ✓ | |
| Implantar serviço Web | ✓ | ✓ | |
| Ver modelos/imagens | ✓ | ✓ | ✓ |
| Ligue para o serviço web | ✓ | ✓ | ✓ |

Se as funções incorporadas não atenderem às suas necessidades, você pode criar funções personalizadas. As funções personalizadas são suportadas apenas para operações no espaço de trabalho e machine learning compute. As funções personalizadas podem ter permissões de leitura, gravação ou exclusão no espaço de trabalho e no recurso de computação nesse espaço de trabalho. Você pode disponibilizar a função em um nível específico de espaço de trabalho, um nível específico de grupo de recursos ou um nível específico de assinatura. Para obter mais informações, consulte [Gerenciar usuários e funções em um espaço de trabalho azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> O Azure Machine Learning não é suportado atualmente com a colaboração business-to-business do Azure Active Directory.

### <a name="securing-compute-targets-and-data"></a>Proteção de metas e dados de computação

Proprietários e colaboradores podem usar todos os alvos de computação e armazenamentode dados que estão conectados ao espaço de trabalho.  

Cada espaço de trabalho também tem uma identidade gerenciada atribuída ao sistema associada que tem o mesmo nome do espaço de trabalho. A identidade gerenciada tem as seguintes permissões nos recursos anexados usados no espaço de trabalho.

Para obter mais informações sobre identidades gerenciadas, consulte [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Recurso | Permissões |
| ----- | ----- |
| Workspace | Colaborador |
| Conta de armazenamento | Colaborador de dados de blob de armazenamento |
| Cofre de chaves | Acesso a todas as chaves, segredos, certificados |
| Registro de Contêiner do Azure | Colaborador |
| Grupo de recursos que contém o espaço de trabalho | Colaborador |
| Grupo de recursos que contém o cofre de chaves (se diferente daquele que contém o espaço de trabalho) | Colaborador |

Não recomendamos que os admins revoguem o acesso da identidade gerenciada aos recursos mencionados na tabela anterior. Você pode restaurar o acesso usando a operação de chaves de ressincronização.

O Azure Machine Learning cria um `aml-` aplicativo `Microsoft-AzureML-Support-App-`adicional (o nome começa com ou ) com acesso em nível de contribuinte em sua assinatura para cada região do espaço de trabalho. Por exemplo, se você tem um espaço de trabalho no Leste dos EUA e um no Norte da Europa na mesma assinatura, você verá dois desses aplicativos. Esses aplicativos permitem que o Azure Machine Learning ajude você a gerenciar recursos de computação.

## <a name="network-security"></a>Segurança de rede

O Azure Machine Learning conta com outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Você pode criar esses alvos de computação em uma rede virtual. Por exemplo, você pode usar a Máquina Virtual de Data Science do Azure para treinar um modelo e, em seguida, implantar o modelo no AKS.  

Para obter mais informações, consulte [Como executar experimentos e inferências em uma rede virtual](how-to-enable-virtual-network.md).

Você também pode habilitar o Azure Private Link para o seu espaço de trabalho. O Private Link permite que você restrinja as comunicações ao seu espaço de trabalho a partir de uma Rede Virtual Do Azure. Para obter mais informações, consulte [Como configurar o Private Link](how-to-configure-private-link.md).

> [!TIP]
> Você pode combinar rede virtual e Private Link para proteger a comunicação entre seu espaço de trabalho e outros recursos do Azure. No entanto, algumas combinações requerem um espaço de trabalho de edição Enterprise. Use a tabela a seguir para entender quais cenários requerem edição enterprise:
>
> | Cenário | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | Sem rede virtual ou Link Privado | ✔ | ✔ |
> | Espaço de trabalho sem Private Link. Outros recursos (exceto O Registro de Contêineres do Azure) em uma rede virtual | ✔ | ✔ |
> | Espaço de trabalho sem Private Link. Outros recursos com O Link Privado | ✔ | |
> | Espaço de trabalho com O Link Privado. Outros recursos (exceto O Registro de Contêineres do Azure) em uma rede virtual | ✔ | ✔ |
> | Espaço de trabalho e qualquer outro recurso com o Private Link | ✔ | |
> | Espaço de trabalho com O Link Privado. Outros recursos sem Private Link ou rede virtual | ✔ | ✔ |
> | Registro de contêineres do Azure em uma rede virtual | ✔ | |
> | Chaves gerenciadas pelo cliente para espaço de trabalho | ✔ | |
> 

> [!WARNING]
> A visualização de instâncias de cálculo do Azure Machine Learning não é suportada em um espaço de trabalho onde o Private Link está ativado.
> 
> O Azure Machine Learning não suporta o uso de um Serviço Azure Kubernetes que tenha um link privado ativado. Em vez disso, você pode usar o Azure Kubernetes Service em uma rede virtual. Para obter mais informações, consulte [os trabalhos de experimentação e inferência do Secure Azure ML dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Criptografia de dados

### <a name="encryption-at-rest"></a>Criptografia em repouso

> [!IMPORTANT]
> Se o seu espaço de trabalho contiver dados confidenciais, recomendamos definir o [hbi_workspace flag](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) enquanto cria seu espaço de trabalho. 

O `hbi_workspace` sinalizador controla a quantidade de dados que a Microsoft coleta para fins de diagnóstico e permite criptografia adicional em ambientes gerenciados pela Microsoft. Além disso, ele habilita o seguinte:

* Começa a criptografar o disco de arranhão local em seu cluster Amlcompute, desde que você não tenha criado nenhum cluster anterior nessa assinatura. Caso outra coisa, você precisa levantar um ticket de suporte para ativar a criptografia do disco de risco de seus clusters de computação 
* Limpa seu disco de arranhão local entre as corridas
* Passa com segurança credenciais para sua conta de armazenamento, registro de contêiner e conta SSH da camada de execução para seus clusters de computação usando seu cofre-chave
* Permite a filtragem de IP para garantir que os pools de lotes subjacentes não possam ser chamados por outros serviços externos que não sejam o AzureMachineLearningService


Para obter mais informações sobre como a criptografia em repouso funciona no Azure, consulte a criptografia de [dados do Azure em repouso](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

#### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O Azure Machine Learning armazena instantâneos, saídas e logs na conta de armazenamento Azure Blob vinculada ao espaço de trabalho Azure Machine Learning e à sua assinatura. Todos os dados armazenados no armazenamento Azure Blob são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para obter informações sobre como usar suas próprias chaves para dados armazenados no armazenamento Azure Blob, consulte [a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Os dados de treinamento normalmente também são armazenados no armazenamento Do Azure Blob para que sejam acessíveis a metas de computação de treinamento. Este armazenamento não é gerenciado pelo Azure Machine Learning, mas montado para calcular alvos como um sistema de arquivos remoto.

Se você precisar __girar ou revogar__ sua chave, você pode fazê-lo a qualquer momento. Ao girar uma chave, a conta de armazenamento começará a usar a nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desativar) uma chave, a conta de armazenamento cuida das solicitações falhadas. Geralmente leva uma hora para que a rotação ou revogação sejam eficazes.

Para obter informações sobre a regeneração das chaves de acesso, consulte [Regenerar as teclas de acesso de armazenamento](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

O Azure Machine Learning armazena métricas e metadados em uma instância DB do Azure Cosmos. Este exemplo está associado a uma assinatura da Microsoft gerenciada pelo Azure Machine Learning. Todos os dados armazenados no Azure Cosmos DB são criptografados em repouso com chaves gerenciadas pela Microsoft.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar a instância do Azure Cosmos DB, você pode criar uma instância dedicada do Cosmos DB para uso com seu espaço de trabalho. Recomendamos essa abordagem se você quiser armazenar seus dados, como informações de histórico de execução, fora da instância do Cosmos DB de vários locatários hospedada em nossa assinatura da Microsoft. 

Para habilitar o provisionamento de uma instância do Cosmos DB em sua assinatura com chaves gerenciadas pelo cliente, execute as seguintes ações:

* Habilite os recursos-chave gerenciados pelo cliente para o Cosmos DB. Neste momento, você deve solicitar acesso para usar esse recurso. Para isso, entre [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)em contato.

* Registre os provedores de recursos Azure Machine Learning e Azure Cosmos DB em sua assinatura, se ainda não estiver pronto.

* Autorize o Aplicativo de Aprendizado de Máquina (em Gerenciamento de Identidade e Acesso) com permissões de contribuinte em sua assinatura.

    ![Autorizar o 'Aplicativo de Machine Learning do Azure' em Gestão de Identidade e Acesso no portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Use os seguintes parâmetros ao criar o espaço de trabalho Azure Machine Learning. Ambos os parâmetros são obrigatórios e suportados em modelos SDK, CLI, REST APIs e Gerenciador de recursos.

    * `resource_cmk_uri`: Este parâmetro é o recurso completo uri da chave gerenciada pelo cliente em seu cofre de chaves, incluindo as informações da [versão para a chave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Este parâmetro é o ID de recurso do cofre-chave em sua assinatura. Este cofre-chave precisa estar na mesma região e assinatura que você usará para o espaço de trabalho Azure Machine Learning. 
    
        > [!NOTE]
        > Esta instância do cofre chave pode ser diferente do cofre de chaves que é criado pelo Azure Machine Learning quando você fornece o espaço de trabalho. Se você quiser usar a mesma instância do cofre de chaves para o espaço de trabalho, passe o mesmo cofre de chaves enquanto provisiona o espaço de trabalho usando o [parâmetro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Esta instância do Cosmos DB é criada em um grupo de recursos gerenciado pela Microsoft em sua assinatura. O grupo de recursos gerenciados é nomeado no formato<AML Workspace Resource Group Name><GUID>

> [!IMPORTANT]
> * Se você precisar excluir esta instância do Cosmos DB, você deve excluir o espaço de trabalho azure Machine Learning que o usa. 
> * As [__unidades de solicitação__](../cosmos-db/request-units.md) padrão desta conta Cosmos DB são definidas em __8000__. A alteração desse valor não é suportada. 

Se você precisar __girar ou revogar__ sua chave, você pode fazê-lo a qualquer momento. Ao girar uma chave, o Cosmos DB começará a usar a nova chave (versão mais recente) para criptografar dados em repouso. Ao revogar (desativar) uma chave, o Cosmos DB cuida das solicitações falhadas. Geralmente leva uma hora para que a rotação ou revogação sejam eficazes.

Para obter mais informações sobre as chaves gerenciadas pelo cliente com o Cosmos DB, consulte [Configurar chaves gerenciadas pelo cliente para sua conta Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Todas as imagens de contêiner em seu registro (Registro de Contêineres Do Azure) são criptografadas em repouso. O Azure criptografa automaticamente uma imagem antes de armazená-la e descriptografá-la quando o Azure Machine Learning puxa a imagem.

Para usar suas próprias chaves (gerenciadas pelo cliente) para criptografar seu Registro de Contêineres do Azure, você precisa criar seu próprio ACR e anexá-lo enquanto fornece o espaço de trabalho ou criptografar a instância padrão que é criada no momento do provisionamento do espaço de trabalho.

Para um exemplo de criação de um espaço de trabalho usando um Registro de Contêineres Azure existente, consulte os seguintes artigos:

* [Crie um espaço de trabalho para o Azure Machine Learning com o Azure CLI](how-to-manage-workspace-cli.md).
* [Use um modelo do Azure Resource Manager para criar um espaço de trabalho para o Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

Você pode criptografar um recurso Aci (ACI) implantado de instância de contêiner do Azure usando chaves gerenciadas pelo cliente. A chave gerenciada pelo cliente usada para ACI pode ser armazenada no Azure Key Vault para o seu espaço de trabalho. Para obter informações sobre como gerar uma chave, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Para usar a chave ao implantar um modelo na Instância de `AciWebservice.deploy_configuration()`Contêiner do Azure, crie uma nova configuração de implantação usando . Forneça as informações-chave usando os seguintes parâmetros:

* `cmk_vault_base_url`: A URL do cofre de chaves que contém a chave.
* `cmk_key_name`: O nome da chave.
* `cmk_key_version`: A versão da chave.

Para obter mais informações sobre como criar e usar uma configuração de implantação, consulte os seguintes artigos:

* [Referência aciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Onde e como implantar](how-to-deploy-and-where.md)
* [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-azure-container-instance.md)

Para obter mais informações sobre o uso de uma chave gerenciada pelo cliente com a ACI, consulte [Criptografar dados com uma chave gerenciada pelo cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Você pode criptografar um recurso do Azure Kubernetes Service implantado usando chaves gerenciadas pelo cliente a qualquer momento. Para obter mais informações, consulte [Traga suas próprias chaves com o Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Esse processo permite criptografar os dados e o disco do SISTEMA OPERACIONAL das máquinas virtuais implantadas no cluster Kubernetes.

> [!IMPORTANT]
> Este processo só funciona com a versão 1.17 do AKS K8s ou superior. O Azure Machine Learning adicionou suporte para aKS 1.17 em 13 de janeiro de 2020.

#### <a name="machine-learning-compute"></a>Computação de aprendizado de máquina

O disco do SISTEMA OPERACIONAL para cada nó de computação armazenado no Azure Storage é criptografado com chaves gerenciadas pela Microsoft em contas de armazenamento Azure Machine Learning. Esse alvo de computação é efêmero, e os clusters são tipicamente dimensionados quando não há filas. A máquina virtual subjacente é desprovisionada e o disco do sistema operacional é excluído. O Azure Disk Encryption não é suportado para o disco do sistema operacional.

Cada máquina virtual também tem um disco temporário local para operações de SO. Se você quiser, você pode usar o disco para encenar dados de treinamento. O disco é criptografado por `hbi_workspace` padrão para `TRUE`espaços de trabalho com o parâmetro definido para . Esse ambiente é de curta duração apenas durante a sua execução, e o suporte à criptografia é limitado apenas a chaves gerenciadas pelo sistema.

#### <a name="azure-databricks"></a>Azure Databricks

Os Azure Databricks podem ser usados em pipelines de Machine Learning do Azure. Por padrão, o DBFS (Databricks File System, sistema de arquivos de tijolos de dados) usado pelo Azure Databricks é criptografado usando uma chave gerenciada pela Microsoft. Para configurar o Azure Databricks para usar as chaves gerenciadas pelo cliente, consulte [Configurar chaves gerenciadas pelo cliente no DBFS padrão (raiz).](/azure/databricks/security/customer-managed-keys-dbfs)

### <a name="encryption-in-transit"></a>Criptografia em trânsito

O Azure Machine Learning usa TLS para garantir a comunicação interna entre vários microserviços de Machine Learning do Azure. Todo acesso ao Azure Storage também ocorre por um canal seguro.

Para proteger chamadas externas para o ponto final de pontuação, o Azure Machine Learning usa TLS. Para obter mais informações, consulte [Usar TLS para garantir um serviço web através do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Como usar o Azure Key Vault

O Azure Machine Learning usa a instância do Azure Key Vault associada ao espaço de trabalho para armazenar credenciais de vários tipos:

* A seqüência de conexão de conta de armazenamento associada
* Senhas para instâncias do Azure Container Repository
* Strings de conexão para armazenamentos de dados

Senhas ssh e chaves para calcular alvos como Azure HDInsight e VMs são armazenadas em um cofre de chaves separado associado à assinatura da Microsoft. O Azure Machine Learning não armazena senhas ou chaves fornecidas pelos usuários. Em vez disso, gera, autoriza e armazena suas próprias chaves SSH para se conectar a VMs e HDInsight para executar os experimentos.

Cada espaço de trabalho tem uma identidade gerenciada atribuída ao sistema associada que tem o mesmo nome do espaço de trabalho. Essa identidade gerenciada tem acesso a todas as chaves, segredos e certificados no cofre principal.

## <a name="data-collection-and-handling"></a>Coleta e manuseio de dados

### <a name="microsoft-collected-data"></a>Dados coletados pela Microsoft

A Microsoft pode coletar informações de identificação não-usuários, como nomes de recursos (por exemplo, o nome do conjunto de dados ou o nome do experimento de aprendizado de máquina) ou variáveis de ambiente de trabalho para fins de diagnóstico. Todos esses dados são armazenados usando chaves gerenciadas pela Microsoft em armazenamento hospedados em assinaturas de propriedade da Microsoft e seguem [a política de privacidade padrão da Microsoft e os padrões de tratamento de dados](https://privacy.microsoft.com/privacystatement).

A Microsoft também recomenda não armazenar informações confidenciais (como segredos-chave da conta) em variáveis de ambiente. As variáveis de ambiente são registradas, criptografadas e armazenadas por nós. Da mesma forma, ao nomear [runid,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)evite incluir informações confidenciais, como nomes de usuários ou nomes de projetos secretos. Essas informações podem aparecer em registros de telemetria acessíveis aos engenheiros de suporte da Microsoft.

Você pode optar por não coletar `hbi_workspace` dados de `TRUE` diagnóstico definindo o parâmetro para enquanto provisiona o espaço de trabalho. Essa funcionalidade é suportada ao usar os modelos AzureML Python SDK, CLI, REST APIs ou Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dados gerados pela Microsoft

Ao usar serviços como o Automated Machine Learning, a Microsoft pode gerar dados transitórios pré-processados para treinar vários modelos. Esses dados são armazenados em um datastore em seu espaço de trabalho, o que permite que você imponha controles de acesso e criptografia adequadamente.

Você também pode querer criptografar [informações de diagnóstico registradas a partir do ponto final implantado](how-to-enable-app-insights.md) na instância do Azure Application Insights.

## <a name="monitoring"></a>Monitoramento

### <a name="metrics"></a>Métricas

Você pode usar as métricas do Azure Monitor para visualizar e monitorar métricas para o seu espaço de trabalho azure Machine Learning. No [portal Azure,](https://portal.azure.com)selecione seu espaço de trabalho e selecione **Métricas**:

[![Captura de tela mostrando métricas de exemplo para um espaço de trabalho](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

As métricas incluem informações sobre corridas, implantações e registros.

Para obter mais informações, consulte [Métricas no Monitor Do Azure](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Log de atividades

Você pode visualizar o registro de atividades de um espaço de trabalho para ver várias operações que são realizadas no espaço de trabalho. O registro inclui informações básicas como o nome da operação, iniciador de eventos e carimbo de data e hora.

Esta captura de tela mostra o registro de atividades de um espaço de trabalho:

[![Captura de tela mostrando o registro de atividades de um espaço de trabalho](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Os detalhes da solicitação de pontuação são armazenados no Application Insights. O Application Insights é criado em sua assinatura quando você cria um espaço de trabalho. As informações registradas incluem campos como:

* HTTPMétodo
* UserAgent
* Type de computação
* SolicitaçãoUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Algumas ações no espaço de trabalho azure Machine Learning não registram informações no registro de atividades. Por exemplo, o início de uma corrida de treinamento e o registro de um modelo não são registrados.
>
> Algumas dessas ações aparecem na área de **Atividades** do seu espaço de trabalho, mas essas notificações não indicam quem iniciou a atividade.

## <a name="data-flow-diagrams"></a>Diagramas de fluxo de dados

### <a name="create-workspace"></a>Criar workspace

O diagrama a seguir mostra o fluxo de trabalho criar espaço de trabalho.

* Você entra no Azure AD de um dos clientes de Machine Learning do Azure (Azure CLI, Python SDK, portal Azure) e solicita o token azure Resource Manager apropriado.
* Você chama o Azure Resource Manager para criar o espaço de trabalho. 
* O Azure Resource Manager entra em contato com o provedor de recursos azure Machine Learning para provisionar o espaço de trabalho.

Recursos adicionais são criados na assinatura do usuário durante a criação do espaço de trabalho:

* Cofre de Chaves (para armazenar segredos)
* Uma conta de armazenamento Azure (incluindo blob e compartilhamento de arquivos)
* Registro de contêineres do Azure (para armazenar imagens do Docker para inferência/pontuação e experimentação)
* Insights de aplicativos (para armazenar telemetria)

O usuário também pode provisionar outros alvos de computação que são anexados a um espaço de trabalho (como o Azure Kubernetes Service ou VMs) conforme necessário.

[![Criar fluxo de trabalho no espaço de trabalho](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salvar código fonte (scripts de treinamento)

O diagrama a seguir mostra o fluxo de trabalho do snapshot de código.

Associados a um espaço de trabalho azure Machine Learning estão diretórios (experimentos) que contêm o código fonte (scripts de treinamento). Esses scripts são armazenados em sua máquina local e na nuvem (no armazenamento Azure Blob para sua assinatura). Os instantâneos de código são usados para execução ou inspeção para auditoria histórica.

[![Fluxo de trabalho de instantâneo de código](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Treinamento

O diagrama a seguir mostra o fluxo de trabalho do treinamento.

* O Azure Machine Learning é chamado com o ID de instantâneo para o instantâneo de código salvo na seção anterior.
* O Azure Machine Learning cria um ID (opcional) e um token de serviço de Machine Learning, que mais tarde é usado por alvos de computação como Machine Learning Compute/VMs para se comunicar com o serviço de Machine Learning.
* Você pode escolher um alvo de computação gerenciado (como Machine Learning Compute) ou um alvo de computação não gerenciado (como VMs) para executar trabalhos de treinamento. Aqui estão os fluxos de dados para ambos os cenários:
   * VMs/HDInsight, acessados por credenciais SSH em um cofre chave na assinatura da Microsoft. O Azure Machine Learning executa o código de gerenciamento no alvo da computação que:

   1. Prepara o ambiente. (Docker é uma opção para VMs e computadores locais. Veja as etapas a seguir para machine learning compute para entender como funciona a execução de experimentos em contêineres Docker.)
   1. Baixa o código.
   1. Configura ções e variáveis de ambiente.
   1. Executa scripts de usuário (o instantâneo de código mencionado na seção anterior).

   * Machine Learning Compute, acessado através de uma identidade gerenciada por espaço de trabalho.
Como o Machine Learning Compute é um alvo de computação gerenciado (ou seja, é gerenciado pela Microsoft) ele é executado sob sua assinatura da Microsoft.

   1. A construção remota do Docker é iniciada, se necessário.
   1. O código de gerenciamento é gravado no compartilhamento de arquivos Azure do usuário.
   1. O contêiner é iniciado com um comando inicial. Ou seja, código de gestão como descrito na etapa anterior.

#### <a name="querying-runs-and-metrics"></a>Querying executa e métricas

No diagrama de fluxo abaixo, essa etapa ocorre quando o alvo de computação de treinamento grava as métricas de execução de volta para o Azure Machine Learning a partir do armazenamento no banco de dados Cosmos DB. Os clientes podem ligar para o Azure Machine Learning. O Machine Learning, por sua vez, puxará métricas do banco de dados do Cosmos DB e as devolverá ao cliente.

[![Fluxo de trabalho de treinamento](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Criação de serviços web

O diagrama a seguir mostra o fluxo de trabalho da inferência. Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para predição, mais comumente em dados de produção.

Estes são os detalhes:

* O usuário registra um modelo usando um cliente como o Azure Machine Learning SDK.
* O usuário cria uma imagem usando um modelo, um arquivo de pontuação e outras dependências do modelo.
* A imagem Do Docker é criada e armazenada no Registro de Contêineres do Azure.
* O serviço web é implantado no destino de computação (Instâncias de contêiner/AKS) usando a imagem criada na etapa anterior.
* Os detalhes da solicitação de pontuação são armazenados no Application Insights, que está na assinatura do usuário.
* A telemetria também é empurrada para a assinatura Microsoft/Azure.

[![Fluxo de trabalho de inferência](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* [Serviços web de aprendizado de máquina do Azure seguro com TLS](how-to-secure-web-service.md)
* [Consumir um modelo de Machine Learning implantado como um serviço web](how-to-consume-web-service.md)
* [Como executar previsões em lotes](how-to-use-parallel-run-step.md)
* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Use o Azure Machine Learning com a rede virtual do Azure](how-to-enable-virtual-network.md)
* [Melhores práticas para criar sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Compilar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
