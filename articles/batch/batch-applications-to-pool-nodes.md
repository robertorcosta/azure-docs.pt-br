---
title: Copiando aplicativos e dados para pool de nomes
description: Aprenda a copiar aplicativos e dados para pool de nomes.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385578"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Copiando aplicativos e dados para pool de nomes

O Azure Batch suporta várias maneiras de obter dados e aplicativos em nós computacionais para que os dados e aplicativos estejam disponíveis para uso por tarefas. Dados e aplicativos podem ser necessários para executar todo o trabalho e, portanto, precisam ser instalados em cada nó. Alguns podem ser necessários apenas para uma tarefa específica, ou precisam ser instalados para o trabalho, mas não precisam estar em todos os nós. Batch tem ferramentas para cada um desses cenários.

- **Pool iniciar arquivos de recursos de tarefa :** Para aplicativos ou dados que precisam ser instalados em cada nó no pool. Use este método juntamente com um pacote de aplicativos ou a coleta de arquivos de recursos da tarefa inicial para executar um comando de instalação.  

Exemplos: 
- Use a linha de comando de tarefa inicial para mover ou instalar aplicativos

- Especifique uma lista de arquivos ou contêineres específicos em uma conta de armazenamento do Azure. Para obter mais informações, consulte [add#resourcefile na documentação REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Todos os trabalhos executados no pool executam myApplication.exe que deve ser primeiro instalado com MyApplication.msi. Se você usar este mecanismo, você precisa definir a espera da tarefa inicial para a propriedade de **sucesso** para **a verdade**. Para obter mais informações, consulte a [tarefa add#starttask na documentação REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Referências do pacote** de aplicativos no pool: Para aplicativos ou dados que precisam ser instalados em cada nó do pool. Não há comando de instalação associado a um pacote de aplicativos, mas você pode usar uma tarefa inicial para executar qualquer comando de instalação. Se o aplicativo não exigir instalação ou consistir em um grande número de arquivos, você pode usar este método. Os pacotes de aplicativos são adequados para um grande número de arquivos porque combinam um grande número de referências de arquivos em uma pequena carga útil. Se você tentar incluir mais de 100 arquivos de recursos separados em uma tarefa, o serviço Batch pode encontrar limitações internas do sistema para uma única tarefa. Além disso, use pacotes de aplicativos se você tiver requisitos rigorosos de versão onde você pode ter muitas versões diferentes do mesmo aplicativo e precisa escolher entre eles. Para obter mais informações, leia [Implantar aplicativos para calcular nós com pacotes de aplicativos em lote](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Arquivos de recursos de tarefa de preparação**de trabalho : Para aplicativos ou dados que devem ser instalados para o trabalho ser executado, mas não precisam ser instalados em todo o pool. Por exemplo: se o seu pool tem muitos tipos diferentes de trabalho, e apenas um tipo de trabalho precisa do MyApplication.msi para ser executado, faz sentido colocar a etapa de instalação em uma tarefa de preparação de trabalho. Para obter mais informações sobre tarefas de preparação de trabalho, consulte [Executar tarefas de preparação de trabalho e liberação de trabalho em nós de computação em lote](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Arquivos de recursos de tarefa :** Para quando um aplicativo ou dados são relevantes apenas para uma tarefa individual. Por exemplo: Você tem cinco tarefas, cada uma processa um arquivo diferente e, em seguida, grava a saída para o armazenamento blob.  Neste caso, o arquivo de entrada deve ser especificado na coleta de **arquivos de recursos de tarefas** porque cada tarefa tem seu próprio arquivo de entrada.

## <a name="determine-the-scope-required-of-a-file"></a>Determine o escopo necessário de um arquivo

Você precisa determinar o escopo de um arquivo - é o arquivo necessário para um pool, um trabalho ou uma tarefa. Os arquivos que estão escopo no pool devem usar pacotes de aplicativos de pool ou uma tarefa inicial. Os arquivos escopo para o trabalho devem usar uma tarefa de preparação de trabalho. Um bom exemplo de arquivos escopo no pool ou nível de trabalho são aplicativos. Os arquivos escopo da tarefa devem usar arquivos de recursos de tarefa.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Outras formas de obter dados em nós de computação em lote

Existem outras maneiras de obter dados em nós de computação em lote que não estão oficialmente integrados na API Batch REST. Como você tem controle sobre nós do Azure Batch e pode executar executáveis personalizados, você é capaz de extrair dados de qualquer número de fontes personalizadas, desde que o nó Batch tenha conectividade com o alvo e você tenha as credenciais dessa fonte no nó Azure Batch. Alguns exemplos comuns são:

- Baixando dados do SQL
- Baixando dados de outros serviços web/locais personalizados
- Mapeando um compartilhamento de rede

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento Blob tem metas de escalabilidade de download. Os alvos de escalabilidade de compartilhamento de arquivos do Azure são os mesmos de uma única bolha. O tamanho afetará o número de nódulos e piscinas que você precisa.

