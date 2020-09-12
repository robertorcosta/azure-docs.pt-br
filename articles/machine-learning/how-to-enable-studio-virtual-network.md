---
title: Habilitar o Azure Machine Learning Studio em uma rede virtual
titleSuffix: Azure Machine Learning
description: Use o Azure Machine Learning Studio para acessar os dados armazenados dentro de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: c897cb513745669523daea0a850e20715de4676b
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663719"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Usar o Azure Machine Learning Studio em uma rede virtual do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a usar o Azure Machine Learning Studio em uma rede virtual. Você aprenderá como:

> [!div class="checklist"]
> - Acesse o estúdio de um recurso dentro de uma rede virtual.
> - Conceda ao estúdio acesso aos dados armazenados dentro de uma rede virtual.
> - Entenda como a segurança de armazenamento é afetada pelo estúdio.

Este artigo é a parte cinco de uma série de cinco partes que orienta você pela proteção de um fluxo de trabalho Azure Machine Learning. É altamente recomendável que você leia a [parte um: visão geral da VNet](how-to-network-security-overview.md) para entender a arquitetura geral primeiro. 

Consulte os outros artigos desta série:

[1. visão geral da VNet](how-to-network-security-overview.md)  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treinamento](how-to-secure-training-vnet.md)  >  [4. Proteja o ambiente do inferência](how-to-secure-inferencing-vnet.md)  >  [5. Habilitar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> Embora a maior parte do estúdio funcione com dados armazenados em uma rede virtual, os notebooks integrados __não__. Os blocos de anotações integrados não dão suporte ao uso do armazenamento que está em uma rede virtual. Em vez disso, você pode usar blocos de anotações do Jupyter de uma instância de computação. Para obter mais informações, consulte a seção [acessar dados em um notebook da instância de computação]() .


## <a name="prerequisites"></a>Pré-requisitos

+ Leia a [visão geral de segurança de rede](how-to-network-security-overview.md) para entender cenários comuns de rede virtual e a arquitetura de rede virtual geral.

+ Uma rede virtual e sub-rede pré-existentes a serem usadas.

+ Um [espaço de trabalho Azure Machine Learning existente com o link privado habilitado](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Uma [conta de armazenamento do Azure existente adicionou sua rede virtual](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Acessar o estúdio de um recurso dentro da VNet

Se você estiver acessando o Studio de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou máquina virtual), deverá permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se você estiver usando NSG (grupos de segurança de rede) para restringir o tráfego de saída, adicione uma regra a um destino de __marca de serviço__ de __AzureFrontDoor. frontend__.

## <a name="access-data-using-the-studio"></a>Acessar dados usando o estúdio

Se os dados estiverem armazenados em uma rede virtual, você deverá configurar suas contas de armazenamento para usar a [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para conceder ao estúdio acesso aos seus dados.


Se você não habilitar a identidade gerenciada, receberá esse erro, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` Além disso, as seguintes operações serão desabilitadas:

* Visualizar dados no estúdio.
* Visualize dados no designer.
* Envie um experimento do AutoML.
* Inicie um projeto de rotulagem.

O estúdio dá suporte à leitura de dados dos seguintes tipos de repositório de armazenamento em uma rede virtual:

* Blob do Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Banco de Dados SQL do Azure


### <a name="configure-datastores-to-use-managed-identity"></a>Configurar os repositórios de armazenamento para usar identidade gerenciada

Azure Machine Learning usa [repositórios](concept-data.md#datastores) de armazenamento para se conectar a contas de armazenamento. Use as etapas a seguir para configurar seus repositórios de armazenamento para usar identidade gerenciada. 

1. No estúdio, selecione __repositórios de armazenamento__.

1. Para criar um novo repositório de armazenamento, selecione __+ novo repositório de armazenamento__.

    Para atualizar um repositório de armazenamento existente, selecione o repositório de armazenamento e selecione __Atualizar credenciais__.

1. Nas configurações do repositório de armazenamento, selecione __Sim__ para  __permitir que o serviço de Azure Machine Learning acesse o armazenamento usando a identidade gerenciada pelo espaço de trabalho__.


Essas etapas adicionam a identidade gerenciada pelo espaço de trabalho como um __leitor__ ao serviço de armazenamento usando o controle de acesso baseado em recursos (RBAC) do Azure. O acesso de __leitor__ permite que o espaço de trabalho recupere as configurações de firewall e verifique se os dados não deixam a rede virtual.

> [!NOTE]
> Essas alterações podem levar até 10 minutos para entrar em vigor.

## <a name="technical-notes-for-managed-identity"></a>Notas técnicas para identidade gerenciada

O uso da identidade gerenciada para acessar serviços de armazenamento afeta algumas considerações de segurança. Essas considerações são exclusivas para o tipo de conta de armazenamento que você está acessando. Esta seção descreve as alterações para cada tipo de conta de armazenamento.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Para o __armazenamento de BLOBs do Azure__, a identidade gerenciada por espaço de trabalho também é adicionada como um [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados do armazenamento de BLOBs.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controle de acesso Azure Data Lake Storage Gen2

Você pode usar as ACLs (listas de controle de acesso) de estilo RBAC e POSIX para controlar o acesso a dados dentro de uma rede virtual.

Para usar o RBAC, adicione a identidade gerenciada pelo espaço de trabalho à função de [leitor de dados de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Para obter mais informações, confira [Controle de acesso baseado em função](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

Para usar ACLs, a identidade gerenciada por espaço de trabalho pode ser atribuída ao acesso, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Controle de acesso Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 oferece suporte apenas a listas de controle de acesso em estilo POSIX. Você pode atribuir o acesso de identidade gerenciada por espaço de trabalho a recursos, assim como qualquer outro princípio de segurança. Para obter mais informações, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>O banco de dados SQL do Azure continha o usuário

Para acessar os dados armazenados em um Azure SQL Database usando a identidade gerenciada, você deve criar um usuário contido no SQL que mapeia para a identidade gerenciada. Para obter mais informações sobre como criar um usuário de um provedor externo, consulte [criar usuários independentes mapeados para identidades do Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Depois de criar um usuário do SQL continha, conceda permissões a ele usando o [comando Grant T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

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

## <a name="next-steps"></a>Próximas etapas

Este artigo é uma parte opcional de uma série de rede virtual de quatro partes. Consulte o restante dos artigos para saber como proteger uma rede virtual:

* [Parte 1: visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: proteger os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: proteger o ambiente de treinamento](how-to-secure-training-vnet.md)
* [Parte 4: proteger o ambiente inferência](how-to-secure-inferencing-vnet.md)