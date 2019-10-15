---
title: Configurar seu Servidor de Configuração no Azure Spring Cloud | Microsoft Docs
description: Neste tutorial, você aprenderá como configurar um Servidor de Configuração do Spring Cloud para seu Azure Spring Cloud no portal do Azure
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038648"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Tutorial: Configurar um Servidor de Configuração do Spring Cloud para seu serviço

Este tutorial mostrará como conectar um Servidor de Configuração do Spring Cloud ao seu serviço do Azure Spring Cloud.

A configuração do Spring Cloud oferece suporte do servidor e do cliente para configuração externalizada em um sistema distribuído. Com o Servidor de Configuração, você tem um local central para gerenciar propriedades externas para aplicativos em todos os ambientes. Para saber mais, acesse a [Referência ao Servidor de Configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço do Azure Spring Cloud já provisionado e em execução.  Conclua [este início rápido](spring-cloud-quickstart-launch-app-cli.md) para provisionar e iniciar um serviço do Azure Spring Cloud.


## <a name="restriction"></a>Restrição

Há algumas restrições quando você usa o __Servidor de Configuração__ com um back-end do git. Algumas propriedades serão automaticamente injetadas em seu ambiente de aplicativo para acessar o __Servidor de Configuração__ e a __Descoberta de Serviço__. Se você também configurar essas propriedades em seus arquivos do **Servidor de Configuração**, poderá enfrentar conflitos e um comportamento inesperado.  Essas propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> É altamente recomendável __NÃO__ colocar as propriedades acima em seus arquivos de aplicativo do __Servidor de Configuração__.

## <a name="create-your-config-server-files"></a>Criar seus arquivos do Servidor de Configuração

O Azure Spring Cloud dá suporte ao Azure DevOps, GitHub, GitLab e Bitbucket para armazenar seus arquivos do Servidor de Configuração.  Quando seu repositório estiver pronto, crie os arquivos de configuração com as instruções abaixo e armazene-os lá.

Além disso, algumas propriedades configuráveis só estão disponíveis para alguns tipos. As subseções a seguir listam as propriedades de cada tipo de repositório.


### <a name="public-repository"></a>Repositório público

Ao usar um repositório público, suas propriedades configuráveis serão mais limitadas.

Todas as propriedades configuráveis usadas para configurar o repositório público `Git` estão listadas abaixo.

> [!NOTE]
> No momento, há suporte apenas para a convenção de nomenclatura de palavras separadas por hífen (“-”). Por exemplo, use `default-label`, mas não `defaultLabel`.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório `Git`. Deve ser `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis usadas para configurar o repositório `Git` privado com `Ssh` estão listadas abaixo.

> [!NOTE]
> No momento, há suporte apenas para a convenção de nomenclatura de palavras separadas por hífen (“-”). Por exemplo, use `default-label`, mas não `defaultLabel`.

| Propriedade                   | Obrigatório | Recurso                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label`            | `no`     | O rótulo padrão do repositório `Git`. Deve ser `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`             | `no`     | Uma matriz de cadeia de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `private-key`              | `no`     | A chave privada `Ssh` para acessar o repositório `Git`, __obrigatória__ quando `uri` é iniciado com `git@` ou `ssh://`. |
| `host-key`                 | `no`     | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | O algoritmo de chave de host. Deve ser um do `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` e `ecdsa-sha2-nistp521`. Obrigatório somente se `host-key` existir. |
| `strict-host-key-checking` | `no`     | Indica se a inicialização do servidor de configuração falhará quando usa o `host-key` fornecido. Deve ser `true` (valor padrão) ou `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação Básica

Todas as propriedades configuráveis usadas para configurar o repositório Git privado com autenticação Básica estão listadas abaixo.

> [!NOTE]
> No momento, há suporte apenas para a convenção de nomenclatura de palavras separadas por hífen (“-”). Por exemplo, você pode usar `default-label`, mas não `defaultLabel`.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório `Git`. Deve ser `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeia de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `username`      | `no`     | O `username` usado para acessar o servidor do repositório `Git`. `Http Basic Authentication` de suporte ao servidor do repositório `Git` __obrigatório__. |
| `password`      | `no`     | A senha usada para acessar o servidor do repositório `Git`, `Git` de suporte ao servidor do repositório `Http Basic Authentication` __obrigatório__. |

> [!NOTE]
> Alguns servidores do repositório `Git`, como o GitHub, dão suporte a "token pessoal" ou a "token acesso" como uma senha para `HTTP Basic Authentication`. Você também pode usar esse tipo de token como senha aqui e o “token pessoal” ou o “token de acesso” não expirará. Porém, para servidores do repositório Git, como o BitBucket e o Azure DevOps, o token expirará em uma ou duas horas, o que torna essa opção inviável para usar com o Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios Git com padrão

Todas as propriedades configuráveis usadas para configurar os repositórios Git com padrão estão listadas abaixo.

> [!NOTE]
> No momento, há suporte apenas para a convenção de nomenclatura de palavras separadas por hífen (“-”). Por exemplo, você pode usar `default-label`, mas não `defaultLabel`.

| Propriedade                           | Obrigatório         | Recurso                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Um mapa é composto por configurações de repositórios `Git` com um nome determinado. |
| `repos."uri"`                      | `yes` em `repos` | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `repos."name"`                     | `yes` em `repos` | Um nome para identificação de um Repositório `Git`. __Obrigatório__ somente se `repos` existir. Pelo exemplo acima, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Uma matriz de cadeia de caracteres usada para corresponder o nome do aplicativo. Para cada padrão, use o formato `{application}/{profile}` com curingas. |
| `repos."default-label"`            | `no`             | O rótulo padrão do repositório `Git`. Deve ser `branch name`, `tag name` ou `commit-id` do repositório. |
| `repos."search-paths`"             | `no`             | Uma matriz de cadeia de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `repos."username"`                 | `no`             | O `username` usado para acessar o servidor do repositório `Git`. `Http Basic Authentication` de suporte ao servidor do repositório `Git` __obrigatório__. |
| `repos."password"`                 | `no`             | A senha usada para acessar o servidor do repositório `Git`, `Git` de suporte ao servidor do repositório `Http Basic Authentication` __obrigatório__. |
| `repos."private-key"`              | `no`             | A chave privada `Ssh` para acessar o repositório `Git`, __obrigatória__ quando `uri` é iniciado com `git@` ou `ssh://`. |
| `repos."host-key"`                 | `no`             | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | O algoritmo de chave de host. Deve ser um do `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` e `ecdsa-sha2-nistp521`. Obrigatório __somente__ se `host-key` existir. |
| `repos."strict-host-key-checking"` | `no`             | Indica se a inicialização do servidor de configuração falhará quando usa o `host-key` fornecido. Deve ser `true` (valor padrão) ou `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importar o arquivo `application.yml` da Configuração do Spring Cloud

Você pode importar algumas configurações padrão do servidor de configuração diretamente do site da [Configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config). Você pode fazer isso diretamente do portal do Azure. Portanto, não é necessário executar nenhuma etapa agora para preparar seu repositório ou arquivos do servidor de configuração.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Anexar o repositório do Servidor de Configuração ao Azure Spring Cloud

Agora que você salvou seus arquivos de configuração em um repositório, é necessário conectar o Azure Spring Cloud a ele.

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Navegue até a página **Visão geral** do Azure Spring Cloud.

1. Acesse a guia **Servidor de Configuração** no título **Configurações** no menu do lado esquerdo.

### <a name="public-repository"></a>Repositório público

Se o repositório for público, basta clicar no botão **Público** e colar a URL.

### <a name="private-repository"></a>Repositório Privado

O Azure Spring Cloud dá suporte à autenticação SSH. Siga as instruções no portal do Azure para adicionar a chave pública ao seu repositório. Em seguida, inclua sua chave privada no arquivo de configuração.

Clique em **Aplicar** para concluir a configuração do seu Servidor de Configuração.


## <a name="delete-your-app-configuration"></a>Excluir a configuração de aplicativos

Após salvar um arquivo de configuração, o botão **Excluir configuração de aplicativos** será exibido na guia **Configuração**. Isso apagará suas configurações existentes completamente. Você deverá fazer isso se desejar conectar seu servidor de configuração a outra fonte, como migrar do GitHub para o Azure DevOps.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como habilitar e configurar o Servidor de Configuração. Para saber mais sobre como gerenciar seu aplicativo, passe para o tutorial para dimensionar manualmente seu aplicativo.

> [!div class="nextstepaction"]
> [Saiba como dimensionar manualmente seu aplicativo Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

