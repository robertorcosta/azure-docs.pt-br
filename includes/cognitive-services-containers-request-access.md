---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229289"
---
Preencha e envie o [formulário de solicitação de solicitação de visão de serviços cognitivos](https://aka.ms/VisionContainersPreview) para solicitar acesso ao contêiner. O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de enviar o formulário, a equipe do Azure Cognitive Services o analisa para ter certeza de que você atende aos critérios de acesso ao registro privado de contêineres.

> [!IMPORTANT]
> Você deve usar um endereço de e-mail associado a uma conta Microsoft Account (MSA) ou a uma conta do Azure Active Directory (Azure AD) no formulário.

Se sua solicitação for aprovada, você recebe um e-mail com instruções que descrevem como obter suas credenciais e acessar o registro privado de contêineres.

## <a name="log-in-to-the-private-container-registry"></a>Efetue login no registro do contêiner particular

Existem várias maneiras de autenticar com o registro privado de contêineres para contêineres de Serviços Cognitivos. Recomendamos que você use o método de linha de comando usando o [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Use o comando [docker login,](https://docs.docker.com/engine/reference/commandline/login/) como mostrado no `containerpreview.azurecr.io`exemplo a seguir, para fazer login , que é o registro privado de contêineres para contêineres de Serviços Cognitivos. Substitua * \<\> * o nome de usuário com o nome de usuário e * \<a senha\> * pela senha fornecida nas credenciais recebidas da equipe de Serviços Cognitivos do Azure.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você garantiu suas credenciais em um arquivo de texto, `docker login` você pode concatenar o conteúdo desse arquivo de texto para o comando. Use `cat` o comando, como mostrado no exemplo a seguir. Substitua * \<\> a senhaArquivo* pelo caminho e nome do arquivo de texto que contém a senha. Substitua * \<\> * o nome de usuário pelo nome de usuário fornecido em suas credenciais.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

