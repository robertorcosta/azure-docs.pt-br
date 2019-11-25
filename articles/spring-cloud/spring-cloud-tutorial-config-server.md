---
title: Configurar seu Servidor de Configuração no Azure Spring Cloud | Microsoft Docs
description: Neste tutorial, você aprenderá como configurar um Servidor de Configuração do Spring Cloud para seu Azure Spring Cloud no portal do Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 6742e1a5924fdcd1fe00f49ac790209a907d1bac
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132781"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Tutorial: Configurar um Servidor de Configuração do Spring Cloud para seu serviço

Este tutorial mostrará como conectar um Servidor de Configuração do Spring Cloud ao seu serviço do Azure Spring Cloud.

A configuração do Spring Cloud oferece suporte do servidor e do cliente para configuração externalizada em um sistema distribuído. Com o Servidor de Configuração, você tem um local central para gerenciar propriedades externas para aplicativos em todos os ambientes. Para saber mais, acesse a [Referência ao Servidor de Configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço do Azure Spring Cloud já provisionado e em execução.  Conclua [este início rápido](spring-cloud-quickstart-launch-app-cli.md) para provisionar e iniciar um serviço do Azure Spring Cloud.

## <a name="restriction"></a>Restrição

Há algumas restrições quando você usa o __Servidor de Configuração__ com um back-end do git. Algumas propriedades serão automaticamente injetadas em seu ambiente de aplicativo para acessar o __Servidor de Configuração__ e a __Descoberta de Serviço__. Se você também configurar essas propriedades em seus arquivos do **Servidor de Configuração**, poderá enfrentar conflitos e um comportamento inesperado. Essas propriedades incluem: 

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

O Azure Spring Cloud dá suporte ao Azure DevOps, GitHub, GitLab e Bitbucket para armazenar seus arquivos do Servidor de Configuração. Quando seu repositório estiver pronto, crie os arquivos de configuração com as instruções abaixo e armazene-os lá.

Além disso, algumas propriedades configuráveis só estão disponíveis para alguns tipos. As subseções a seguir listam as propriedades de cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Ao usar um repositório público, suas propriedades configuráveis serão mais limitadas.

Todas as propriedades configuráveis usadas para configurar o repositório público `Git` estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, você pode usar `default-label`, mas não `defaultLabel`.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório `Git` deve ser o `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis usadas para configurar o repositório `Git` privado com `Ssh` estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, você pode usar `default-label`, mas não `defaultLabel`.

| Propriedade                   | Obrigatório | Recurso                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label`            | `no`     | O rótulo padrão do repositório `Git` deve ser o `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`             | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `private-key`              | `no`     | A chave privada `Ssh` para acessar o repositório `Git`, __obrigatória__ quando `uri` é iniciado com `git@` ou `ssh://`. |
| `host-key`                 | `no`     | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | O algoritmo de chave de host deve ser `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` ou `ecdsa-sha2-nistp521`. Obrigatório somente se `host-key` existir. |
| `strict-host-key-checking` | `no`     | Indica se a inicialização do servidor de configuração falhará ao fazer uso do `host-key` privado. Deve ser `true` (valor padrão) ou `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação Básica

Todas as propriedades configuráveis usadas para configurar o repositório Git privado com autenticação Básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, use `default-label` e não `defaultLabel`.

| Propriedade        | Obrigatório | Recurso                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório `Git` deve ser o `branch name`, `tag name` ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `username`      | `no`     | O `username` usado para acessar o servidor do repositório `Git`, __obrigatório__ quando o servidor do repositório `Git` dá suporte a `Http Basic Authentication`. |
| `password`      | `no`     | A senha usada para acessar o servidor do repositório `Git`, __obrigatória__ quando o servidor do repositório `Git` dá suporte a `Http Basic Authentication`. |

> [!NOTE]
> Muitos servidores de repositório `Git` dão suporte ao uso de tokens em vez de senhas para `HTTP Basic Authentication`. Alguns repositórios, como o GitHub, permitem que os tokens persistam indefinidamente. No entanto, alguns servidores de repositório Git, incluindo o Azure DevOps, forçam tokens a expirar em algumas horas. Os repositórios que fazem com que os tokens expirem não devem usar a autenticação baseada em token com o Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios Git com padrão

Todas as propriedades configuráveis usadas para configurar os repositórios Git com padrão estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única convenção de nomenclatura compatível no momento. Por exemplo, use `default-label` e não `defaultLabel`.

| Propriedade                           | Obrigatório         | Recurso                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Um mapa que consiste nas configurações de um repositório `Git` com um determinado nome. |
| `repos."uri"`                      | `yes` em `repos` | O `uri` do repositório `Git` usado como back-end de servidor de configuração. Deve ser começado com `http://`, `https://`, `git@` ou `ssh://`. |
| `repos."name"`                     | `yes` em `repos` | Um nome para identificação de um repositório `Git`, __obrigatório__ somente se `repos` existir. Pelo exemplo acima, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | Uma matriz de cadeias de caracteres usada para corresponder a um nome de aplicativo. Para cada padrão, use o formato `{application}/{profile}` com caracteres curinga. |
| `repos."default-label"`            | `no`             | O rótulo padrão do repositório `Git` deve ser o `branch name`, `tag name` ou `commit-id` do repositório. |
| `repos."search-paths`"             | `no`             | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório `Git`. |
| `repos."username"`                 | `no`             | O `username` usado para acessar o servidor do repositório `Git`, __obrigatório__ quando o servidor do repositório `Git` dá suporte a `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | A senha usada para acessar o servidor do repositório `Git`, __obrigatória__ quando o servidor do repositório `Git` dá suporte a `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | A chave privada `Ssh` para acessar o repositório `Git`, __obrigatória__ quando `uri` é iniciado com `git@` ou `ssh://`. |
| `repos."host-key"`                 | `no`             | A chave de host do servidor do repositório Git. Não deve incluir o prefixo de algoritmo, conforme abordado por `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | O algoritmo de chave de host deve ser `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` ou `ecdsa-sha2-nistp521`. Obrigatório __somente__ se `host-key` existir. |
| `repos."strict-host-key-checking"` | `no`             | Indica se a inicialização do servidor de configuração falhará ao fazer uso do `host-key` privado. Deve ser `true` (valor padrão) ou `false`. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Anexar o repositório do Servidor de Configuração ao Azure Spring Cloud

Agora que você salvou seus arquivos de configuração em um repositório, é necessário conectar o Azure Spring Cloud a ele.

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Navegue até a página **Visão geral** do Azure Spring Cloud.

1. Escolha o serviço a ser configurado.

1. Na página do serviço, escolha a guia **Servidor de Configuração** no título **Configurações** no menu do lado esquerdo.

![captura de tela da janela](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Fornecer informações do repositório diretamente para o portal do Azure

#### <a name="default-repository"></a>Repositório padrão

* Repositório público: Na seção **Repositório padrão**, cole o URI do repositório na seção **URI**.  Defina o **Rótulo** como `config`. Verifique se a configuração **Autenticação** está marcada como **Pública** e, em seguida, selecione **Aplicar** para concluir. 

* Repositório privado: O Azure Spring Cloud dá suporte a SSH e à autenticação básica baseada em token e senha.

    * Autenticação Básica: Na seção **Repositório padrão**, cole o URI do repositório na seção **URI** e clique em **Autenticação**. Selecione **Básica** como seu **Tipo de autenticação** e insira seu nome de usuário e senha/token para permitir acesso ao Azure Spring Cloud. Clique em **OK** e **Aplicar** para concluir a configuração do seu Servidor de Configuração.

    ![captura de tela da janela](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alguns servidores de repositório Git, tais como o GitHub, usam um `personal-token` ou um `access-token` como uma senha para **Autenticação Básica**. Você pode usar esse tipo de token como uma senha no Azure Spring Cloud, pois ele nunca expirará. Mas para outros servidores de repositório Git, tais como o BitBucket e o Azure DevOps, o `access-token` expirará em uma ou duas horas. Isso significa que essa opção não é viável ao usar esses servidores de repositório com o Azure Spring Cloud.

    * SSH: Na seção **Repositório padrão**, cole o URI do repositório na seção **URI** e clique em **Autenticação**. Selecione **SSH** como seu **Tipo de autenticação** e insira sua **Chave privada**. Opcionalmente, você pode especificar sua **Chave de host** e seu **Algoritmo de chave de host**. Não deixe de incluir sua chave pública no repositório do servidor de configuração. Clique em **OK** e **Aplicar** para concluir a configuração do seu Servidor de Configuração.

    ![captura de tela da janela](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repositório de padrões

Se você quiser usar um **Repositório de padrões** opcional para configurar seu serviço, especifique o **URI** e a **Autenticação** da mesma maneira que o **Repositório padrão**. Não deixe de incluir um **Nome** para seu padrão e, em seguida, clique em **Aplicar** para anexá-lo à sua instância. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Inserir informações do repositório em um arquivo YAML

Se você tiver escrito um arquivo YAML com as configurações do repositório, poderá importar esse arquivo diretamente do computador local para o Azure Spring Cloud. Um arquivo YAML simples para um repositório privado com autenticação Básica ficaria assim:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Clique no botão **Importar configurações** e selecione o arquivo `.yml` do diretório do projeto. Clique em **Importar** e, em seguida, uma operação `async` de suas **Notificações** será exibida. Após um a dois minutos, ela deve relatar êxito.

![captura de tela da janela](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Você deve ver as informações do arquivo YAML exibidas no portal do Azure. Clique em **Aplicar** para concluir. 


## <a name="delete-your-app-configuration"></a>Excluir a configuração de aplicativos

Após salvar um arquivo de configuração, o botão **Excluir configuração de aplicativos** será exibido na guia **Configuração**. Isso apagará suas configurações existentes completamente. Você deverá fazer isso se desejar conectar seu servidor de configuração a outra fonte, como migrar do GitHub para o Azure DevOps.



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como habilitar e configurar o Servidor de Configuração. Para saber mais sobre como gerenciar seu aplicativo, passe para o tutorial para dimensionar manualmente seu aplicativo.

> [!div class="nextstepaction"]
> [Saiba como dimensionar manualmente seu aplicativo Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

