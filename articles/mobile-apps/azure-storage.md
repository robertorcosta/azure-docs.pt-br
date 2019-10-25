---
title: Armazenamento em nuvem para criar aplicativos altamente seguros, duráveis e escalonáveis com o armazenamento do Azure
description: Saiba mais sobre os serviços para armazenar grandes dados estruturados e não estruturados de aplicativos móveis na nuvem.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 01407f843ef36095fc87feb3722e85dc477ad8bb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795647"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicativos altamente seguros, duráveis e escalonáveis com o armazenamento do Azure
O [armazenamento do Azure](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicativos modernos que oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, um repositório de mensagens para mensagens confiáveis e um repositório NoSQL. O Armazenamento do Azure é:
- A redundância **durável e altamente disponível** garante que seus dados estejam seguros no caso de falhas transitórias de hardware. Você também pode optar por replicar dados em datacenters ou regiões geográficas para obter mais proteção contra catástrofes ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.
- **Seguro** -todos os dados gravados no armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.
- Os serviços **escalonáveis** são projetados para serem altamente escalonáveis para atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.
- **Gerenciado** – o Azure lida com a manutenção de hardware, atualizações e problemas críticos para você.
- Os dados **podem ser acessados** de qualquer lugar do mundo por http ou HTTPS. A Microsoft fornece bibliotecas de cliente em uma variedade de linguagens, incluindo .NET, Java, Node. js, Python, PHP, Ruby, Go e outros, bem como uma API REST madura. Há suporte para scripts no Azure PowerShell ou CLI do Azure e no portal do Azure e Gerenciador de Armazenamento do Azure oferecem soluções visuais fáceis para trabalhar com seus dados.

Use os seguintes serviços para habilitar o armazenamento em nuvem em seus aplicativos móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/) oferece a solução de armazenamento de objetos para a nuvem e é otimizado para armazenar grandes quantidades de dados não estruturados que não aderem a um modelo ou definição de dados específico, como texto ou binário. Ele dá suporte à variedade de bibliotecas de cliente de linguagens e foi projetado para:
- Fornecimento de imagens ou de documentos diretamente a um navegador.
- Armazenamento de arquivos para acesso distribuído.
- Transmissão por streaming de áudio e vídeo.
- Gravando nos arquivo de log.
- Armazenamento de dados de backup e restauração, recuperação de desastres e arquivamento.
- Armazenamento de dados para análise por um serviço local ou hospedado no Azure.

**Referências**
- [Azure portal](https://portal.azure.com)
- [Documentação](/azure/storage/blobs/storage-blobs-introduction)
- [Inícios Rápidos](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Exemplos](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure
O [armazenamento de tabelas do Azure](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados NoSQL estruturados na nuvem, fornecendo um repositório de chave/atributo com um design sem esquema. O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL, que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Os usos comuns do armazenamento de tabelas incluem:
- Armazenamento de TBs de dados estruturados capazes de servir aplicativos de escala da Web.
- Armazenamento de conjuntos de valores que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e podem ser desnormalizados para acesso rápido.
- Consulta rápida de dados usando um índice clusterizado.
- Acessando dados usando o protocolo OData e consultas LINQ com bibliotecas do WCF Data Service .NET.

Você pode usar o armazenamento de tabelas para armazenar e consultar grandes conjuntos de dados estruturados não relacionais, e suas tabelas serão dimensionadas conforme a demanda aumentar.

**Referências**
- [Azure portal](https://portal.azure.com)
- [Documentação](/azure/storage/tables/table-storage-overview)
- [Exemplos](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Inícios Rápidos](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Arquivos do Azure
Os [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/) permite que você configure compartilhamentos de arquivo por rede altamente disponíveis que podem ser acessados usando o protocolo SMB (Server Message Block) padrão. Várias VMs podem compartilhar os mesmos arquivos com acesso de leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas de cliente de armazenamento. Você pode acessar os arquivos de qualquer lugar no mundo usando uma URL que aponta para o arquivo e inclui um token de SAS (assinatura de acesso compartilhado). Você pode gerar tokens SAS; eles permitem o acesso específico a um ativo privado durante período especificado.

Os compartilhamentos de arquivos do Azure podem ser usados para:
- Substituir ou complementar servidores de arquivos locais: sistemas operacionais populares, como Windows, macOS e Linux, podem montar diretamente compartilhamentos de arquivos do Azure onde quer que estejam no mundo. Os compartilhamentos de arquivos do Azure também podem ser replicados com a Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados.
- Migre **e SHIFTe os aplicativos** para a nuvem que esperam que um compartilhamento de arquivos armazene os dados do aplicativo ou do usuário do arquivo.
- **Simplificar o desenvolvimento em nuvem**: os arquivos do Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento em nuvem. Por exemplo:
    - Configurações de aplicativo compartilhado: um padrão comum para aplicativos distribuídos é ter arquivos de configuração em um local centralizado, onde eles podem ser acessados de várias instâncias do aplicativo. As instâncias do aplicativo podem carregar sua configuração por meio da API REST de Arquivo e os usuários podem acessá-las conforme necessário com a montagem local do compartilhamento SMB.
    - Compartilhamento de diagnóstico: um compartilhamento de arquivos do Azure é um local conveniente para aplicativos de nuvem gravarem seus logs, métricas e despejos de memória. Os logs podem ser gravados pelas instâncias do aplicativo por meio da API REST de Arquivo e os desenvolvedores podem acessá-los ao montar o compartilhamento de arquivos em seu computador local. Isso permite maior flexibilidade, já que os desenvolvedores podem adotar o desenvolvimento em nuvem sem a necessidade de abandonar as ferramentas existentes que conhecemos e amamos.

**Referências**
- [Azure portal](https://portal.azure.com)
- [Documentação](/azure/storage/files/storage-files-introduction)
- [Inícios Rápidos](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queues"></a>Filas do Azure
O [armazenamento de filas do Azure](https://azure.microsoft.com/services/storage/queues/) é um serviço para armazenar grandes números de mensagens. Você acessa as mensagens em qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono.

**Referências**
- [Azure portal](https://portal.azure.com)
- [Documentação](/azure/storage/queues/)
- [Inícios Rápidos](/azure/storage/queues/storage-quickstart-queues-portal)
- [Exemplos](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
