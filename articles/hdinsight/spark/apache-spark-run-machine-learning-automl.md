---
title: Execute cargas de trabalho de aprendizado de máquina do Azure no Apache Spark no HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638866"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Execute cargas de trabalho de aprendizado de máquina do Azure com aprendizado de máquina automatizado no Apache Spark no HDInsight

O Azure Machine Learning simplifica e acelera a construção, o treinamento e a implantação de modelos de aprendizado de máquina. No AutoML (Machine Learning) automatizado, você começa com dados de treinamento que tem um recurso de destino definido e, em seguida, itera através de combinações de algoritmos e seleções de recursos para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O HDInsight permite que os clientes disponibilizem clusters com centenas de nós. O AutoML em execução no Spark em um cluster HDInsight permite que os usuários usem a capacidade de computação entre esses álos para executar trabalhos de treinamento de forma escalonada e para executar vários trabalhos de treinamento em paralelo. Isso permite que os usuários executem experimentos AutoML enquanto compartilham a computação com suas outras cargas de trabalho de big data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning em um cluster do HDInsight

Para tutoriais gerais de aprendizado de máquina automatizado, consulte [Tutorial: Use aprendizado de máquina automatizado para construir seu modelo de regressão.](../../machine-learning/tutorial-auto-train-models.md)
Todos os novos clusters HDInsight-Spark vêm pré-instalados com O AzureML-AutoML SDK.

> [!Note]
> Pacotes do Azure Machine Learning são instalados no ambiente Python3 conda. O Jupyter Notebook instalado deve ser executado usando o kernel PySpark3.

Você também pode usar notebooks Zeppelin para usar o AutoML.

> [!Note]
> Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) onde PySpark3 não escolhe a versão certa do Python. Por favor, use o trabalho documentado.

## <a name="authentication-for-workspace"></a>Autenticação para o workspace

Criação do workspace e envio de teste exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [Usuário do Azure AD](/azure/python/python-sdk-azure-authenticate) também poderá ser usado para gerar o token de autenticação obrigatório se a autenticação multifator não estiver habilitada na conta.  

O snippet de código a seguir cria um token de autenticação usando um **aplicativo do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

O snippet de código a seguir cria um token de autenticação usando um **usuário do Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Carregando conjunto de dados

O aprendizado de máquina automatizado no Spark usa **Fluxos de dados**, que são operações imutáveis avaliadas lentamente nos dados.  Um Fluxo de Dados pode carregar um conjunto de dados de um blob com acesso de leitura público ou uma URL de blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Você também pode registrar o repositório de dados com o workspace usando um único registro.

## <a name="experiment-submission"></a>Envio de experimento

Na [configuração automatizada de aprendizado de máquina,](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)a propriedade `spark_context` deve ser definida para que o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos de executor para o aplicativo Spark.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a motivação por trás do aprendizado automatizado de máquina, consulte [modelos de lançamento em ritmo usando o aprendizado automatizado de máquina da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais informações sobre como usar os recursos ml automatizados do Azure ML, consulte [novos recursos automatizados de aprendizado de máquina no Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
