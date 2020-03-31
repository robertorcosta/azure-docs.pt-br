---
title: Onde salvar & gravar arquivos de experimentos
titleSuffix: Azure Machine Learning
description: Saiba onde salvar seus arquivos de entrada do experimento e onde gravar arquivos de saída para evitar erros de limitação de armazenamento e latência do experimento.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078421"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Onde salvar e gravar arquivos para experimentos de Machine Learning do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende onde salvar arquivos de entrada e onde escrever arquivos de saída de seus experimentos para evitar erros de limite de armazenamento e latência de experimentos.

Ao iniciar o treinamento executado em um [alvo de computação,](how-to-set-up-training-targets.md)eles são isolados de ambientes externos. O objetivo deste projeto é garantir a reprodutibilidade e portabilidade do experimento. Se você executar o mesmo script duas vezes, no mesmo alvo ou outro objetivo de computação, você receberá os mesmos resultados. Com este projeto, você pode tratar as metas de computação como recursos de computação apátridas, cada um não tendo afinidade com os trabalhos que estão sendo executados após o término.

## <a name="where-to-save-input-files"></a>Onde salvar arquivos de entrada

Antes de iniciar um experimento em um alvo de computação ou em sua máquina local, você deve garantir que os arquivos necessários estejam disponíveis para esse alvo de computação, como arquivos de dependência e arquivos de dados que seu código precisa executar.

O Azure Machine Learning executa scripts de treinamento copiando toda a pasta de script para o contexto de computação de destino e, em seguida, tira um instantâneo. O limite de armazenamento para instantâneos de teste é de 300 MB e/ou 2.000 arquivos.

Por essa razão, recomendamos:

* **Armazenamento de seus arquivos em um armazenamento [de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)azure Machine Learning .** Isso evita problemas de latência de experimentos e tem as vantagens de acessar dados de um alvo remoto de computação, o que significa que a autenticação e a montagem são gerenciadas pelo Azure Machine Learning. Saiba mais sobre como especificar um datastore como seu diretório de origem e fazer upload de arquivos para seu datastore nos dados de acesso do artigo [do seu datastore.](how-to-access-data.md)

* **Se você precisar apenas de alguns arquivos de dados e scripts de dependência e não puder usar um datastore,** coloque os arquivos no mesmo diretório de pastas que seu script de treinamento. Especifique `source_directory` esta pasta como sua diretamente em seu script de treinamento ou no código que chama seu script de treinamento.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos de experimentos

Para experimentos, o Azure Machine Learning faz automaticamente um instantâneo de experimento do seu código com base no diretório que você sugere quando configura a execução. Isso tem um limite total de 300 MB e/ou 2000 arquivos. Se você exceder esse limite, verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver esse erro, armazene seus arquivos de experimento em um datastore. Se você não puder usar um datastore, a tabela abaixo oferece possíveis soluções alternativas.

Descrição&nbsp;do experimento|Solução de limite de armazenamento
---|---
Menos de 2000 arquivos & não podem usar um datastore| Substituir o limite de tamanho do snapshot com <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isso pode levar vários minutos, dependendo do número e tamanho dos arquivos.
Deve usar diretório de script específico| Faça `.amlignore` um arquivo para excluir arquivos do instantâneo do experimento que não fazem parte do código-fonte. Adicione os nomes `.amlignore` de arquivo ao arquivo e coloque-o no mesmo diretório que seu script de treinamento. O `.amlignore` arquivo usa a mesma [sintaxe e padrões](https://git-scm.com/docs/gitignore) de um `.gitignore` arquivo.
Pipeline|Use um subdiretório diferente para cada etapa
Notebooks Jupyter| Crie `.amlignore` um arquivo ou mova seu notebook para um novo subdiretório vazio e execute seu código novamente.

## <a name="where-to-write-files"></a>Onde escrever arquivos

Devido ao isolamento dos experimentos de treinamento, as alterações nos arquivos que acontecem durante as corridas não são necessariamente persistidas fora do seu ambiente. Se o seu script modificar os arquivos locais para calcular, as alterações não serão persistidas para a próxima execução do experimento, e elas não serão propagadas de volta para a máquina cliente automaticamente. Portanto, as mudanças feitas durante a primeira execução do experimento não afetam e não devem afetar as do segundo.

Ao escrever alterações, recomendamos escrever arquivos em um datastore do Azure Machine Learning. Consulte [os dados de acesso de seus datastores](how-to-access-data.md).

Se você não precisar de um armazenamento `./outputs` de dados, escreva arquivos para a pasta e/ou. `./logs`

>[!Important]
> Duas pastas, *saídas* e *registros,* recebem tratamento especial pelo Azure Machine Learning. Durante o treinamento, quando`./outputs` `./logs` você escreve arquivos e pastas, os arquivos serão enviados automaticamente para o seu histórico de execução, para que você tenha acesso a eles assim que sua execução for concluída.

* **Para saída, como mensagens de status ou resultados de pontuação,** escreva arquivos para a `./outputs` pasta, para que eles sejam persistidos como artefatos no histórico de execução. Esteja atento ao número e tamanho dos arquivos escritos nesta pasta, pois a latência pode ocorrer quando o conteúdo é carregado para executar o histórico. Se a latência for uma preocupação, recomenda-se escrever arquivos em um datastore.

* **Para salvar o arquivo escrito como logs no histórico de execução,** escreva arquivos para `./logs` pasta. Os logs são carregados em tempo real, portanto este método é adequado para transmitir atualizações ao vivo a partir de uma execução remota.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como acessar dados de seus datastores](how-to-access-data.md).

* Saiba mais sobre [como configurar metas de treinamento](how-to-set-up-training-targets.md).
