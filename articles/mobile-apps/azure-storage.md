---
title: Armazenamento em nuvem para criar aplicativos altamente seguros, duráveis e escaláveis com armazenamento azure
description: Conheça os serviços para armazenar dados de aplicativos móveis estruturados e não estruturados na nuvem.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240986"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicativos altamente seguros, duráveis e escaláveis com armazenamento azure
[O Azure Storage](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicativos modernos que oferece uma armazenamento de objetos maciçamente escalável para objetos de dados, um serviço de sistema de arquivos para a nuvem, uma loja de mensagens para mensagens confiáveis e uma loja NoSQL. O Armazenamento do Azure é:
- **Durável e altamente disponível:** A redundância garante que seus dados estejam seguros em caso de falhas transitórias de hardware. Você também pode optar por replicar dados em datacenters ou regiões geográficas para obter mais proteção contra catástrofes ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.
- **Seguro:** Todos os dados gravados no Azure Storage são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.
- **Escalável:** Os serviços foram projetados para serem maciçamente escaláveis para atender às necessidades de armazenamento de dados e desempenho dos aplicativos atuais.
- **Gerenciado:** O Azure lida com manutenção de hardware, atualizações e problemas críticos para você.
- **Acessível:** Os dados são acessíveis de qualquer lugar do mundo em HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes em uma variedade de idiomas, como .NET, Java, Node.js, Python, PHP, Ruby e Go, e uma API rest madura. O scripting é suportado no Azure PowerShell ou no Azure CLI. O portal Azure e o Azure Storage Explorer oferecem soluções visuais fáceis para trabalhar com seus dados.

Use os seguintes serviços para permitir o armazenamento em nuvem em seus aplicativos móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
[O armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) oferece uma solução de armazenamento de objetos para a nuvem. O armazenamento blob é otimizado para armazenar grandes quantidades de dados não estruturados que não aderem a um determinado modelo de dados ou definição, como texto ou binário. Ele suporta uma variedade de idiomas que as bibliotecas de clientes usam. O armazenamento blob foi projetado para:
- Sirva imagens ou documentos diretamente para um navegador.
- Armazene arquivos para acesso distribuído.
- Transmitir vídeo e áudio.
- Escreva para registrar arquivos.
- Armazene dados para backup e restauração, recuperação de desastres e arquivamento.
- Armazene dados para análise por um serviço no local ou hospedado no Azure.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação do armazenamento de blobs do Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Inícios rápidos](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento da tabela do Azure
[O armazenamento azure Table](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados NoSQL estruturados na nuvem e fornece uma chave ou armazenamento de atributos com um design sem esquema. O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL, que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas azure são ideais para armazenar dados estruturados e não relacionais. O armazenamento de mesa é normalmente usado para:
- Armazene terabytes de dados estruturados capazes de servir aplicativos em escala web.
- Armazene conjuntos de dados que não exijam adesão complexa, chaves estrangeiras ou procedimentos armazenados e podem ser desnormalizados para acesso rápido.
- Consulta rapidamente os dados usando um índice agrupado.
- Acesse os dados usando o protocolo OData e consultas LINQ com bibliotecas De Dados .NET da Windows Communication Foundation (Windows Communication Foundation).

Você pode usar o armazenamento de tabela para armazenar e consultar enormes conjuntos de dados estruturados e não relacionais. Suas tabelas são dimensionadas à medida que a demanda aumenta.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação do Armazenamento de Tabelas do Azure](/azure/storage/tables/table-storage-overview)
- [Amostras](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Inícios rápidos](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Arquivos do Azure
Com [o Azure Files,](https://azure.microsoft.com/services/storage/files/)você pode configurar compartilhamentos de arquivos de rede altamente disponíveis que podem ser acessados usando o protocolo padrão server message block (SMB). Várias VMs podem compartilhar os mesmos arquivos com acesso a leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas clientes de armazenamento. Você pode acessar os arquivos de qualquer lugar do mundo usando uma URL que aponta para o arquivo e inclui um token SAS (SAS) de Assinatura de Acesso Compartilhado. Você pode gerar tokens SAS. Eles permitem acesso específico a um ativo privado por um período específico de tempo.

Os compartilhamentos de arquivos do Azure podem ser usados para:
- **Substitua ou complemente servidores de arquivos no local:** Sistemas operacionais populares como Windows, macOS e Linux podem montar diretamente os compartilhamentos de arquivos do Azure onde quer que estejam no mundo. Os compartilhamentos de arquivos do Azure também podem ser replicados com a Sincronização de Arquivos do Azure para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde estão sendo usados.
- **Aplicativos de elevação e turno:** Migre os aplicativos para a nuvem que esperam um compartilhamento de arquivos para armazenar o aplicativo de arquivos ou os dados do usuário.
- **Simplificar o desenvolvimento em nuvem:** Os Arquivos Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento de nuvem. Por exemplo: 
    - **Configurações compartilhadas do aplicativo:** Um padrão comum para aplicativos distribuídos é ter arquivos de configuração em um local centralizado onde eles podem ser acessados de muitas instâncias de aplicativos. As instâncias do aplicativo podem carregar sua configuração através da API File REST. Os usuários podem acessá-los conforme necessário, aumentando o compartilhamento de SMB localmente.
    - **Compartilhamento de diagnóstico:** Um compartilhamento de arquivos Do Zure é um lugar conveniente para aplicativos em nuvem escreverem seus registros, métricas e dumps de falhas. Os logs podem ser escritos pelas instâncias do aplicativo através da API File REST. Os desenvolvedores podem acessá-los montando o compartilhamento de arquivos em sua máquina local. Esse recurso permite grande flexibilidade. Os desenvolvedores podem adotar o desenvolvimento em nuvem sem ter que abandonar as ferramentas existentes que conhecem.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação dos Arquivos do Azure](/azure/storage/files/storage-files-introduction)
- [Inícios rápidos](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure
[O armazenamento azure Queue](https://azure.microsoft.com/services/storage/queues/) é um serviço para armazenar um grande número de mensagens. Você acessa mensagens de qualquer lugar do mundo através de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite total de capacidade de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação de armazenamento de Filas do Azure](/azure/storage/queues/)
- [Inícios rápidos](/azure/storage/queues/storage-quickstart-queues-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
