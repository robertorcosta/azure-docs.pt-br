---
title: Use segredos em corridas de treinamento
titleSuffix: Azure Machine Learning
description: Passar segredos para o treinamento é executado de forma segura usando o Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942250"
---
# <a name="use-secrets-in-training-runs"></a>Use segredos em corridas de treinamento
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a usar segredos em corridas de treinamento com segurança. Informações de autenticação, como seu nome de usuário e senha, são segredos. Por exemplo, se você se conectar a um banco de dados externo para consultar dados de treinamento, você precisará passar seu nome de usuário e senha para o contexto de execução remota. Codificar tais valores em scripts de treinamento em texto claro é inseguro, pois exporia o segredo. 

Em vez disso, seu espaço de trabalho azure Machine Learning tem um recurso associado chamado [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Use este Key Vault para passar segredos para corridas remotas com segurança através de um conjunto de APIs no Azure Machine Learning Python SDK.

O fluxo básico para usar segredos é:
 1. No computador local, faça login no Azure e conecte-se ao seu espaço de trabalho.
 2. No computador local, defina um segredo no Cofre de Chaves do Espaço de Trabalho.
 3. Envie uma corrida remota.
 4. Dentro do controle remoto, pegue o segredo do Key Vault e use-o.

## <a name="set-secrets"></a>Definir segredos

No Azure Machine Learning, a classe [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) contém métodos para definir segredos. Em sua sessão Python local, primeiro obtenha uma referência ao [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) seu key vault do espaço de trabalho e, em seguida, use o método para definir um segredo por nome e valor. O método __set_secret__ atualiza o valor secreto se o nome já existir.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto em seu código Python, pois é inseguro armazená-lo em arquivo como texto claro. Em vez disso, obtenha o valor secreto de uma variável de ambiente, por exemplo, o Azure DevOps constrói segredo, ou a partir de entrada interativa do usuário.

Você pode listar nomes [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) secretos usando o método e há também uma versão em lote,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) que permite definir vários segredos ao mesmo tempo.

## <a name="get-secrets"></a>Obter segredos

Em seu código local,[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) você pode usar o método para obter o valor secreto pelo nome.

Para as corridas [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) submetidas, use o [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) método com a [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) classe. Como uma execução submetida está ciente de seu espaço de trabalho, este método corta a instanciação do Espaço de Trabalho e retorna o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo-o.

Há também uma versão em lote, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) para acessar vários segredos de uma só vez.

## <a name="next-steps"></a>Próximas etapas

 * [Ver exemplo de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Saiba mais sobre segurança corporativa com o Azure Machine Learning](concept-enterprise-security.md)
