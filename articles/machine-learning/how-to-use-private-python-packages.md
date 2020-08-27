---
title: Usar pacotes privados do Python
titleSuffix: Azure Machine Learning
description: Acesse pacotes python privados com segurança em ambientes de Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 314f6a45bf688125e79f0b8ce0099a8326b339dc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958143"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Usar pacotes python privados com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, saiba como usar pacotes python privados com segurança no Azure Machine Learning. Os casos de uso para pacotes do Python privados incluem:

 * Você desenvolveu um pacote particular que não deseja compartilhar publicamente.
 * Você deseja usar um repositório organizado de pacotes armazenados em um firewall corporativo.

A abordagem recomendada depende se você tem poucos pacotes para um único espaço de trabalho de Azure Machine Learning ou um repositório inteiro de pacotes para todos os espaços de trabalho em uma organização.

Os pacotes privados são usados por meio da classe de [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) . Em um ambiente, você declara quais pacotes do Python usar, incluindo os privados. Para saber mais sobre o ambiente em Azure Machine Learning em geral, consulte [como usar ambientes](how-to-use-environments.md). 

## <a name="prerequisites"></a>Pré-requisitos

 * O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Use um pequeno número de pacotes para desenvolvimento e teste

Para um pequeno número de pacotes privados para um único espaço de trabalho, use o [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) método estático. Essa abordagem permite que você adicione rapidamente um pacote privado ao espaço de trabalho e seja adequado para fins de desenvolvimento e teste.

Aponte o argumento caminho do arquivo para um arquivo de roda local e execute o ```add_private_pip_wheel``` comando. O comando retorna uma URL usada para controlar o local do pacote em seu espaço de trabalho. Capture a URL de armazenamento e passe-a para o `add_pip_package()` método.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Internamente, o serviço de Azure Machine Learning substitui a URL pela URL SAS segura, para que o arquivo de roda seja mantido privado e seguro.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Usar um repositório de pacotes do feed DevOps do Azure

Se você estiver desenvolvendo ativamente pacotes do Python para seu aplicativo de aprendizado de máquina, poderá hospedá-los em um repositório DevOps do Azure como artefatos e publicá-los como um feed. Essa abordagem permite que você integre o fluxo de trabalho do DevOps para criar pacotes com seu Workspace do Azure Machine Learning. Para saber como configurar os feeds do Python usando o Azure DevOps, leia [introdução aos pacotes do Python no Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

Essa abordagem usa o token de acesso pessoal para se autenticar no repositório. A mesma abordagem é aplicável a outros repositórios com autenticação baseada em token, como repositórios GitHub privados. 

 1. [Crie um Pat (token de acesso pessoal)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) para sua instância do DevOps do Azure. Defina o escopo do token para o __empacotamento de > leitura__. 

 2. Adicione a URL DevOps do Azure e PAT como propriedades de espaço de trabalho, usando o método [Workspace. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) .

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Crie um ambiente de Azure Machine Learning e adicione pacotes python do feed.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

O ambiente agora está pronto para ser usado em execuções de treinamento ou implantações de ponto de extremidade de serviço Web. Ao criar o ambiente, Azure Machine Learning serviço usa o PAT para se autenticar no feed com a URL base correspondente.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Usar um repositório de pacotes do armazenamento privado

Você pode consumir pacotes de uma conta de armazenamento do Azure dentro do firewall da sua organização. A conta de armazenamento pode conter um conjunto organizado de pacotes ou um espelho interno de pacotes disponíveis publicamente.

Para configurar esse armazenamento privado:

1. [Coloque o espaço de trabalho dentro de uma rede virtual (VNet)](how-to-enable-virtual-network.md).
1. Criar uma conta de armazenamento e não [permitir acesso público](https://docs.microsoft.com/azure/storage/common/storage-network-security).
1. Coloque os pacotes do Python que você deseja usar em um contêiner dentro da conta de armazenamento 
1. [Permitir o acesso à conta de armazenamento da VNet do espaço de trabalho](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
1. [Coloque o ACR (registro de contêiner do Azure) para o espaço de trabalho por trás da VNet](how-to-enable-virtual-network.md#azure-container-registry).

    > [!IMPORTANT]
    > Você deve concluir esta etapa para poder treinar ou implantar modelos usando o repositório de pacotes particular.

Depois de concluir essas configurações, você pode fazer referência aos pacotes na definição de ambiente Azure Machine Learning por sua URL completa no armazenamento de BLOBs do Azure.

## <a name="next-steps"></a>Próximas etapas

 * Saiba mais sobre [segurança corporativa no Azure Machine Learning](concept-enterprise-security.md)
