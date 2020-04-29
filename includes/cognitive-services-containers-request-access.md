---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "68229289"
---
Preencha e envie o [formulário de solicitação de contêineres de visão de serviços cognitivas](https://aka.ms/VisionContainersPreview) para solicitar acesso ao contêiner. O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de enviar o formulário, a equipe de serviços cognitivas do Azure revisa-o para garantir que você atenda aos critérios de acesso ao registro de contêiner privado.

> [!IMPORTANT]
> Você deve usar um endereço de email associado a uma conta da Microsoft (MSA) ou a uma conta do Azure Active Directory (Azure AD) no formulário.

Se sua solicitação for aprovada, você receberá um email com instruções que descrevem como obter suas credenciais e acessar o registro de contêiner privado.

## <a name="log-in-to-the-private-container-registry"></a>Efetue login no registro do contêiner particular

Há várias maneiras de se autenticar com o registro de contêiner privado para contêineres de serviços cognitivas. Recomendamos que você use o método de linha de comando usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , conforme mostrado no exemplo a seguir, para fazer logon no `containerpreview.azurecr.io`, que é o registro de contêiner privado para contêineres de serviços cognitivas. Substitua * \<username\> * pelo nome de usuário e * \<senha\> * pela senha fornecida nas credenciais recebidas da equipe de serviços cognitivas do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você protegeu suas credenciais em um arquivo de texto, você pode concatenar o conteúdo desse arquivo de texto `docker login` para o comando. Use o `cat` comando, conforme mostrado no exemplo a seguir. Substitua * \<passwordfile\> * pelo caminho e nome do arquivo de texto que contém a senha. Substitua * \<username\> * pelo nome de usuário fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

