---
title: Usar um feed NuGet personalizado
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use um feed NuGet personalizado para acessar e usar os pacotes NuGet em um Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325735"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Use um feed NuGet personalizado com espaços de dev do Azure

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacotes em um projeto. O Azure Dev Spaces precisa acessar este feed para que as dependências sejam instaladas corretamente no contêiner Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` para sua `PackageReference` dependência no arquivo o nó. Por exemplo: 

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um arquivo [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta `packageSources` `packageSourceCredentials` do projeto e defina as seções e e para o feed NuGet. A `packageSources` seção contém sua url de alimentação, que deve estar acessível a partir do seu cluster AKS. São `packageSourceCredentials` as credenciais para acessar o feed. Por exemplo: 

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Atualize seus arquivos `NuGet.Config` Docker para copiar o arquivo para a imagem. Por exemplo: 

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No `NuGet.Config`Windows, `Nuget.Config`e `nuget.config` tudo funciona como nomes de arquivo válidos. No Linux, `NuGet.Config` só é um nome de arquivo válido para este arquivo. Como o Azure Dev Spaces usa Docker `NuGet.Config`e Linux, este arquivo deve ser nomeado . Você pode corrigir o nome `dotnet restore --configfile nuget.config`manualmente ou executando .


Se você estiver usando o Git, você não deve ter as credenciais para o seu feed NuGet no controle de versão. Adicione `NuGet.Config` ao `.gitignore` para o seu `NuGet.Config` projeto para que o arquivo não seja adicionado ao controle da versão. O Azure Dev Spaces precisará deste arquivo durante o processo de compilação `.gitignore` de `.dockerignore` imagens de contêiner, mas, por padrão, ele respeita as regras definidas dentro e durante a sincronização. Para alterar o padrão e permitir que o `NuGet.Config` Azure `azds.yaml` Dev Spaces sincronize o arquivo, atualize o arquivo:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se você não estiver usando o Git, você pode pular este passo.

Da próxima vez `azds up` que `F5` você executar ou bater no Visual Studio Code ou `NuGet.Config` Visual Studio, o Azure Dev Spaces sincronizará o arquivo usá-lo para instalar dependências de pacotes.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [nuget e como ele funciona](https://docs.microsoft.com/nuget/what-is-nuget).