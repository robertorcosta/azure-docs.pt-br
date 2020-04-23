---
title: Adicionar e gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics
description: Aprenda a adicionar e gerenciar bibliotecas usadas pelo Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870375"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Adicionar e gerenciar bibliotecas para Apache Spark no Azure Synapse Analytics

Apache Spark depende de muitas bibliotecas para fornecer funcionalidade. Essas bibliotecas podem ser aumentadas ou substituídas por bibliotecas adicionais ou versões atualizadas das mais antigas.

Os pacotes Python podem ser adicionados no nível do pool Spark (visualização) e os pacotes baseados em .jar podem ser adicionados no nível de definição do trabalho spark.

## <a name="adding-or-updating-python-libraries"></a>Adicionando ou atualizando bibliotecas Python

Apache Spark no Azure Synapse Analytics tem uma instalação completa do Anacondas, além de bibliotecas adicionais. A lista completa de bibliotecas pode ser encontrada no suporte à [versão Apache Spark](apache-spark-version-support.md).

Quando uma ocorrência de Faísca é iniciada, um novo ambiente virtual é criado usando essa instalação como base. Além disso, um arquivo *requirements.txt* (saída do `pip freeze` comando) pode ser usado para atualizar o ambiente virtual. Os pacotes listados neste arquivo para instalação ou atualização são baixados do PyPi no momento da inicialização do cluster. Este arquivo de requisitos é usado toda vez que uma instância spark é criada a partir desse pool spark.

> [!IMPORTANT]
>
> - Se o pacote que você está instalando for grande ou demorar muito tempo para ser instalado, isso afetará o tempo de inicializar a ocorrência do Spark.
> - Os pacotes que requerem suporte ao compilador no momento da instalação, como o GCC, não são suportados.
> - Os pacotes não podem ser rebaixados, apenas adicionados ou atualizados.

### <a name="requirements-format"></a>Formato de requisitos

O trecho a seguir mostra o formato do arquivo de requisitos. O nome do pacote PyPi é listado junto com uma versão exata. Este arquivo segue o formato descrito na documentação de referência [do pip freeze.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Este exemplo fixa uma versão específica. Você também pode especificar versões "não maiores que" e "menos que" neste arquivo.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interface de usuário da biblioteca Python

A ui para adicionar bibliotecas está na guia **de configurações adicionais** da página **criar o pool do Apache Spark** no portal Azure.

Faça o upload do arquivo de configuração do ambiente usando o seletor de arquivos na seção **Pacotes** da página.

![Adicionar bibliotecas Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Adicionar bibliotecas Python")

### <a name="verifying-installed-libraries"></a>Verificando bibliotecas instaladas

Para verificar se as versões corretas das bibliotecas corretas estão instaladas, execute o seguinte código

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentação do Apache Spark](https://spark.apache.org/docs/2.4.4/)
