---
title: '.NET SDK: Operações do sistema de arquivos no Azure Data Lake Storage Gen1'
description: Use o Azure Data Lake Storage Gen1 .NET SDK para operações de sistema de arquivos no Data Lake Storage Gen1, como criar pastas, etc.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638894"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operações do sistema de arquivos no Data Lake Storage Gen1 usando o .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Neste artigo, você aprende como executar operações de sistema de arquivos no Data Lake Storage Gen1 usando o .NET SDK. As operações de filesytem incluem a criação de pastas em uma conta do Data Lake Storage Gen1, carregamento de arquivos, download de arquivos etc.

Para obter instruções sobre como fazer operações de gerenciamento de contas no Data Lake Storage Gen1 usando o SDK .NET, consulte [Operações de gerenciamento de contas no Data Lake Storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2013 ou superior**. As instruções deste artigo usam o Visual Studio 2019.

* **Uma assinatura do Azure.** Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma conta, consulte [Comece com o Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET

O exemplo de código disponível [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo percorre com você as principais partes do código.

1. No Visual Studio, selecione o menu **Arquivo,** **Novo**e, em seguida, **Projeto**.
1. Escolha **o aplicativo de console (.NET Framework)** e selecione **Next**.
1. Em **Nome do projeto**, digite `CreateADLApplication` e, em seguida, selecione **Criar**.
1. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   1. Na guia **NuGet Package Manager,** **certifique-se de** que a origem do pacote está definida como **nuget.org**. Além disso, certifique-se de que a caixa **de seleção de pré-lançamento** include está selecionada.
   1. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.DataLake.Store`- Este artigo usa v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- Este artigo usa v2.3.1.

      Feche o **NuGet Package Manager**.

1. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Declare as variáveis, conforme mostrado abaixo, e forneça os valores para os espaços reservados. Além disso, o caminho local e o nome de arquivo fornecido aqui existem no computador.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

Nas demais seções do artigo, você pode ver como usar os métodos .NET disponíveis para fazer operações como autenticação, upload de arquivos, etc.

## <a name="authentication"></a>Autenticação

* Para autenticação do usuário final para seu aplicativo, veja [Autenticação de usuário final com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Para autenticação de serviço a serviço do aplicativo, confira [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Criar objeto de cliente

O snippet de código a seguir cria o objeto de cliente de sistema de arquivos do Data Lake Storage Gen1, que é usado para emitir solicitações para o serviço.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Criar um arquivo e diretório

Adicione o snippet de código a seguir ao seu aplicativo. Este trecho adiciona um arquivo e qualquer diretório pai que não exista.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Anexar a um arquivo

O snippet de código a seguir acrescenta dados a um arquivo existente na conta do Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Ler um arquivo

O snippet de código a seguir lê o conteúdo de um arquivo na conta do Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Obter propriedades de arquivo

O snippet de código a seguir retorna as propriedades associadas a um arquivo ou diretório.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

A definição `PrintDirectoryEntry` do método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Renomear um arquivo

O snippet de código a seguir renomeia um arquivo existente em uma conta do Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Enumerar um diretório

O seguinte trecho enumera diretórios em uma conta Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

A definição `PrintDirectoryEntry` do método está disponível como parte da amostra [no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Excluir diretórios recursivamente

O trecho a seguir exclui um diretório, e todos os seus subdiretórios, recursivamente.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Exemplos

Aqui estão algumas amostras que mostram como usar o Data Lake Storage Gen1 Filesystem SDK.

* [Amostra básica no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Amostra avançada no GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Confira também

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Referência de SDK do .NET do Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas

* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
