---
title: Segredos de autenticação no treinamento
titleSuffix: Azure Machine Learning
description: Passe segredos para que o treinamento seja executado de modo seguro usando o espaço de trabalho Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5c7568b0914b5e60d1a47971424f1f04f41745da
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646956"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Usar segredos de credencial de autenticação em Azure Machine Learning execuções de treinamento
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a usar os segredos no treinamento em execução com segurança. As informações de autenticação, como seu nome de usuário e senha, são segredos. Por exemplo, se você se conectar a um banco de dados externo a fim de consultar dado de treinamento, você precisará passar seu nome de usuário e senha para o contexto de execução remota. Codificar esses valores em scripts de treinamento em texto não criptografado é inseguro, pois ele exporia o segredo. 

Em vez disso, seu espaço de trabalho Azure Machine Learning tem um recurso associado chamado de [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Use esta Key Vault para passar segredos para execuções remotas com segurança por meio de um conjunto de APIs no SDK do Azure Machine Learning Python.

O fluxo básico para usar segredos é:
 1. No computador local, faça logon no Azure e conecte-se ao seu espaço de trabalho.
 2. No computador local, defina um segredo no Key Vault do espaço de trabalho.
 3. Enviar uma execução remota.
 4. Na execução remota, obtenha o segredo de Key Vault e use-o.

## <a name="set-secrets"></a>Definir segredos

No Azure Machine Learning, a classe [keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) contém métodos para definir segredos. Em sua sessão do Python local, primeiro obtenha uma referência ao seu espaço de trabalho Key Vault e, em seguida, use o [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secret-name--value-) método para definir um segredo por nome e valor. O método __set_secret__ atualizará o valor secreto se o nome já existir.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Não coloque o valor secreto em seu código Python, pois ele não é seguro para armazená-lo no arquivo como texto não criptografado. Em vez disso, obtenha o valor secreto de uma variável de ambiente, por exemplo, o segredo de compilação do Azure DevOps ou da entrada interativa do usuário.

Você pode listar nomes de segredo usando o [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=truelist-secrets--) método e também há uma versão do lote,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-) que permite que você defina vários segredos de cada vez.

## <a name="get-secrets"></a>Obter segredos

No código local, você pode usar o [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueget-secret-name-) método para obter o valor secreto por nome.

Para execuções enviadas [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)  , use o [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secret-name-) método com a [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) classe. Como uma execução enviada está ciente de seu espaço de trabalho, esse método faz o atalho da instanciação do espaço de trabalho e retorna o valor secreto diretamente.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Tenha cuidado para não expor o valor secreto escrevendo ou imprimindo-o.

Também há uma versão do lote, [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secrets-secrets-) para acessar vários segredos de uma só vez.

## <a name="next-steps"></a>Próximas etapas

 * [Exibir bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Saiba mais sobre o Enterprise Security com Azure Machine Learning](concept-enterprise-security.md)
