---
title: Isolamento e privacidade da rede
titleSuffix: Azure Machine Learning
description: Use uma Rede Virtual do Azure isolada com o Azure Machine Learning para proteger a experimentação/treinamento, bem como trabalhos de inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121197"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Isolamento de rede durante o treinamento e a inferência com pontos de extremidade privados e redes virtuais
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger seus ciclos de vida de aprendizado de máquina isolando Azure Machine Learning trabalhos de inferência e treinamento em uma vnet (rede virtual) do Azure. Uma __rede virtual__ atua como um limite de segurança, isolando os recursos do Azure da Internet pública. Além disso, é possível ingressar em uma rede virtual do Azure na rede local. Ao ingressar em redes, você pode treinar com segurança seus modelos e acessar seus modelos implantados para inferência.

Azure Machine Learning espaço de trabalho pode ser acessado de uma rede virtual usando um __ponto de extremidade privado__. O ponto de extremidade privado é um conjunto de endereços IP privados em sua rede virtual e o acesso ao seu espaço de trabalho é limitado à rede virtual. O ponto de extremidade privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [Link Privado do Azure](/azure/private-link/private-link-overview).

> [!NOTE]
> Você pode encontrar problemas ao acessar um espaço de trabalho por meio de um ponto de extremidade privado ao usar o Mozilla Firefox. O problema pode estar relacionado à configuração de DNS sobre HTTPS no navegador. É recomendável usar o Microsoft Edge ou o Google Chrome para contornar esse problema.

Azure Machine Learning se baseia em outros serviços do Azure para armazenamento de dados e recursos computacionais (usados para treinar e implantar modelos). Esses recursos também podem ser criados em uma rede virtual. Por exemplo, você pode usar a computação do Azure Machine Learning para treinar um modelo e, em seguida, implantar o modelo no Serviço de Kubernetes do Azure (AKS). 

## <a name="prerequisites"></a>Pré-requisitos

+ Um [workspace](how-to-manage-workspace.md) do Azure Machine Learning.

+ Conhecimento geral de trabalho do [serviço da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e de [rede IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Rede e sub-rede virtuais pré-existentes para usar com os recursos de computação.

+ Para implantar recursos em uma rede virtual ou sub-rede, sua conta de usuário deve ter permissões para as seguintes ações nos controles de acesso baseado em função (RBAC) do Azure:

    - "Microsoft. Network/virtualNetworks/Join/Action" no recurso de rede virtual.
    - "Microsoft. Network/virtualNetworks/sub-rede/junção/ação" no recurso de sub-rede.

    Para obter mais informações sobre o RBAC com rede, consulte [funções internas de rede](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>Proteger seu espaço de trabalho

Seu espaço de trabalho do Azure Machine Learning pode ter um __ponto de extremidade público__ ou um __ponto de extremidade privado__. Um ponto de extremidade público é um conjunto de endereços IP que são acessíveis na Internet pública, enquanto um ponto de extremidade privado é um conjunto de endereços IP privados em uma rede virtual. 

Para limitar o acesso ao seu espaço de trabalho para ocorrer somente nos endereços IP privados, use um ponto de extremidade privado.

Como a comunicação com o espaço de trabalho só é permitida a partir da rede virtual, todos os ambientes de desenvolvimento que usam o espaço de trabalho devem ser membros da rede virtual. Por exemplo, uma máquina virtual na rede virtual.

> [!IMPORTANT]
> O ponto de extremidade privado não afeta o plano de controle do Azure (operações de gerenciamento), como excluir o espaço de trabalho ou gerenciar recursos de computação. Por exemplo, criando, atualizando ou excluindo um destino de computação. Essas operações são executadas pela Internet pública normalmente.
>
> O ponto de extremidade privado impede o acesso ao espaço de trabalho de fora da rede virtual.

Algumas combinações de recursos com um ponto de extremidade privado exigem um espaço de trabalho do Enterprise Edition. Use a tabela a seguir para entender quais cenários exigem a edição Enterprise:

| Cenário | Enterprise</br>edition | Basic</br>edition |
| ----- |:-----:|:-----:| 
| Nenhuma rede virtual ou ponto de extremidade privado | ✔ | ✔ |
| Espaço de trabalho sem ponto de extremidade privado. Outros recursos (exceto o Registro de Contêiner do Azure) em uma rede virtual | ✔ | ✔ |
| Espaço de trabalho sem ponto de extremidade privado. Outros recursos com ponto de extremidade privado | ✔ | |
| Espaço de trabalho com ponto de extremidade privado. Outros recursos (exceto o Registro de Contêiner do Azure) em uma rede virtual | ✔ | ✔ |
| Espaço de trabalho e qualquer outro recurso com ponto de extremidade privado | ✔ | |
| Espaço de trabalho com ponto de extremidade privado. Outros recursos sem ponto de extremidade privado ou rede virtual | ✔ | ✔ |
| Registro de Contêiner do Azure em uma rede virtual | ✔ | |
| Chaves gerenciadas pelo cliente para o espaço de trabalho | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning instâncias de computação não têm suporte em um espaço de trabalho em que o ponto de extremidade privado está habilitado.
>
> Azure Machine Learning não dá suporte ao uso de um serviço kubernetes do Azure que tenha um ponto de extremidade privado habilitado. Em vez disso, você pode usar o Serviço de Kubernetes do Azure em uma rede virtual. Para obter mais informações, consulte [Proteger trabalhos de experimentação e inferência do Azure ML em uma Rede Virtual do Azure](how-to-enable-virtual-network.md).

Para saber mais sobre pontos de extremidade privados no Azure, confira o artigo [link privado do Azure](/azure/private-link/private-link-overview) .

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Criar um espaço de trabalho que usa um ponto de extremidade privado

Você pode criar um novo espaço de trabalho com um ponto de extremidade privado usando o SDK Azure Machine Learning, a CLI, um modelo de Azure Resource Manager ou o portal do Azure.

__Requisitos__

* A rede virtual que você usa com o ponto de extremidade privado deve ter as políticas de rede desabilitadas. Para obter mais informações, consulte [Disable Network Policies for a Private Endpoint](/azure/private-link/disable-private-endpoint-network-policy).

__Limitações__

* Você não pode se conectar ao espaço de trabalho pela Internet pública, somente de dentro da rede virtual.

* Se vários espaços de trabalho forem criados usando pontos de extremidade privados e usarem a mesma zona DNS privada, somente o primeiro espaço de trabalho será adicionado aos __links de rede virtual__ da zona DNS privada.

    Para contornar essa limitação, adicione manualmente o link de rede virtual para os espaços de trabalho adicionais. Para obter mais informações, consulte [o que é um link de rede virtual](/azure/dns/private-dns-virtual-network-links).

__Configuration__

Para obter informações sobre como criar um espaço de trabalho usando uma rede virtual e um ponto de extremidade privado, juntamente com outras opções de configuração, consulte os seguintes artigos:

* [Use um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).
* [Crie espaços de trabalho no portal](how-to-manage-workspace.md).
* [Crie espaços de trabalho com CLI do Azure](how-to-manage-workspace-cli.md).
* Para usar o SDK do Python, consulte a documentação de referência do [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) e [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) .

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Requisitos__

* Para acessar dados em uma conta de armazenamento, a conta de armazenamento deve estar na mesma rede virtual que o espaço de trabalho.

* Se os dados estiverem armazenados em uma rede virtual, você deverá usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) do espaço de trabalho para conceder ao estúdio acesso aos seus dados.

    > [!IMPORTANT]
    > Embora a maioria do estúdio funcione com dados armazenados em uma rede virtual, os notebooks integrados __não__. Os blocos de anotações integrados não dão suporte ao uso do armazenamento que está em uma rede virtual. Em vez disso, você pode usar blocos de anotações do Jupyter de uma instância de computação. Para obter mais informações, consulte a seção [clusters de computação & instâncias](#compute-instance) .

    Se você não conceder acesso ao estúdio, receberá esse erro, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` as seguintes operações não estarão disponíveis:

    * Visualizar dados no estúdio.
    * Visualize dados no designer.
    * Envie um experimento do AutoML.
    * Inicie um projeto de rotulagem.

__Limitações__

* O Azure Machine Learning Studio dá suporte à leitura de dados dos seguintes tipos de repositório de armazenamento em uma rede virtual:

    * Blob do Azure
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Banco de Dados SQL do Azure

__Configuration__

* __Configure os repositórios de dados para usar uma identidade gerenciada__ para acessar os mesmos. Essas etapas adicionam a identidade gerenciada do espaço de trabalho como um __leitor__ ao serviço de armazenamento usando o controle de acesso baseado em recursos (RBAC) do Azure. O acesso de __leitor__ permite que o espaço de trabalho recupere as configurações de firewall e verifique se os dados não deixam a rede virtual.

    1. No estúdio, selecione __repositórios de armazenamento__.

    1. Para criar um novo repositório de armazenamento, selecione __+ novo repositório de armazenamento__. Para atualizar um existente, selecione o repositório de armazenamento e selecione __Atualizar credenciais__.

    1. Nas configurações do repositório de armazenamento, selecione __Sim__ para __permitir que o serviço de Azure Machine Learning acesse o armazenamento usando a identidade gerenciada do espaço de trabalho__.

    > [!NOTE]
    > Essas alterações podem levar até 10 minutos para entrar em vigor.

* Para o __armazenamento de BLOBs do Azure__, a identidade gerenciada do espaço de trabalho também deve ser adicionada como um [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados do armazenamento de BLOBs.

* O designer usa a conta de armazenamento anexada ao seu espaço de trabalho para armazenar a saída por padrão. No entanto, você pode especificá-lo para armazenar a saída em qualquer repositório de armazenamento ao qual você tenha acesso. Se seu ambiente usa redes virtuais, você pode usar esses controles para garantir que seus dados permaneçam seguros e acessíveis. Para definir um novo armazenamento padrão para um pipeline:

    1. Em um rascunho de pipeline, selecione o **ícone de engrenagem configurações** próximo ao título do seu pipeline.
    1. Selecione a entrada de **repositório de armazenamento padrão** .
    1. Especifique um novo repositório de armazenamento.

    Você também pode substituir o repositório de armazenamento padrão por módulo. Isso lhe dá controle sobre o local de armazenamento de cada módulo individual.

    1. Selecione o módulo cuja saída você deseja especificar.
    1. Expanda a seção **configurações de saída** .
    1. Selecione **Substituir configurações de saída padrão**.
    1. Selecione **definir configurações de saída**.
    1. Especifique um novo repositório de armazenamento.

* Se estiver usando __Azure data Lake Storage Gen2__, você poderá usar as ACLs (listas de controle de acesso) de estilo RBAC e POSIX para controlar o acesso a dados dentro de uma rede virtual.

    Para usar o RBAC, adicione a identidade gerenciada do espaço de trabalho à função de [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Para obter mais informações, confira [Controle de acesso baseado em função](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    Para usar ACLs, a identidade gerenciada do espaço de trabalho pode ser atribuída ao acesso, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure data Lake Storage Gen1__ oferece suporte apenas a listas de controle de acesso em estilo POSIX. Você pode atribuir o acesso de identidade gerenciada do espaço de trabalho a recursos, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

* Para acessar os dados armazenados em um __Azure SQL Database__ usando a identidade gerenciada, você deve criar um usuário contido no SQL que mapeia para a identidade gerenciada. Para obter mais informações sobre como criar um usuário de um provedor externo, consulte [criar usuários independentes mapeados para identidades do Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Depois de criar um usuário do SQL continha, conceda permissões a ele usando o [comando Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Se você estiver __acessando o Studio de um recurso dentro de uma rede virtual__ (por exemplo, uma instância de computação ou máquina virtual), deverá permitir o tráfego de saída da rede virtual para o estúdio. 

    Por exemplo, se você estiver usando NSG (grupos de segurança de rede) para restringir o tráfego de saída, adicione uma regra a um destino de __marca de serviço__ de __AzureFrontDoor. frontend__.

## <a name="use-datastores-and-datasets"></a>Usar repositórios de armazenamento e conjuntos de valores

> [!NOTE]
> Esta seção aborda o uso do datastore e do conjunto de conhecimento para a experiência do SDK. Para obter mais informações sobre a experiência do estúdio, consulte a seção [Machine Learning Studio](#machine-learning-studio).

__Limitações__

Por padrão, Azure Machine Learning executa as verificações de validade e credencial de dados quando você tenta acessar dados usando o SDK. Se seus dados estiverem atrás de uma rede virtual, Azure Machine Learning não poderá acessar os dados e falhará em suas verificações. Para contornar esse problema, pule a validação ao criar repositórios de armazenamento e conjuntos de valores.

* Ao usar um __repositório de armazenamento__:

    Azure Data Lake Store Gen1 e Azure Data Lake Store Gen2 ignoram a validação por padrão, portanto, nenhuma ação adicional é necessária. No entanto, para os seguintes serviços, você pode usar sintaxe semelhante para ignorar a validação do repositório de armazenamento:

    - Armazenamento de Blobs do Azure
    - FileShare do Azure
    - PostgreSQL
    - Banco de Dados SQL do Azure

    O exemplo de código a seguir cria um novo armazenamento de BLOBs e conjuntos do Azure `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* Ao usar um __conjunto__de uma:

    A sintaxe para ignorar a validação de DataSet é semelhante para os seguintes tipos de conjunto de conjuntos:
    - Arquivo delimitado
    - JSON 
    - Parquet
    - SQL
    - Arquivo

    O código a seguir cria um novo conjunto de valores e conjuntos de JSON `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Conta de Armazenamento do Azure

> [!IMPORTANT]
> Você pode posicionar a _conta de armazenamento padrão_ para Azure Machine Learning e _contas de armazenamento não padrão_ em uma rede virtual.

__Requisitos__

* A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias ou clusters de computação usados para treinamento ou inferência.

__Configuration__

Para proteger a conta de armazenamento do Azure usada pelo seu espaço de trabalho, habilite um __ponto de extremidade privado__ ou um __ponto de extremidade de serviço__ para a conta de armazenamento em sua rede virtual.

* Para configurar a conta de armazenamento para usar um __ponto de extremidade privado__, consulte o artigo [usar pontos de extremidades particulares](/azure/storage/common/storage-private-endpoints.md) .

* Para configurar a conta de armazenamento para usar um __ponto de extremidade de serviço__, use as seguintes etapas:

    1. Para adicionar a conta de armazenamento à rede virtual usada pelo seu espaço de trabalho, use as informações na seção __conceder acesso de uma rede virtual__ do artigo [configurar redes virtuais e firewalls do armazenamento do Azure](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. Para permitir o acesso dos serviços da Microsoft na rede virtual (como Azure Machine Learning), use as informações na seção __exceções__ do artigo [configurar redes virtuais e firewalls de armazenamento do Azure](/azure/storage/common/storage-network-security#exceptions) .
    1. Ao trabalhar com o SDK do Azure Machine Learning, seu ambiente de desenvolvimento deve ter a capacidade de se conectar à Conta de Armazenamento do Azure. O firewall deve permitir o acesso do endereço IP do ambiente de desenvolvimento quando a conta de armazenamento está dentro de uma rede virtual. Para adicionar o endereço IP do ambiente de desenvolvimento, use as informações na seção __conceder acesso de um intervalo de IP da Internet__ do artigo [configurar redes virtuais e firewalls do armazenamento do Azure](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Registro de Contêiner do Azure

__Requisitos__

* Seu workspace do Azure Machine Learning deve ser da edição Enterprise. Para obter mais informações sobre a atualização, consulte [Atualizar para a edição Enterprise](how-to-manage-workspace.md#upgrade).
* Sua região de espaço de trabalho do Azure Machine Learning deve ser uma [região habilitada para link privado](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
* O registro de contêiner do Azure deve ser uma versão Premium. Para obter mais informações sobre atualização, consulte [Como alterar SKUs](/azure/container-registry/container-registry-skus#changing-skus).
* Seu Registro de Contêiner do Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os destinos de computação usados para treinamento ou inferência.
* Seu workspace do Azure Machine Learning deve conter um [cluster de cálculo do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).

__Limitações__

* Quando o ACR está atrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para criar imagens do Docker diretamente. Em vez disso, o cluster de cálculo é usado para compilar as imagens.

__Configuration__

1. Para localizar o nome do Registro de Contêiner do Azure para seu workspace, use um dos seguintes métodos:

    __Azure portal__

    Na seção de visão geral do seu workspace, o valor do __Registro__ vincula-se ao Registro de Contêiner do Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro de Contêiner do Azure para o workspace" border="true":::

    __CLI do Azure__

    Se você tiver [instalado a extensão do Machine Learning para CLI do Azure](reference-azure-machine-learning-cli.md), poderá usar o comando `az ml workspace show` para mostrar as informações do workspace.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Esse comando retorna um valor semelhante a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. A última parte da cadeia de caracteres é o nome do Registro de Contêiner do Azure para o workspace.

1. Para limitar o acesso à sua rede virtual, use as etapas em [Configurar o acesso à rede para o registro](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Ao adicionar a rede virtual, selecione a rede virtual e a sub-rede para seus recursos do Azure Machine Learning.

1. Use o SDK do Python do Azure Machine Learning para configurar um cluster de cálculo para compilar imagens do Docker. O seguinte trecho de código demonstra como fazer isso:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A conta de armazenamento, o cluster de cálculo e o Registro de Contêiner do Azure devem estar todos na mesma sub-rede da rede virtual.
    
    Para obter mais informações, consulte a referência do método [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

1. Para permitir que seu espaço de trabalho se comunique com a instância do ACR, aplique o seguinte modelo de Azure Resource Manager:

    > [!WARNING]
    > Este modelo habilita um ponto de extremidade privado para seu espaço de trabalho e o altera para um espaço de trabalho corporativo. Não é possível desfazer essas alterações.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Instância do cofre de chaves 

__Requisitos__

__Limitações__

__Configuration__ 

Para usar Azure Machine Learning recursos de experimentação com Azure Key Vault por trás de uma rede virtual, use o artigo [configurar Azure Key Vault firewalls e redes virtuais](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> Ao seguir as etapas no artigo, use a mesma rede virtual usada pelos recursos de computação de experimentação. Você também deve __permitir que os serviços confiáveis da Microsoft ignorem esse firewall__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters e instâncias de computação 

__Requisitos__

* A rede virtual deve estar na mesma assinatura e região que o workspace do Azure Machine Learning.
* A sub-rede especificada para a instância ou cluster de computação deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs destinadas. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de cálculo será parcialmente alocado.
* Se você planeja proteger a rede virtual restringindo o tráfego, algumas portas devem ser deixadas abertas para o serviço de computação.
* Se você for colocar várias instâncias ou clusters de computação em uma rede virtual, talvez seja necessário solicitar um aumento de cota para um ou mais de seus recursos.
* Se as Contas de Armazenamento do Azure para o workspace também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que a instância ou cluster de computação do Azure Machine Learning. 
* Para que a funcionalidade de Jupyter da instância de computação funcione, verifique se a comunicação do soquete da Web não está desabilitada.

__Limitações__

* A instância ou cluster de computação do Machine Learning aloca automaticamente recursos adicionais de rede __no grupo de recursos que contém a rede virtual__. Para cada instância ou cluster de computação, o serviço aloca os seguintes recursos:

    * Um grupo de segurança de rede
    * Um endereço IP público
    * Um balanceador de carga
    
    Para __clusters de computação__, esses recursos são excluídos (e recriados) sempre que o cluster é dimensionado para 0 nós.
    
    Para uma __instância de computação__ , os recursos são mantidos até que a instância seja excluída (parar não remove os recursos).

    Esses recursos são limitados pelas [cotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da assinatura.

__Configuration__

* Para criar um cluster de Computação do Machine Learning, siga as seguintes etapas:

    1. Entre no [estúdio do Azure Machine Learning](https://ml.azure.com/) e selecione sua assinatura e workspace.
    1. Selecione __Computação__ à esquerda.
    1. Selecione __Clusters de treinamento__ da central e, em seguida, selecione __+__ .
    1. Na caixa de diálogo __Novo cluster de treinamento__, expanda a seção __Configurações avançadas__.
    1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações na seção __Configurar rede virtual__:

        1. Na lista suspensa __Grupo de recursos__, selecione o grupo de recursos que contém a rede virtual.
        1. Na lista suspensa __Rede virtual__, selecione a rede virtual que contém a sub-rede.
        1. Na lista suspensa __Sub-rede__, selecione a sub-rede a ser usada.

    ![As configurações de rede virtual para a Computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    Também é possível criar um cluster de Computação do Machine Learning, usando o SDK do Azure Machine Learning. O código a seguir cria um novo cluster de Computação do Machine Learning na sub-rede `default` de uma rede virtual chamada `mynetwork`:

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    Após concluir o processo de criação, você treina seu modelo usando o cluster em um experimento. Para obter mais informações, consulte [Selecionar e usar um destino de computação para treinamento](how-to-set-up-training-targets.md).

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Se você estiver usando blocos de anotações em uma instância de computação do Azure, certifique-se de que o bloco de anotações esteja sendo executado em um recurso de computação por trás da mesma rede virtual e sub-rede que seus dados. 

    Configure sua instância de computação para estar na mesma rede virtual durante a criação em **Configurações avançadas**  >  **Configurar rede virtual**. Você não pode adicionar uma instância de computação existente a uma rede virtual.

* Se você planeja proteger a rede virtual restringindo o tráfego de rede de/para a Internet pública, você deve permitir comunicações de entrada do serviço de lote do Azure.

    O serviço de Lote adiciona grupos de segurança de rede (NSGs) no nível dos adaptadores de rede (NICs) anexados às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

    - Tráfego TCP de entrada nas portas 29876 e 29877 a partir de uma __Marca de serviço__ de __BatchNodeManagement__.

    - (Opcional) Tráfego TCP de entrada na porta 22 para permitir acesso remoto. Use essa porta somente se quiser se conectar usando SSH no IP público.

    - Tráfego de saída em qualquer porta para a rede virtual.

    - Tráfego de saída em qualquer porta para a internet.

    - Para o tráfego TCP de entrada da instância de computação na porta 44224 de uma __Marca de serviço__ do __AzureMachineLearning__.

    > [!IMPORTANT]
    > Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se um NSG bloquear a comunicação com os nós de computação, os serviços de computação definem o estado dos nós de computação como inutilizáveis.
    >
    > Não é necessário especificar NSGs no nível de sub-rede porque o serviço do Lote do Azure configura seus próprios NSGs. No entanto, se a sub-rede que contém o Azure Machine Learning computação tiver associado NSGs ou um firewall, você também deverá permitir o tráfego listado anteriormente.

* Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, siga as seguintes etapas:

    1. Negue a conexão de Internet de saída usando as regras de NSG.
    1. Para uma __instância de computação__ ou um __cluster de cálculo__, limite o tráfego de saída para os seguintes itens:
        - Armazenamento do Azure que usa a __Marca de serviço__ de __Storage.RegionName__. Em que `{RegionName}` é o nome de uma região do Azure.
        - Registro de Contêiner do Azure, usando __Marca de serviço__ de __AzureContainerRegistry.RegionName__. Em que `{RegionName}` é o nome de uma região do Azure.
        - Azure Machine Learning, usando a __Marca de serviço__ de __AzureMachineLearning__
        - Azure Resource Manager, usando a __Marca de serviço__ de __AzureResourceManager__
        - Azure Active Directory, usando a __Marca de serviço__ de __AzureActiveDirectory__

    > [!NOTE]
    > Se você planeja usar imagens padrão do Docker fornecidas pela Microsoft e habilitando dependências gerenciadas pelo usuário, você também deve usar as seguintes __marcas de serviço__:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > Essa configuração é necessária quando você tem um código semelhante aos seguintes trechos como parte de seus scripts de treinamento:
    >
    > __Treinamento do RunConfig__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Treinamento do avaliador__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* Se você estiver usando o [túnel forçado](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) com Azure Machine Learning computação, deverá permitir a comunicação com a Internet pública da sub-rede que contém o recurso de computação. Essa comunicação é usada para agendamento de tarefas e acesso ao armazenamento do Azure.

    Há duas maneiras de fazer isso:

    * Use um [NAT de rede virtual](../virtual-network/nat-overview.md). Um gateway NAT fornece conectividade de Internet de saída para uma ou mais sub-redes em sua rede virtual. Para obter informações, consulte [Designing Virtual Networks with NAT gateway Resources](../virtual-network/nat-gateway-resource.md).

    * Adicione [UDRs (rotas definidas pelo usuário)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso de computação. Estabeleça uma UDR para cada endereço IP usado pelo serviço do Lote do Azure na região onde existem seus recursos. Essas UDRs permitem que o serviço de Lote se comunique com os nós de computação para agendamento de tarefas. Adicione também o endereço IP para o serviço do Azure Machine Learning onde estão os recursos, já que isso é necessário para acessar as Instâncias de Computação. Use um dos seguintes métodos para obter uma lista de endereços IP do serviço de Lote e do serviço do Azure Machine Learning:

        * Baixe os [Intervalos de IP do Azure e as Marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) e pesquise `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` no arquivo, em que `<region>` é a sua região do Azure.

        * Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para baixar as informações. O exemplo a seguir baixa as informações do endereço IP e filtra as informações para a região Leste dos EUA 2:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Quando você adicionar as UDRs, defina a rota para cada prefixo de endereço IP do Lote relacionado e defina __Próximo tipo de salto__ como __Internet__. 

        Além de qualquer UDRs que você definir, o tráfego de saída para o armazenamento do Azure deve ser permitido por meio de seu dispositivo de rede local. Especificamente, as URLs para esse tráfego estão nas seguintes formas: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` . 

        Para obter mais informações, consulte [Criar um pool de Lote do Azure em uma rede virtual](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

__Requisitos__

* A instância do AKS (serviço kubernetes do Azure) e a rede virtual do Azure devem estar na mesma região. Se você proteger as contas de armazenamento do Azure usadas pelo espaço de trabalho em uma rede virtual, elas deverão estar na mesma rede virtual que a instância AKS.

* Para __planejar o endereçamento IP__ para o cluster, siga os pré-requisitos no artigo [Configurar a rede avançada no AKs (serviço de kubernetes do Azure)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Para restringir a comunicação de entrada e saída ao cluster AKS, siga as orientações no artigo [controlar o tráfego de saída para nós de cluster no serviço de kubernetes do Azure](/azure/aks/limit-egress-traffic) para garantir que a comunicação de _saída_ do AKS esteja configurada corretamente. Os requisitos de comunicação de _entrada_ , se houver, são chamados na seção de configuração abaixo.

__Limitações__

* Se você quiser usar um serviço kubernetes do Azure que tenha o link privado habilitado, você deverá anexá-lo ao seu espaço de trabalho. Não é possível criar um cluster do serviço kubernetes do Azure com o link privado do Azure Machine Learning (SDK, portal, CLI, etc.).

__Configuration__

> [!IMPORTANT]
> Esta seção contém várias configurações. Selecione aquele que melhor atenda às suas necessidades.

* Para __usar o AKs por trás da rede virtual, com um balanceador de carga público__:

    1. Crie ou anexe o cluster AKS. Se você estiver __criando__ um novo cluster, deverá especificar a rede virtual a ser usada para o cluster.
    
        O exemplo a seguir demonstra como criar um novo cluster AKS usando o SDK do Python:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Se você tiver um cluster AKS existente que já está por trás da rede virtual, use as informações no artigo [implantar no Azure kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Certifique-se de que o grupo de segurança de rede que controla a rede virtual tenha uma regra de segurança de __entrada__ habilitada para o ponto de extremidade de Pontuação para que ele possa ser chamado de fora da rede virtual.

* Para __usar o AKs por trás da rede virtual, com um balanceador de carga privado__:

    1. Crie ou anexe o cluster AKS. Se você estiver __criando__ um novo cluster, deverá especificar a rede virtual a ser usada para o cluster.
    
        O exemplo a seguir demonstra como criar um novo cluster AKS usando o SDK do Python:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Se você tiver um cluster AKS existente que já está por trás da rede virtual, use as informações no artigo [implantar no Azure kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Para localizar a entidade de serviço ou a ID de identidade gerenciada para AKS, use os seguintes comandos de CLI do Azure. Substitua `<aks-cluster-name>` pelo nome do cluster. Substitua `<resource-group-name>` pelo nome do grupo de recursos que _contém o cluster AKs_:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Se esse comando retornar um valor de `msi` , use o seguinte comando para identificar a ID da entidade de segurança para a identidade gerenciada:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Para localizar a ID do grupo de recursos que contém sua rede virtual, use o comando a seguir. Substitua `<resource-group-name>` pelo nome do grupo de recursos que _contém a rede virtual_:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Para adicionar a entidade de serviço ou a identidade gerenciada como um colaborador de rede, use o comando a seguir. Substituir `<SP-or-managed-identity>` pela ID retornada para a entidade de serviço ou identidade gerenciada. Substituir `<resource-group-id>` pela ID retornada para o grupo de recursos que contém a rede virtual:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Para atualizar o cluster AKS para usar um __balanceador de carga privado__, use o SDK do Python. O trecho de código a seguir demonstra como atualizar um cluster AKS existente que foi adicionado ou anexado ao espaço de trabalho:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* Para __anexar AKs usando um ponto de extremidade privado__:

    1. Use o comando CLI do Azure a seguir para obter a __ID de sub-rede__ da sub-rede que o cluster AKs usará. Por exemplo, a sub-rede padrão para sua rede virtual:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Esse comando retorna uma matriz de IDs para as sub-redes na rede virtual. O JSON a seguir é um exemplo de uma rede virtual que tem apenas uma sub-rede:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Se várias IDs forem retornadas, selecione aquela que você deseja usar.

    1. Para criar um cluster AKS como um ponto de extremidade privado, use as informações na seção __rede avançada__ do artigo [criar um cluster do serviço kubernetes do Azure privado](/azure/aks/private-clusters#advanced-networking) . Ao criar o cluster, use a ID de sub-rede do comando anterior com o `--vnet-subnet-id` parâmetro.

    1. Para anexar o cluster, use as informações no artigo [implantar no Azure kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > Ao usar essa configuração e restringir a comunicação de saída do cluster AKS, você não precisa permitir a comunicação nas portas __1194__ ou __9000__. Para outras portas que devem ser permitidas, consulte as diretrizes no artigo [controlar o tráfego de saída para nós de cluster no serviço kubernetes do Azure](/azure/aks/limit-egress-traffic) .

## <a name="use-azure-container-instances-aci"></a>Usar Instâncias de Contêiner do Azure (ACI)

__Requisitos__

* As Instâncias de Contêiner do Azure são criadas dinamicamente durante a implantação de um modelo. Para habilitar a Azure Machine Learning para criar ACI dentro da rede virtual, você deve habilitar a __delegação de sub-rede__ para a sub-rede usada pela implantação.

__Limitações__

* A rede virtual deve estar no mesmo grupo de recursos que seu espaço de trabalho do Azure Machine Learning.

* O ACR (registro de contêiner do Azure) para seu espaço de trabalho também não pode estar na rede virtual.

__Configuration__

Para usar a ACI em uma rede virtual no seu workspace, siga as seguintes etapas:

1. Para habilitar a delegação de sub-rede em sua rede virtual, use as informações do artigo [Adicionar ou remover uma delegação de sub-rede](../virtual-network/manage-subnet-delegation.md). É possível habilitar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao habilitar a delegação, use `Microsoft.ContainerInstance/containerGroups` como o valor de __Delegar sub-rede ao serviço__.

2. Implante o modelo usando [AciWebservice. deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), use os parâmetros `vnet_name` e `subnet_name`. Defina esses parâmetros para o nome da rede virtual e a sub-rede onde você habilitou a delegação.

## <a name="azure-databricks"></a>Azure Databricks

__Requisitos__

* A rede virtual deve estar na mesma assinatura e região que o workspace do Azure Machine Learning.
* Se as Contas de Armazenamento do Azure para o workspace também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que o cluster do Azure Databricks.
* Além das sub-redes __databricks-particular__ e  __databricks-público__ usadas pelos Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

__Limitações__

__Configuration__

Para obter informações específicas sobre o uso do Azure Databricks com uma rede virtual, consulte [Implantar o Azure Databricks na sua Rede Virtual do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Máquina virtual ou cluster HDInsight

__Requisitos__

* O Azure Machine Learning dá suporte apenas a máquinas virtuais que executam Ubuntu.
* A porta SSH deve ser habilitada na máquina virtual ou no cluster HDInsight.

__Limitações__

__Configuration__

1. Crie um cluster de VM ou HDInsight usando o portal do Azure ou a CLI do Azure e coloque-o em uma rede virtual do Azure. Para obter mais informações, consulte os seguintes artigos:

    * [Criar e gerenciar redes virtuais do Azure para VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender HDInsight usando uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Um NSG é criado automaticamente para máquinas virtuais do Azure baseadas em Linux. Esse NSG permite o acesso à porta 22 de qualquer fonte. Se você quiser limitar o acesso à porta SSH, deverá permitir o acesso de Azure Machine Learning. Para preservar o acesso para o Azure ML, você deve permitir o acesso de um __serviço de origem__ com uma marca de __serviço de origem__ de __AzureMachineLearning__. Por exemplo, os comandos CLI do Azure a seguir modificam a regra SSH para permitir o acesso somente de Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Para obter mais informações, consulte a seção [criar grupos de segurança de rede](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) do artigo redes virtuais do Azure para máquinas virtuais do Linux.
    
    Mantenha as regras de saída padrão para o grupo de segurança de rede. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

1. Anexe o cluster da VM ou do HDInsight ao seu workspace do Azure Machine Learning. Para obter mais informações, consulte [Configurar destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Próximas etapas

* [Use Azure Machine Learning espaço de trabalho por trás do firewall do Azure](how-to-access-azureml-behind-firewall.md).
* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Configurar pontos de extremidade privados](how-to-configure-private-link.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
