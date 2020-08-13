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
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: 947f7afba6a8b40e9b1c71ac817239dd039539f7
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192409"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Isolamento de rede durante o treinamento & inferência com redes virtuais privadas
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger seus ciclos de vida de aprendizado de máquina isolando Azure Machine Learning trabalhos de inferência e treinamento em uma vnet (rede virtual) do Azure. Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação, também conhecidos como [destinos de computação](concept-compute-target.md), para treinar e implantar modelos. Os destinos podem ser criados dentro de uma rede virtual. Por exemplo, você pode usar a computação do Azure Machine Learning para treinar um modelo e, em seguida, implantar o modelo no Serviço de Kubernetes do Azure (AKS). 

Uma __rede virtual__ atua como um limite de segurança, isolando os recursos do Azure da Internet pública. Além disso, é possível ingressar em uma rede virtual do Azure na rede local. Ao ingressar em redes, você pode treinar com segurança seus modelos e acessar seus modelos implantados para inferência.

## <a name="prerequisites"></a>Pré-requisitos

+ Um [workspace](how-to-manage-workspace.md) do Azure Machine Learning.

+ Conhecimento geral de trabalho do [serviço da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e de [rede IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Rede e sub-rede virtuais pré-existentes para usar com os recursos de computação.

+ Para implantar recursos em uma rede virtual ou sub-rede, sua conta de usuário deve ter permissões para as seguintes ações nos controles de acesso baseado em função (RBAC) do Azure:

    - "Microsoft. Network/virtualNetworks/Join/Action" no recurso de rede virtual.
    - "Microsoft. Network/virtualNetworks/sub-rede/junção/ação" no recurso de sub-rede.

    Para obter mais informações sobre o RBAC com rede, consulte [funções internas de rede](/azure/role-based-access-control/built-in-roles#networking)

## <a name="private-endpoints"></a>Pontos de extremidade privados

Você também pode [habilitar o Link Privado do Azure](how-to-configure-private-link.md) para se conectar ao seu workspace usando um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados na sua rede virtual. [Saiba como configurar esse ponto de extremidade privado.](how-to-configure-private-link.md)



> [!TIP]
> Você pode combinar a rede virtual com o Link Privado para proteger a comunicação entre seu workspace e outros recursos do Azure. No entanto, algumas combinações exigem um workspace da edição Enterprise. Use a tabela a seguir para entender quais cenários exigem a edição Enterprise:
>
> | Cenário | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | Nenhuma rede virtual ou Link Privado | ✔ | ✔ |
> | Workspace sem Link Privado. Outros recursos (exceto o Registro de Contêiner do Azure) em uma rede virtual | ✔ | ✔ |
> | Workspace sem Link Privado. Outros recursos com Link Privado | ✔ | |
> | Workspace com Link Privado. Outros recursos (exceto o Registro de Contêiner do Azure) em uma rede virtual | ✔ | ✔ |
> | Workspace e qualquer outro recurso com Link Privado | ✔ | |
> | Workspace com Link Privado. Outros recursos sem Link Privado ou rede virtual | ✔ | ✔ |
> | Registro de Contêiner do Azure em uma rede virtual | ✔ | |
> | Chaves gerenciadas pelo cliente para workspace | ✔ | |
> 

> [!WARNING]
> 
> Não há suporte para a versão prévia de instâncias de computação do Azure Machine Learning em um workspace em que o Link Privado está habilitado.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

Se os dados estiverem armazenados em uma rede virtual, você deverá usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) do espaço de trabalho para conceder ao estúdio acesso aos seus dados.

> [!IMPORTANT]
> Embora a maioria do estúdio funcione com dados armazenados em uma rede virtual, os notebooks integrados __não__. Os blocos de anotações integrados não dão suporte ao uso do armazenamento que está em uma rede virtual. Em vez disso, você pode usar blocos de anotações do Jupyter de uma instância de computação. Para obter mais informações, consulte a seção [acessar dados em um notebook da instância de computação](#access-data-in-a-compute-instance-notebook) .

Se você não conceder acesso ao estúdio, receberá esse erro `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` e desabilitará as seguintes operações:

* Visualizar dados no estúdio.
* Visualize dados no designer.
* Envie um experimento do AutoML.
* Inicie um projeto de rotulagem.

O estúdio dá suporte à leitura de dados dos seguintes tipos de repositório de armazenamento em uma rede virtual:

* Blob do Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Banco de Dados SQL do Azure

### <a name="add-resources-to-the-virtual-network"></a>Adicionar recursos à rede virtual 

Adicione seu espaço de trabalho e a conta de armazenamento à mesma rede virtual para que eles possam acessar um ao outro.

1. Para conectar seu espaço de trabalho à rede virtual, [habilite o link privado do Azure](how-to-configure-private-link.md). Esse recurso está atualmente em visualização e está disponível nas regiões do leste dos EUA, oeste dos EUA 2, centro-sul dos EUA.

1. Para conectar sua conta de armazenamento à rede virtual, [defina as configurações de firewalls e redes virtuais](#use-a-storage-account-for-your-workspace).

### <a name="configure-a-datastore-to-use-managed-identity"></a>Configurar um repositório de armazenamento para usar identidade gerenciada

Depois de adicionar seu espaço de trabalho e a conta de serviço de armazenamento à rede virtual, você precisa configurar os armazenamentos de dados para usar identidade gerenciada para acessar os seus serviços. Essas etapas adicionam a identidade gerenciada do espaço de trabalho como um __leitor__ ao serviço de armazenamento usando o controle de acesso baseado em recursos (RBAC) do Azure. O acesso de __leitor__ permite que o espaço de trabalho recupere as configurações de firewall e verifique se os dados não deixam a rede virtual.

1. No estúdio, selecione __repositórios de armazenamento__.

1. Para criar um novo repositório de armazenamento, selecione __+ novo repositório de armazenamento__. Para atualizar um existente, selecione o repositório de armazenamento e selecione __Atualizar credenciais__.

1. Nas configurações do repositório de armazenamento, selecione __Sim__ para  __permitir que o serviço de Azure Machine Learning acesse o armazenamento usando a identidade gerenciada do espaço de trabalho__.

> [!NOTE]
> Essas alterações podem levar até 10 minutos para entrar em vigor.

### <a name="azure-blob-storage-blob-data-reader"></a>Leitor de dados de blob do armazenamento de BLOBs do Azure

Para o __armazenamento de BLOBs do Azure__, a identidade gerenciada do espaço de trabalho também é adicionada como um [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados do armazenamento de BLOBs.


### <a name="azure-machine-learning-designer-default-datastore"></a>Repositório de armazenamento padrão do Azure Machine Learning designer

O designer usa a conta de armazenamento anexada ao seu espaço de trabalho para armazenar a saída por padrão. No entanto, você pode especificá-lo para armazenar a saída em qualquer repositório de armazenamento ao qual você tenha acesso. Se seu ambiente usa redes virtuais, você pode usar esses controles para garantir que seus dados permaneçam seguros e acessíveis.

Para definir um novo armazenamento padrão para um pipeline:

1. Em um rascunho de pipeline, selecione o **ícone de engrenagem configurações** próximo ao título do seu pipeline.
1. Selecione **selecionar repositório de armazenamento padrão**.
1. Especifique um novo repositório de armazenamento.

Você também pode substituir o repositório de armazenamento padrão por módulo. Isso lhe dá controle sobre o local de armazenamento de cada módulo individual.

1. Selecione o módulo cuja saída você deseja especificar.
1. Expanda a seção **configurações de saída** .
1. Selecione **Substituir configurações de saída padrão**.
1. Selecione **definir configurações de saída**.
1. Especifique um novo repositório de armazenamento.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controle de acesso Azure Data Lake Storage Gen2

Você pode usar as ACLs (listas de controle de acesso) de estilo RBAC e POSIX para controlar o acesso a dados dentro de uma rede virtual.

Para usar o RBAC, adicione a identidade gerenciada do espaço de trabalho à função de [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Para obter mais informações, confira [Controle de acesso baseado em função](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

Para usar ACLs, a identidade gerenciada do espaço de trabalho pode ser atribuída ao acesso, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="azure-data-lake-storage-gen1-access-control"></a>Controle de acesso Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 oferece suporte apenas a listas de controle de acesso em estilo POSIX. Você pode atribuir o acesso de identidade gerenciada do espaço de trabalho a recursos, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).


### <a name="azure-sql-database-contained-user"></a>O banco de dados SQL do Azure continha o usuário

Para acessar os dados armazenados em um Azure SQL Database usando a identidade gerenciada, você deve criar um usuário contido no SQL que mapeia para a identidade gerenciada. Para obter mais informações sobre como criar um usuário de um provedor externo, consulte [criar usuários independentes mapeados para identidades do Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Depois de criar um usuário do SQL continha, conceda permissões a ele usando o [comando Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="connect-to-the-studio"></a>Conectar-se ao estúdio

Se você estiver acessando o Studio de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou máquina virtual), deverá permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se você estiver usando NSG (grupos de segurança de rede) para restringir o tráfego de saída, adicione uma regra a um destino de __marca de serviço__ de __AzureFrontDoor. frontend__.

## <a name="use-a-storage-account-for-your-workspace"></a>Use a conta de armazenamento para o seu workspace

> [!IMPORTANT]
> Você pode colocar a _conta de armazenamento padrão_ para o Azure Machine Learning ou as _contas de armazenamento não padrão_ em uma rede virtual.
>
> A conta de armazenamento padrão é provisionada automaticamente quando você cria um workspace.
>
> O parâmetro `storage_account` na função [`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) permite que você especifique uma conta de armazenamento personalizada pela ID do recurso do Azure em contas de armazenamento não padrão.

Para usar um serviço de armazenamento do Azure para o espaço de trabalho em uma rede virtual, use as seguintes etapas:

1. Crie um recurso de computação (por exemplo, um cluster ou instância de computação do Machine Learning) atrás de uma rede virtual ou anexe um recurso de computação ao workspace (por exemplo, um cluster HDInsight, uma máquina virtual ou um cluster do Serviço de Contêiner do Azure). O recurso de computação pode ser para experimentação ou implantação de modelo.

   Para obter mais informações, consulte neste artigo as seções [Usar uma computação do Machine Learning](#amlcompute), [Usar uma máquina virtual ou cluster HDInsight](#vmorhdi) e [Usar o Serviço de Kubernetes do Azure](#aksvnet).

1. Na portal do Azure, vá para o serviço de armazenamento que você deseja usar em seu espaço de trabalho.

   [![O armazenamento anexado ao workspace do Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página conta de serviço de armazenamento, selecione __firewalls e redes virtuais__.

   ![A área “Firewalls e redes virtuais” na página Armazenamento do Azure no portal do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na página __Firewalls e redes virtuais__, execute as seguintes ações:
    - Selecione __Redes selecionadas__.
    - Em __Redes virtuais__, selecione o link __Adicionar rede virtual existente__. Essa ação adiciona a rede virtual onde sua computação reside (consulte a etapa 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias ou clusters de computação usados para treinamento ou inferência.

    - Marque a caixa de seleção __Permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento__.

    > [!IMPORTANT]
    > Ao trabalhar com o SDK do Azure Machine Learning, seu ambiente de desenvolvimento deve ter a capacidade de se conectar à Conta de Armazenamento do Azure. O firewall deve permitir o acesso do endereço IP do ambiente de desenvolvimento quando a conta de armazenamento está dentro de uma rede virtual.
    >
    > Para habilitar o acesso à conta de armazenamento, acesse __Firewalls e redes virtuais__ para a conta de armazenamento *de um navegador da Web no cliente de desenvolvimento*. Em seguida, use a caixa de seleção __Adicionar endereço IP do seu cliente__ para adicionar o endereço IP do cliente ao __INTERVALO DE ENDEREÇOS__. Você também pode usar o campo __INTERVALO DE ENDEREÇOS__ para inserir manualmente o endereço IP do ambiente de desenvolvimento. Após adicionar o endereço IP do cliente, ele poderá acessar a conta de armazenamento usando o SDK.

   [![O painel “Firewalls e redes virtuais” no portal do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>Usar repositórios de armazenamento e conjuntos de valores

Esta seção aborda o uso do datastore e do conjunto de conhecimento para a experiência do SDK. Para obter mais informações sobre a experiência do estúdio, consulte a seção [Machine Learning Studio](#machine-learning-studio).

Por padrão, Azure Machine Learning executa as verificações de validade e credencial de dados quando você tenta acessar dados usando o SDK. Se seus dados estiverem atrás de uma rede virtual, Azure Machine Learning não poderá acessar os dados e falhará em suas verificações. Para evitar isso, você deve criar repositórios de armazenamento e conjuntos de valores que ignoram a validação.

### <a name="use-a-datastore"></a>Usar um repositório de armazenamento

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

### <a name="use-a-dataset"></a>Usar um conjunto de dados

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


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters e instâncias de computação 

Para usar um [destino de computação__do__Azure Machine Learning gerenciado](concept-compute-target.md#azure-machine-learning-compute-managed) ou uma [instância de computação do __Azure Machine Learning__](concept-compute-instance.md) em uma rede virtual, os requisitos de rede a seguir devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o workspace do Azure Machine Learning.
> * A sub-rede especificada para a instância ou cluster de computação deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs destinadas. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de cálculo será parcialmente alocado.
> * Verifique se as políticas de segurança, os bloqueios na assinatura da rede virtual ou o grupo de recursos restringem permissões para gerenciar a rede virtual. Se você planeja proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para saber mais, consulte a seção [Portas necessárias](#mlcports).
> * Se você for colocar várias instâncias ou clusters de computação em uma rede virtual, talvez seja necessário solicitar um aumento de cota para um ou mais de seus recursos.
> * Se as Contas de Armazenamento do Azure para o workspace também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que a instância ou cluster de computação do Azure Machine Learning. 
> * Para que a funcionalidade de Jupyter da instância de computação funcione, verifique se a comunicação do soquete da Web não está desabilitada.

> [!TIP]
> A instância ou cluster de computação do Machine Learning aloca automaticamente recursos adicionais de rede __no grupo de recursos que contém a rede virtual__. Para cada instância ou cluster de computação, o serviço aloca os seguintes recursos:
> 
> * Um grupo de segurança de rede
> * Um endereço IP público
> * Um balanceador de carga
> 
> No caso de clusters, esses recursos são excluídos (e recriados) sempre que o cluster é reduzido para 0 nós. No entanto, para uma instância, os recursos são mantidos até que a instância seja completamente excluída (a interrupção não remove os recursos). 
> Esses recursos são limitados pelas [cotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da assinatura.


### <a name="required-ports"></a><a id="mlcports"></a> Portas obrigatórias

Se você planeja proteger a rede virtual restringindo o tráfego de rede de/para a Internet pública, você deve permitir comunicações de entrada do serviço de lote do Azure.

O serviço de Lote adiciona grupos de segurança de rede (NSGs) no nível dos adaptadores de rede (NICs) anexados às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

- Tráfego TCP de entrada nas portas 29876 e 29877 a partir de uma __Marca de serviço__ de __BatchNodeManagement__.

    ![Uma regra de entrada que usa a marca de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Opcional) Tráfego TCP de entrada na porta 22 para permitir acesso remoto. Use essa porta somente se quiser se conectar usando SSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a internet.

- Para o tráfego TCP de entrada da instância de computação na porta 44224 de uma __Marca de serviço__ do __AzureMachineLearning__.

> [!IMPORTANT]
> Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se um NSG bloquear a comunicação com os nós de computação, os serviços de computação definem o estado dos nós de computação como inutilizáveis.
>
> Não é necessário especificar NSGs no nível de sub-rede porque o serviço do Lote do Azure configura seus próprios NSGs. No entanto, se a sub-rede que contém o Azure Machine Learning computação tiver associado NSGs ou um firewall, você também deverá permitir o tráfego listado anteriormente.

A configuração da regra de NSG no portal do Azure é mostrada nas seguintes imagens:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="As regras de NSG de entrada para Computação do Machine Learning" border="true":::



![As regras de NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Limitar a conectividade de saída da rede virtual

Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, siga as seguintes etapas:

- Negue a conexão de Internet de saída usando as regras de NSG.

- Para uma __instância de computação__ ou um __cluster de cálculo__, limite o tráfego de saída para os seguintes itens:
   - Armazenamento do Azure que usa a __Marca de serviço__ de __Storage.RegionName__. Em que `{RegionName}` é o nome de uma região do Azure.
   - Registro de Contêiner do Azure, usando __Marca de serviço__ de __AzureContainerRegistry.RegionName__. Em que `{RegionName}` é o nome de uma região do Azure.
   - Azure Machine Learning, usando a __Marca de serviço__ de __AzureMachineLearning__
   - Azure Resource Manager, usando a __Marca de serviço__ de __AzureResourceManager__
   - Azure Active Directory, usando a __Marca de serviço__ de __AzureActiveDirectory__

A configuração da regra de NSG no portal do Azure é mostrada na seguinte imagem:

[![As regras de NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

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

### <a name="forced-tunneling"></a>Túnel forçado

Se você estiver usando o [túnel forçado](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) com Azure Machine Learning computação, deverá permitir a comunicação com a Internet pública da sub-rede que contém o recurso de computação. Essa comunicação é usada para agendamento de tarefas e acesso ao armazenamento do Azure.

Há duas maneiras de fazer isso:

* Use um [NAT de rede virtual](../virtual-network/nat-overview.md). Um gateway NAT fornece conectividade de Internet de saída para uma ou mais sub-redes em sua rede virtual. Para obter informações, consulte [Designing Virtual Networks with NAT gateway Resources](../virtual-network/nat-gateway-resource.md).

* Adicione [UDRs (rotas definidas pelo usuário)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso de computação. Estabeleça uma UDR para cada endereço IP usado pelo serviço do Lote do Azure na região onde existem seus recursos. Essas UDRs permitem que o serviço de Lote se comunique com os nós de computação para agendamento de tarefas. Adicione também o endereço IP para o serviço do Azure Machine Learning onde estão os recursos, já que isso é necessário para acessar as Instâncias de Computação. Use um dos seguintes métodos para obter uma lista de endereços IP do serviço de Lote e do serviço do Azure Machine Learning:

    * Baixe os [Intervalos de IP do Azure e as Marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) e pesquise `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` no arquivo, em que `<region>` é a sua região do Azure.

    * Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para baixar as informações. O exemplo a seguir baixa as informações do endereço IP e filtra as informações para a região Leste dos EUA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```
    
    Quando você adicionar as UDRs, defina a rota para cada prefixo de endereço IP do Lote relacionado e defina __Próximo tipo de salto__ como __Internet__. A imagem a seguir mostra um exemplo dessa UDR no portal do Azure:

    ![Exemplo de uma UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

    Além de qualquer UDRs que você definir, o tráfego de saída para o armazenamento do Azure deve ser permitido por meio de seu dispositivo de rede local. Especificamente, as URLs para esse tráfego estão nas seguintes formas: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` . 

    Para obter mais informações, consulte [Criar um pool de Lote do Azure em uma rede virtual](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Criar um cluster de cálculo em uma rede virtual

Para criar um cluster de Computação do Machine Learning, siga as seguintes etapas:

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

### <a name="access-data-in-a-compute-instance-notebook"></a>Acessar dados em um bloco de anotações de instância de computação

Se você estiver usando blocos de anotações em uma instância de computação do Azure, certifique-se de que o bloco de anotações esteja sendo executado em um recurso de computação por trás da mesma rede virtual e sub-rede que seus dados. 

Você deve configurar sua instância de computação para estar na mesma rede virtual durante a criação em **Configurações avançadas**  >  **Configurar rede virtual**. Você não pode adicionar uma instância de computação existente a uma rede virtual.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Para adicionar o Serviço de Kubernetes do Azure (AKS) em uma rede virtual ao seu workspace, siga as seguintes etapas:

> [!IMPORTANT]
> Antes de começar o procedimento a seguir, siga os pré-requisitos nas instruções presentes em [Configurar rede avançada no AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) e planeje o endereçamento IP para o seu cluster.
>
> A instância do AKS e a rede virtual do Azure devem estar na mesma região. Se você proteger as Contas de Armazenamento do Azure usadas pelo workspace em uma rede virtual, elas deverão estar na mesma rede virtual que a instância do AKS.

1. Entre no [estúdio do Azure Machine Learning](https://ml.azure.com/) e selecione sua assinatura e workspace.

1. Selecione __Computação__ à esquerda.

1. Selecione __Clusters de inferência__ da central e, em seguida, selecione __+__ .

1. Na caixa de diálogo __Novo cluster de inferência__, selecione __Avançado__ em __Configuração de rede__.

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações:

    1. Na lista suspensa __Grupo de recursos__, selecione o grupo de recursos que contém a rede virtual.
    1. Na lista suspensa __Rede virtual__, selecione a rede virtual que contém a sub-rede.
    1. Na lista suspensa __Sub-rede__, selecione a sub-rede.
    1. Na caixa __Intervalo de endereços do serviço Kubernetes__ , insira o intervalo de endereços do serviço Kubernetes. Esse intervalo de endereços usa um intervalo de IP de notação de Roteamento entre Domínios sem Classificação (CIDR) para definir os endereços IP disponíveis para o cluster. Ele não deve se sobrepor a nenhum intervalo de IP de sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa __Endereço IP do serviço DNS do Kubernetes__ , insira o endereço IP do serviço de DNS do Kubernetes. Esse endereço IP é atribuído ao serviço DNS do Kubernetes. Ele deve estar dentro do intervalo de endereços do serviço Kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa __Endereço de ponte do Docker__, insira o endereço da ponte do Docker. Esse endereço IP é atribuído à ponte Docker. Ele não deve estar em nenhum intervalo de IP de sub-rede nem no intervalo de endereços do serviço Kubernetes (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Configurações de rede virtual de Computação do Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verifique se o grupo de NSG que controla a rede virtual tem uma regra de segurança de entrada habilitada para o ponto de extremidade de pontuação para que ela possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também é possível usar o SDK do Azure Machine Learning para adicionar o Serviço de Kubernetes do Azure a uma rede virtual. Se você já tem um cluster do AKS em uma rede virtual, anexe-o ao workspace conforme descrito em [Como implantar no AKS](how-to-deploy-and-where.md). O código a seguir cria uma nova instância de AKS na `default`sub-rede de uma rede virtual nomeada`mynetwork`:

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

Quando o processo de criação for concluído, você pode executar inferência ou pontuação de modelo em um cluster de AKS atrás de uma rede virtual. Para obter mais informações, consulte [Como implantar no AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Use IPs particulares com o Serviço de Kubernetes do Azure

Por padrão, um endereço IP público é atribuído às implantações do AKS. Ao usar o AKS dentro de uma rede virtual, você pode usar um endereço IP particular em vez disso. Os endereços IP particulares só podem ser acessados de dentro da rede virtual ou de redes unidas.

Um endereço IP particular é habilitado configurando o AKS para usar um _balanceador de carga interno_. 

#### <a name="network-contributor-role"></a>Função de colaborador de rede

> [!IMPORTANT]
> Se você criar ou anexar um cluster AKS fornecendo uma rede virtual criada anteriormente, deverá conceder a entidade de serviço (SP) ou a identidade gerenciada para seu cluster AKS a função de _colaborador de rede_ para o grupo de recursos que contém a rede virtual. Isso deve ser feito antes de tentar alterar o balanceador de carga interno para o IP privado.
>
> Para adicionar a identidade como colaborador de rede, use as seguintes etapas:

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
Para obter mais informações sobre como usar o balanceador de carga interno com o AKS, consulte [Usar o balanceador de carga interno com o Serviço de Kubernetes do Azure](/azure/aks/internal-lb).

#### <a name="enable-private-ip"></a>Habilitar IP privado

> [!IMPORTANT]
> Você não pode habilitar o IP particular ao criar o cluster do Serviço de Kubernetes do Azure. Ele deve estar habilitado como uma atualização de um cluster existente.

O trecho de código a seguir demonstra como __criar um novo cluster de AKS__ e, em seguida, atualizá-lo para usar um balanceador de carga interno/IP particular:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

O conteúdo do arquivo `body.json` referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Ao __anexar um cluster existente__ ao seu espaço de trabalho, você deve aguardar até que a operação de anexação configure o balanceador de carga.

Para obter informações sobre como anexar um cluster, consulte [anexar um cluster AKs existente](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster).

Depois de anexar o cluster existente, você pode atualizar o cluster para usar um IP privado.

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

## <a name="use-azure-container-instances-aci"></a>Usar Instâncias de Contêiner do Azure (ACI)

As Instâncias de Contêiner do Azure são criadas dinamicamente durante a implantação de um modelo. Para habilitar a Azure Machine Learning para criar ACI dentro da rede virtual, você deve habilitar a __delegação de sub-rede__ para a sub-rede usada pela implantação.

> [!WARNING]
> Ao usar instâncias de contêiner do Azure em uma rede virtual, a rede virtual deve estar no mesmo grupo de recursos que seu espaço de trabalho do Azure Machine Learning.
>
> Ao usar as instâncias de contêiner do Azure dentro da rede virtual, o ACR (registro de contêiner do Azure) para seu espaço de trabalho também não pode estar na rede virtual.

Para usar a ACI em uma rede virtual no seu workspace, siga as seguintes etapas:

1. Para habilitar a delegação de sub-rede em sua rede virtual, use as informações do artigo [Adicionar ou remover uma delegação de sub-rede](../virtual-network/manage-subnet-delegation.md). É possível habilitar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao habilitar a delegação, use `Microsoft.ContainerInstance/containerGroups` como o valor de __Delegar sub-rede ao serviço__.

2. Implante o modelo usando [AciWebservice. deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), use os parâmetros `vnet_name` e `subnet_name`. Defina esses parâmetros para o nome da rede virtual e a sub-rede onde você habilitou a delegação.

## <a name="azure-firewall"></a>Firewall do Azure

Para obter informações sobre como usar o Azure Machine Learning com o Firewall do Azure, consulte [Usar o workspace do Azure Machine Learning atrás do Firewall do Azure](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Registro de Contêiner do Azure

> [!IMPORTANT]
> O ACR (Registro de Contêiner do Azure) pode ser colocado dentro de uma rede virtual. No entanto, você deve atender aos seguintes pré-requisitos:
>
> * Seu workspace do Azure Machine Learning deve ser da edição Enterprise. Para obter mais informações sobre a atualização, consulte [Atualizar para a edição Enterprise](how-to-manage-workspace.md#upgrade).
> * Sua região de espaço de trabalho do Azure Machine Learning deve ser uma [região habilitada para link privado](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
> * Seu Registro de Contêiner do Azure deve ser a versão Premium. Para obter mais informações sobre atualização, consulte [Como alterar SKUs](/azure/container-registry/container-registry-skus#changing-skus).
> * Seu Registro de Contêiner do Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os destinos de computação usados para treinamento ou inferência.
> * Seu workspace do Azure Machine Learning deve conter um [cluster de cálculo do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Quando o ACR está atrás de uma rede virtual, o Azure Machine Learning não pode usá-lo para criar imagens do Docker diretamente. Em vez disso, o cluster de cálculo é usado para compilar as imagens.

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

1. Você deve aplicar o seguinte modelo do Azure Resource Manager. Este modelo permite que seu workspace se comunique com o ACR.

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

A instância do cofre de chaves associada ao workspace é usada pelo Azure Machine Learning para armazenar as seguintes credenciais:
* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do Repositório do Contêiner do Azure
* Cadeias de conexão para armazenamento de dados

Para usar os recursos de experimentação do Azure Machine Learning com Azure Key Vault atrás de uma rede virtual, siga as seguintes etapas:

1. Vá para o cofre de chaves associado ao workspace.

   [![O cofre de chaves associado ao workspace do Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na página __Key Vault__, no painel esquerdo, selecione __Firewalls e redes virtuais__.

   ![A seção “Firewalls e redes virtuais” no painel do Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na página __Firewalls e redes virtuais__, execute as seguintes ações:
    - Em __Permitir acesso de__, clique em __Redes selecionadas__.
    - Em __Redes virtuais__, selecione __Adicionar redes virtuais existentes__ para adicionar a rede virtual onde reside a sua computação de experimentação.
    - Em __Permitir que serviços confiáveis da Microsoft ignorem esse firewall__, selecione __Sim__.

   [![A seção “Firewalls e redes virtuais” no painel do Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Para usar o Azure Databricks em uma rede virtual com seu workspace, os seguintes requisitos devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o workspace do Azure Machine Learning.
> * Se as Contas de Armazenamento do Azure para o workspace também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que o cluster do Azure Databricks.
> * Além das sub-redes __databricks-particular__ e  __databricks-público__ usadas pelos Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

Para obter informações específicas sobre o uso do Azure Databricks com uma rede virtual, consulte [Implantar o Azure Databricks na sua Rede Virtual do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Máquina virtual ou cluster HDInsight

> [!IMPORTANT]
> O Azure Machine Learning dá suporte apenas a máquinas virtuais que executam Ubuntu.

Para usar uma máquina virtual ou cluster do Azure HDInsight em uma rede virtual com seu workspace, siga as seguintes etapas:

1. Crie um cluster de VM ou HDInsight usando o portal do Azure ou a CLI do Azure e coloque-o em uma rede virtual do Azure. Para obter mais informações, consulte os seguintes artigos:
    * [Criar e gerenciar redes virtuais do Azure para VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender HDInsight usando uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Para permitir que o Azure Machine Learning comunique-se com a porta SSH na VM ou no cluster, configure uma entrada de origem para o grupo de segurança de rede. A porta SSH geralmente é a porta 22. Para permitir o tráfego dessa origem, execute as seguintes ações:

    * Na lista suspensa __Origem__, selecione __Marca de serviço__.

    * Na lista suspensa __Marca de serviço da origem__, selecione __AzureMachineLearning__.

    * Na lista suspensa __Intervalos da porta de origem__, selecione __*__ .

    * Na lista suspensa __Destino__, selecione __Qualquer um__.

    * Na lista suspensa __Intervalos da porta de destino__, selecione __22__.

    * Em __Protocolo__, selecione __Qualquer um__.

    * Em __Ação__, selecione __Permitir__.

   ![Regras de entrada para experimentação em um cluster de VM ou HDInsight dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o grupo de segurança de rede. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída da sua rede virtual, consulte a seção [Limitar a conectividade de saída da rede virtual](#limiting-outbound-from-vnet).

1. Anexe o cluster da VM ou do HDInsight ao seu workspace do Azure Machine Learning. Para obter mais informações, consulte [Configurar destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Próximas etapas

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Configurar pontos de extremidade privados](how-to-configure-private-link.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)