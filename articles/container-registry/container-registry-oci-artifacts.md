---
title: Empurre e puxe o artefato OCI
description: Empurre e puxe artefatos da Open Container Initiative (OCI) usando um registro privado de contêineres no Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371045"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Empurre e puxe um artefato OCI usando um registro de contêiner Azure

Você pode usar um registro de contêiner do Azure para armazenar e gerenciar [artefatos OCI (Open Container Initiative, iniciativa](container-registry-image-formats.md#oci-artifacts) de contêiner aberto), bem como imagens de contêiner compatíveis com Docker e Docker.

Para demonstrar esse recurso, este artigo mostra como usar a ferramenta [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) para empurrar um artefato de amostra - um arquivo de texto - para um registro de contêiner do Azure. Então, retire o artefato do registro. Você pode gerenciar uma variedade de artefatos OCI em um registro de contêiner do Azure usando diferentes ferramentas de linha de comando apropriadas a cada artefato.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de contêineres do Azure** - Crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
* **Ferramenta ORAS** - Baixe e instale uma versão ORAS atual para o seu sistema operacional a partir do [repo GitHub](https://github.com/deislabs/oras/releases). A ferramenta é lançada como uma`.tar.gz` bola de tarball compactada (arquivo). Extrair e instalar o arquivo usando procedimentos padrão para o seu sistema operacional.
* **Principal do serviço de diretório ativo do Azure (opcional)** - Para autenticar diretamente com o ORAS, crie um [princípio de serviço](container-registry-auth-service-principal.md) para acessar seu registro. Certifique-se de que o diretor de serviço é atribuído a uma função como AcrPush para que ele tenha permissões para empurrar e puxar artefatos.
* **Azure CLI (opcional)** - Para usar uma identidade individual, você precisa de uma instalação local do Azure CLI. Recomenda-se a versão 2.0.71 ou posterior. Corra `az --version `para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli).
* **Docker (opcional)** - Para usar uma identidade individual, você também deve ter o Docker instalado localmente, para autenticar com o registro. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Faça login em um registro

Esta seção mostra dois fluxos de trabalho sugeridos para entrar no registro, dependendo da identidade usada. Escolha o método adequado para o seu ambiente.

### <a name="sign-in-with-oras"></a>Faça login com a ORAS

Usando um [diretor de serviço](container-registry-auth-service-principal.md) `oras login` com direitos push, execute o comando para entrar no registro usando o ID e senha do aplicativo principal do serviço. Especifique o nome do registro totalmente qualificado (todas as minúsculas), neste caso *myregistry.azurecr.io*. O ID do aplicativo principal do `$SP_APP_ID`serviço é passado `$SP_PASSWD`na variável ambiente , e a senha na variável .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Para ler a senha do `--password-stdin`Stdin, use .

### <a name="sign-in-with-azure-cli"></a>Entrar com a CLI do Azure

[Faça login na](/cli/azure/authenticate-azure-cli) CLI do Azure com sua identidade para empurrar e retirar artefatos do registro do contêiner.

Em seguida, use o [login az acr do](/cli/azure/acr?view=azure-cli-latest#az-acr-login) comando Azure CLI para acessar o registro. Por exemplo, para autenticar em um registro chamado *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`usa o cliente Docker para definir um token `docker.config` Azure Active Directory no arquivo. O cliente Docker deve ser instalado e executado para completar o fluxo de autenticação individual.

## <a name="push-an-artifact"></a>Empurre um artefato

Crie um arquivo de texto em um diretório local de trabalho com algum texto de exemplo. Por exemplo, em uma concha de bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Use `oras push` o comando para empurrar este arquivo de texto para o seu registro. O exemplo a seguir empurra `samples/artifact` o arquivo de texto de amostra para o repo. O registro é identificado com o nome de registro totalmente qualificado *myregistry.azurecr.io* (todas as minúsculas). O artefato `1.0`está marcado. O artefato tem um tipo indefinido, por padrão, identificado `artifact.txt`pela seqüência de tipos de *mídia* seguindo o nome do arquivo . Consulte [artefatos OCI](https://github.com/opencontainers/artifacts) para obter outros tipos. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

A saída para um empurrão bem-sucedido é semelhante à seguinte:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Para gerenciar artefatos em seu registro, se você estiver usando `az acr` o Azure CLI, execute comandos padrão para gerenciar imagens. Por exemplo, obtenha os atributos do artefato usando o comando [az acr repositório show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

A saída deverá ser semelhante a esta:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Puxe um artefato

Execute `oras pull` o comando para retirar o artefato do seu registro.

Primeiro remova o arquivo de texto do diretório de trabalho local:

```bash
rm artifact.txt
```

Execute `oras pull` para puxar o artefato e especifique o tipo de mídia usado para empurrar o artefato:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifique se a tração foi bem sucedida:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Remova o artefato (opcional)

Para remover o artefato do registro de contêiner do Azure, use o comando [az acr repositório delete.][az-acr-repository-delete] O exemplo a seguir remove o artefato armazenado lá:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a Biblioteca ORAS,](https://github.com/deislabs/oras/tree/master/docs)incluindo como configurar um manifesto para um artefato
* Visite o repo [da OCI Artifacts](https://github.com/opencontainers/artifacts) para obter informações de referência sobre novos tipos de artefatos



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
