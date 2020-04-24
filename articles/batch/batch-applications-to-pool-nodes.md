---
title: Copiar aplicativos e dados para nós de pool
description: Saiba como copiar aplicativos e dados para nós de pool.
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 700e9b80f8420266c0300b47bdd30bc271f8421c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115577"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Copiar aplicativos e dados para nós de pool

O lote do Azure dá suporte a várias maneiras de obter dados e aplicativos em nós de computação para que os dados e aplicativos estejam disponíveis para uso por tarefas. Os dados e aplicativos podem ser necessários para executar o trabalho inteiro e, portanto, precisam ser instalados em todos os nós. Alguns podem ser necessários apenas para uma tarefa específica ou precisam ser instalados para o trabalho, mas não precisam estar em todos os nós. O lote tem ferramentas para cada um desses cenários.

- **Arquivos de recurso de tarefa de início de pool**: para aplicativos ou dados que precisam ser instalados em cada nó no pool. Use esse método junto com um pacote de aplicativos ou a coleção de arquivos de recursos da tarefa inicial para executar um comando de instalação.  

Exemplos: 
- Use a linha de comando Iniciar tarefa para mover ou instalar aplicativos

- Especifique uma lista de arquivos ou contêineres específicos em uma conta de armazenamento do Azure. Para obter mais informações, consulte [Adicionar # ResourceFile na documentação REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Cada trabalho executado no pool executa o MyApplication. exe que deve ser instalado primeiro com MyApplication. msi. Se você usar esse mecanismo, precisará definir a propriedade Wait da tarefa **de** início para êxito como **verdadeira**. Para obter mais informações, consulte a [documentação adicionar # starttask in REST](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Referências do pacote de aplicativos** no pool: para aplicativos ou dados que precisam ser instalados em cada nó no pool. Não há nenhum comando de instalação associado a um pacote de aplicativos, mas você pode usar uma tarefa de inicialização para executar qualquer comando de instalação. Se o seu aplicativo não requer instalação ou consiste em um grande número de arquivos, você pode usar esse método. Os pacotes de aplicativos são adequados para grandes quantidades de arquivos porque combinam um grande número de referências de arquivo em uma carga pequena. Se você tentar incluir mais de 100 arquivos de recurso separados em uma tarefa, o serviço de lote poderá vir contra limitações internas do sistema para uma única tarefa. Além disso, use pacotes de aplicativos se você tiver requisitos de controle de versão rigorosos em que você pode ter muitas versões diferentes do mesmo aplicativo e precisar escolher entre eles. Para obter mais informações, leia [implantar aplicativos para nós de computação com pacotes de aplicativos do lote](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Arquivos de recurso de tarefa de preparação de trabalho**: para aplicativos ou dados que devem ser instalados para que o trabalho seja executado, mas não precisa ser instalado em todo o pool. Por exemplo: se o pool tiver muitos tipos diferentes de trabalhos, e apenas um tipo de trabalho precisar de MyApplication. msi para ser executado, faz sentido colocar a etapa de instalação em uma tarefa de preparação de trabalho. Para obter mais informações sobre as tarefas de preparação de trabalho [, consulte executar tarefas de preparação de trabalho e liberação de trabalho em nós de computação do lote](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Arquivos de recurso de tarefa**: para quando um aplicativo ou dados é relevante apenas para uma tarefa individual. Por exemplo: você tem cinco tarefas, cada uma processa um arquivo diferente e, em seguida, grava a saída no armazenamento de BLOBs.  Nesse caso, o arquivo de entrada deve ser especificado na coleção de **arquivos de recursos de tarefas** , pois cada tarefa tem seu próprio arquivo de entrada.

## <a name="determine-the-scope-required-of-a-file"></a>Determinar o escopo necessário de um arquivo

Você precisa determinar o escopo de um arquivo-é o arquivo necessário para um pool, um trabalho ou uma tarefa. Os arquivos que têm o escopo para o pool devem usar pacotes de aplicativos de pool ou uma tarefa inicial. Os arquivos com escopo para o trabalho devem usar uma tarefa de preparação de trabalho. Um bom exemplo de arquivos com escopo no nível do pool ou do trabalho são os aplicativos. Os arquivos com escopo para a tarefa devem usar arquivos de recurso de tarefa.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Outras maneiras de obter dados em nós de computação do lote

Há outras maneiras de obter dados em nós de computação do lote que não são oficialmente integrados à API REST do lote. Como você tem controle sobre os nós do lote do Azure e pode executar executáveis personalizados, é possível efetuar pull de dados de qualquer número de fontes personalizadas, desde que o nó do lote tenha conectividade com o destino e você tenha as credenciais para essa fonte no nó do lote do Azure. Alguns exemplos comuns são:

- Baixando dados do SQL
- Baixando dados de outros serviços Web/locais personalizados
- Mapeando um compartilhamento de rede

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento de BLOBs tem destinos de escalabilidade de download. Os destinos de escalabilidade do compartilhamento de arquivos do armazenamento do Azure são os mesmos para um único BLOB. O tamanho afetará o número de nós e pools necessários.

