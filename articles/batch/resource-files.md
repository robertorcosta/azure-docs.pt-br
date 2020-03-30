---
title: Criação e uso de arquivos de recursos - Azure Batch
description: Saiba como criar arquivos de recursos batch de várias fontes de entrada. Este artigo abrange alguns métodos comuns sobre como criá-los e colocá-los em uma VM.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531134"
---
# <a name="creating-and-using-resource-files"></a>Criação e uso de arquivos de recursos

Uma tarefa do Azure Batch muitas vezes requer algum tipo de dados para processar. Os arquivos de recursos são a maneira de fornecer esses dados para sua VM (Batch virtual Machine) através de uma tarefa. Todos os tipos de tarefas suportam arquivos de recursos: tarefas, tarefas iniciais, tarefas de preparação de trabalho, tarefas de liberação de trabalho, etc. Este artigo abrange alguns métodos comuns de como criar arquivos de recursos e colocá-los em uma VM.  

Os arquivos de recursos colocam dados em uma VM em batch, mas o tipo de dados e como ele é usado é flexível. Há, no entanto, alguns casos de uso comum:

1. Provisão de arquivos comuns em cada VM usando arquivos de recursos em uma tarefa inicial
1. Dados de entrada de provisão a serem processados por tarefas

Arquivos comuns podem ser, por exemplo, arquivos em uma tarefa inicial usada para instalar aplicativos que suas tarefas executam. Os dados de entrada podem ser dados brutos de imagem ou vídeo, ou qualquer informação a ser processada pela Batch.

## <a name="types-of-resource-files"></a>Tipos de arquivos de recursos

Existem algumas opções diferentes disponíveis para gerar arquivos de recursos. O processo de criação de arquivos de recursos varia dependendo de onde os dados originais são armazenados.

Opções para criar um arquivo de recursos:

- [URL do contêiner de armazenamento](#storage-container-url): Gera um arquivo de recurso de qualquer contêiner de armazenamento no Azure
- [Nome do contêiner de armazenamento](#storage-container-name): Gera um arquivo de recurso a partir do nome de um contêiner em uma conta de armazenamento do Azure vinculada ao Batch
- [Ponto final da Web](#web-endpoint): Gera um arquivo de recurso a partir de qualquer URL HTTP válido

### <a name="storage-container-url"></a>URL do contêiner de armazenamento

Usando um URL de contêiner de armazenamento significa que, com as permissões corretas, você pode acessar arquivos em qualquer contêiner de armazenamento no Azure. 

Neste exemplo C#, os arquivos já foram carregados em um contêiner de armazenamento Do Zure como armazenamento blob. Para acessar os dados necessários para criar um arquivo de recursos, primeiro precisamos ter acesso ao contêiner de armazenamento.

Crie um URI de assinatura de acesso compartilhado (SAS) com as permissões corretas para acessar o contêiner de armazenamento. Defina o tempo de validade e as permissões para o SAS. Neste caso, não é especificado o tempo de início, de modo que o SAS se torna válido imediatamente e expira duas horas após a sua gerada.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Para acesso a contêineres, você deve ter ambos `Read` e `List` permissões, enquanto que com acesso blob, você só precisa de `Read` permissão.

Uma vez configuradas as permissões, crie o token SAS e forme a URL SAS para acesso ao contêiner de armazenamento. Usando a URL SAS formatada para o [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)contêiner de armazenamento, gere um arquivo de recurso com .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Uma alternativa para gerar uma URL SAS é permitir o acesso de leitura anônimo e público a um contêiner e suas bolhas no armazenamento Azure Blob. Ao fazer isso, você pode conceder acesso somente à leitura a esses recursos sem compartilhar a chave da sua conta e sem exigir um SAS. O acesso público de leitura é normalmente usado para cenários onde você quer que certas bolhas estejam sempre disponíveis para acesso de leitura anônimo. Se esse cenário se adequar à sua solução, consulte o artigo [de acesso anônimo ao blobs](../storage/blobs/storage-manage-access-to-resources.md) para saber mais sobre como gerenciar o acesso aos seus dados blob.

### <a name="storage-container-name"></a>Nome do contêiner de armazenamento

Em vez de configurar e criar uma URL SAS, você pode usar o nome do seu contêiner de armazenamento Azure para acessar seus dados blob. O contêiner de armazenamento que você usa deve estar na conta de armazenamento do Azure que está vinculada à sua conta Batch. Essa conta de armazenamento é conhecida como conta de armazenamento automático. O uso do contêiner de armazenamento automático permite contornar a configuração e a criação de uma URL SAS para acessar um contêiner de armazenamento.

Neste exemplo, assumimos que os dados a serem usados para criação de arquivos de recursos já estão em uma conta do Azure Storage vinculada à sua conta Batch. Se você não tiver uma conta de armazenamento automático, consulte as etapas em [Criar uma conta em lote](batch-account-create-portal.md) para obter detalhes sobre como criar e vincular uma conta.

Ao usar uma conta de armazenamento vinculada, você não precisa criar e configurar uma URL SAS em um contêiner de armazenamento. Em vez disso, forneça o nome do contêiner de armazenamento em sua conta de armazenamento vinculada.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Ponto final da Web

Os dados que não são carregados no Azure Storage ainda podem ser usados para criar arquivos de recursos. Você pode especificar qualquer URL HTTP válida contendo seus dados de entrada. A URL é fornecida à API de lote e, em seguida, os dados são usados para criar um arquivo de recursos.

No exemplo C# a seguir, os dados de entrada estão hospedados em um ponto final fictício do GitHub. A API recupera o arquivo do ponto final da Web válido e gera um arquivo de recurso a ser consumido pela sua tarefa. Não são necessárias credenciais para este cenário.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Cada tarefa do Azure Batch usa arquivos de forma diferente, e é por isso que o Batch tem opções disponíveis para gerenciar arquivos em tarefas. Os seguintes cenários não devem ser abrangentes, mas, em vez disso, abrangem algumas situações comuns e fornecem recomendações.

### <a name="many-resource-files"></a>Muitos arquivos de recursos

Seu trabalho em lote pode conter várias tarefas que todos usam os mesmos arquivos comuns. Se os arquivos de tarefas comuns forem compartilhados entre muitas tarefas, usar um pacote de aplicativos para conter os arquivos em vez de usar arquivos de recursos pode ser uma opção melhor. Os pacotes de aplicativos fornecem otimização para a velocidade de download. Além disso, os dados em pacotes de aplicativos são armazenados em cache entre tarefas, portanto, se seus arquivos de tarefas não mudarem com frequência, os pacotes de aplicativos podem ser adequados para sua solução. Com pacotes de aplicativos, você não precisa gerenciar manualmente vários arquivos de recursos ou gerar URLs SAS para acessar os arquivos no Azure Storage. O batch funciona em segundo plano com o Azure Storage para armazenar e implantar pacotes de aplicativos para calcular os nós.

Se cada tarefa tem muitos arquivos exclusivos para essa tarefa, os arquivos de recursos são a melhor opção porque tarefas que usam arquivos exclusivos muitas vezes precisam ser atualizadas ou substituídas, o que não é tão fácil de fazer com o conteúdo de pacotes de aplicativos. Os arquivos de recursos fornecem flexibilidade adicional para atualizar, adicionar ou editar arquivos individuais.

### <a name="number-of-resource-files-per-task"></a>Número de arquivos de recursos por tarefa

Se houver várias centenas de arquivos de recursos especificados em uma tarefa, Batch pode rejeitar a tarefa como sendo muito grande. É melhor manter suas tarefas pequenas minimizando o número de arquivos de recursos na tarefa em si.

Se não houver como minimizar o número de arquivos que sua tarefa precisa, você pode otimizar a tarefa criando um único arquivo de recurso que faz referência a um contêiner de armazenamento de arquivos de recursos. Para fazer isso, coloque seus arquivos de recursos em um contêiner do Azure Storage e use os [diferentes métodos](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) de "contêiner" para arquivos de recursos. Use as opções de prefixo blob para especificar coleções de arquivos a serem baixados para suas tarefas.

## <a name="next-steps"></a>Próximas etapas

- Conheça [os pacotes de aplicativos](batch-application-packages.md) como uma alternativa aos arquivos de recursos.
- Para obter mais informações sobre o uso de contêineres para arquivos de recursos, consulte [cargas de trabalho de contêiner](batch-docker-container-workloads.md).
- Para saber como coletar e salvar os dados de saída de suas tarefas, consulte [Persistir a saída de tarefas e trabalho](batch-task-output.md).
- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
