---
title: Adicionar e gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics
description: Saiba como adicionar e gerenciar bibliotecas usadas por Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cf78a2f7d909fb260c5ff99f80c9d1482f2cd08b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027300"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Adicionar e gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics

Apache Spark depende de muitas bibliotecas para fornecer funcionalidade. Essas bibliotecas podem ser aumentadas ou substituídas por bibliotecas adicionais ou versões atualizadas de mais antigas.

Os pacotes do Python podem ser adicionados no nível do pool do Spark (visualização) e os pacotes baseados em jar podem ser adicionados no nível de definição de trabalho do Spark.

## <a name="adding-or-updating-python-libraries"></a>Adicionando ou atualizando bibliotecas do Python

Apache Spark no Azure Synapse Analytics tem uma instalação completa do Anacondas, além de bibliotecas adicionais. A lista de bibliotecas completa pode ser encontrada em [suporte à versão Apache Spark](apache-spark-version-support.md).

Quando uma instância do Spark é iniciada, um novo ambiente virtual é criado usando essa instalação como base. Além disso, um arquivo de *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente virtual. Os pacotes listados neste arquivo para instalação ou atualização são baixados de PyPi no momento da inicialização do cluster. Esse arquivo de requisitos é usado toda vez que uma instância do Spark é criada a partir desse pool do Spark.

> [!IMPORTANT]
>
> - Se o pacote que você está instalando for grande ou demorar muito para ser instalado, isso afetará o tempo de inicialização da instância do Spark.
> - Os pacotes que exigem suporte ao compilador no momento da instalação, como GCC, não têm suporte.
> - Os pacotes não podem ser desatualizados, somente adicionados ou atualizados.

### <a name="requirements-format"></a>Formato dos requisitos

O trecho a seguir mostra o formato do arquivo de requisitos. O nome do pacote PyPi é listado junto com uma versão exata. Esse arquivo segue o formato descrito na documentação de referência do [Pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Este exemplo fixa uma versão específica. Você também pode especificar as versões "não maiores que" e "menores que" neste arquivo.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interface do usuário da biblioteca do Python

A interface do usuário para adicionar bibliotecas está na guia **configurações adicionais** da página **criar Apache Spark pool** no portal do Azure.

Carregue o arquivo de configuração do ambiente usando o seletor de arquivo na seção **pacotes** da página.

![Adicionar bibliotecas do Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Adicionar bibliotecas do Python")

### <a name="verifying-installed-libraries"></a>Verificando as bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas estão instaladas, execute o código a seguir

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação do Apache Spark](https://spark.apache.org/docs/2.4.4/)
