---
title: Copiar aplicativos e dados para nós de pool
description: Saiba como copiar aplicativos e dados para nós de pool.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954886"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiar aplicativos e dados para nós de pool

O Lote do Azure dá suporte a várias maneiras de obter dados e aplicativos em nós de computação de modo que os dados e aplicativos estejam disponíveis para uso por tarefas. Os dados e aplicativos talvez precisem executar o trabalho inteiro e, portanto, precisam ser instalados em todos os nós. Alguns podem ser necessários apenas para uma tarefa específica ou precisam ser instalados para o trabalho, mas não precisam estar em todos os nós. O Lote tem ferramentas para cada um desses cenários.

- **Colocar arquivos de recurso de tarefa de inicialização em pool**: Para aplicativos ou dados que precisam ser instalados em todos os nós no pool. Use esse método junto com um pacote de aplicativos ou com a coleção de arquivos de recurso de tarefa de inicialização para realizar um comando de instalação.  

Exemplos: 
- Use a linha de comando da tarefa de inicialização para mover ou instalar aplicativos

- Especifique uma lista de arquivos ou contêineres específicos em uma conta do Armazenamento do Azure. Para obter mais informações, confira a [add#resourcefile na documentação do REST](/rest/api/batchservice/pool/add#resourcefile)

- Cada trabalho executado no pool executa MyApplication.exe, que precisa ser instalado primeiro com MyApplication.msi. Se você usar esse mecanismo, precisará definir a propriedade **wait for success** da tarefa como **true**. Para obter mais informações, confira a [add#starttask na documentação do REST](/rest/api/batchservice/pool/add#starttask).

- **Referências do pacote de aplicativos** no pool: Para aplicativos ou dados que precisam ser instalados em todos os nós no pool. Não há nenhum comando de instalação associado a um pacote de aplicativos, mas você pode usar uma tarefa de inicialização para executar qualquer comando de instalação. Se o seu aplicativo não requer instalação ou consiste em um grande número de arquivos, você pode usar esse método. Os pacotes de aplicativos são adequados para grandes quantidades de arquivos porque combinam um grande número de referências de arquivo em um conteúdo pequeno. Se você tentar incluir mais de 100 arquivos de recurso separados em uma tarefa, o serviço de Lote poderá enfrentar limitações internas do sistema para uma tarefa individual. Além disso, use pacotes de aplicativos se você tem requisitos de controle de versão rigorosos em que você pode ter muitas versões diferentes do mesmo aplicativo e precisa escolher entre eles. Para obter mais informações, leia [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](./batch-application-packages.md).

- **Arquivos de recurso de tarefa de preparação de trabalho**: Para aplicativos ou dados que precisam ser instalados para que o trabalho seja executado, mas não precisam ser instalados em todo o pool. Por exemplo: se o pool tiver muitos tipos diferentes de trabalhos e apenas um desses tipos precisar de MyApplication.msi para ser executado, faz sentido colocar a etapa de instalação em uma tarefa de preparação de trabalho. Para saber mais sobre tarefas de preparação de trabalho, confira [Executar tarefas de preparação e de liberação de trabalhos em nós de computação do Lote](./batch-job-prep-release.md).

- **Arquivos de recurso de tarefa**: para quando dados ou um aplicativo são relevantes apenas para uma tarefa individual. Por exemplo:  Você tem cinco tarefas, cada uma processa um arquivo diferente e, em seguida, grava a saída no armazenamento de blobs.  Nesse caso, o arquivo de entrada deve ser especificado na coleção de **arquivos de recurso de tarefas**, pois cada tarefa tem seu próprio arquivo de entrada.

## <a name="determine-the-scope-required-of-a-file"></a>Determinar o escopo necessário de um arquivo

Você precisa determinar o escopo de um arquivo: se é o arquivo necessário para um pool, um trabalho ou uma tarefa. Os arquivos que têm o escopo para o pool devem usar pacotes de aplicativos de pool ou uma tarefa de inicialização. Os arquivos com escopo para o trabalho devem usar uma tarefa de preparação de trabalho. Um bom exemplo de arquivos com escopo no nível do pool ou do trabalho são os aplicativos. Os arquivos com escopo para a tarefa devem usar arquivos de recurso de tarefa.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Outras maneiras de obter dados em nós de computação do Lote

Há outras maneiras de obter dados em nós de computação do Lote que não são oficialmente integrados à API REST do Lote. Já que você tem controle sobre os nós do Lote do Azure e pode executar arquivos executáveis personalizados, poderá também efetuar pull de dados de qualquer número de fontes personalizadas, desde que o nó do Lote tenha conectividade com o destino e você tenha as credenciais para essa fonte no nó do Lote do Azure. Alguns exemplos comuns são:

- Baixar dados do SQL
- Baixar dados de outros serviços Web/locais personalizados
- Mapear um compartilhamento de rede

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento de blobs tem destinos de escalabilidade de download. Os destinos de escalabilidade do compartilhamento de arquivo do Armazenamento do Azure são os mesmos que os de um único blob. O tamanho afetará o número de nós e pools necessários.

