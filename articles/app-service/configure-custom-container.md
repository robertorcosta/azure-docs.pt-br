---
title: Configurar um contêiner personalizado do Linux
description: Saiba como configurar um contêiner personalizado do Linux no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 2f26f1b041b2d369b68aeb11755c8e8053862b16
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082892"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurar um contêiner personalizado do Linux para o serviço Azure App

Este artigo mostra como configurar um contêiner personalizado do Linux para ser executado no serviço Azure App.

Este guia fornece os principais conceitos e instruções para a Containerização de aplicativos do Linux no serviço de aplicativo. Se você nunca usou Azure App serviço, siga primeiro o [início rápido](quickstart-custom-container.md?pivots=container-linux) e o [tutorial](tutorial-custom-container.md?pivots=container-linux) do contêiner personalizado. Há também um início rápido e [tutorial](tutorial-multi-container-app.md)de [aplicativo de vários contêineres](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Configurar número da porta

Por padrão, o serviço de aplicativo assume que seu contêiner personalizado está escutando na porta 80. O servidor Web em sua imagem personalizada pode usar uma porta diferente de 80. Você informa ao Azure sobre a porta que seu contêiner personalizado usa usando a `WEBSITES_PORT` configuração do aplicativo. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_. Você pode defini-lo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Seu contêiner personalizado pode usar variáveis de ambiente que precisam ser fornecidas externamente. Você pode passá-los no executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Esse método funciona tanto para aplicativos de contêiner único quanto para aplicativos de vários contêineres, onde as variáveis de ambiente são especificadas no arquivo *Docker-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Usar armazenamento compartilhado persistente

Você pode usar o diretório */Home* no sistema de arquivos do seu aplicativo para manter os arquivos entre as reinicializações e compartilhá-los entre instâncias. O `/home` em seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acesse o armazenamento persistente.

Quando o armazenamento persistente é desabilitado, as gravações no `/home` diretório não são mantidas entre as reinicializações do aplicativo ou entre várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar os logs do Docker e do contêiner. Quando o armazenamento persistente está habilitado, todas as gravações no `/home` diretório são persistidas e podem ser acessadas por todas as instâncias de um aplicativo expandido.

Por padrão, o armazenamento persistente é *habilitado* e a configuração não é exposta nas configurações do aplicativo. Para desabilitá-lo, defina a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração do aplicativo executando o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Você também pode [configurar seu próprio armazenamento persistente](configure-connect-to-azure-storage.md?pivots=platform-linux).

## <a name="enable-ssh"></a>Habilitar SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que um contêiner personalizado dê suporte ao SSH, você deve adicioná-lo no próprio Dockerfile.

> [!TIP]
> Todos os contêineres internos do Linux adicionaram as instruções de SSH em seus repositórios de imagem. Você pode percorrer as instruções a seguir com o [repositórioNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como ele está habilitado lá.

- Use a instrução [Run](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH e definir a senha da conta raiz como `"Docker!"` . Por exemplo, para uma imagem baseada em [Alpine Linux](https://hub.docker.com/_/alpine), você precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas por meio `https://<app-name>.scm.azurewebsites.net` de e autenticado com as credenciais de publicação.

- Adicione [este arquivo de sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagens e use a instrução de [cópia](https://docs.docker.com/engine/reference/builder/#copy) para copiar o arquivo para o diretório */etc/ssh/* Para obter mais informações sobre *sshd_config* arquivos, consulte a [documentação do OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens:
    > - `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

- Use a instrução de [exposição](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no contêiner. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada pela Internet. Ele é acessível somente por contêineres dentro da rede de ponte de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de inicialização do seu contêiner, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Para obter um exemplo, consulte como o [contêiner padrãoNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>Alterar a imagem do Docker de um contêiner personalizado

Para alterar um aplicativo de contêiner personalizado existente da imagem do Docker atual para uma nova imagem, use o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Usar uma imagem de um registro particular

Para usar uma imagem de um registro privado, como o registro de contêiner do Azure, execute o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Para o *\<username>* e *\<password>* o, forneça as credenciais de logon para sua conta de registro particular.

## <a name="configure-multi-container-apps"></a>Configurar aplicativos de vários contêineres

- [Usar armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de visualização](#preview-limitations)
- [Opções de Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usar armazenamento persistente no Docker Compose

Aplicativos de vários contêineres, como o WordPress, precisam de armazenamento persistente para funcionar corretamente. Para habilitá-lo, sua configuração de Docker Compose deve apontar para um local de armazenamento *fora* do contêiner. Os locais de armazenamento dentro de seu contêiner não mantêm as alterações além da reinicialização do aplicativo.

Habilite o armazenamento persistente definindo a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` configuração do aplicativo, usando o comando [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No arquivo *Docker-Compose. yml* , mapeie a `volumes` opção para `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicativo mapeada para armazenamento persistente para o aplicativo. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitações de visualização

No momento, vários contêineres estão em versão prévia. Os seguintes recursos da plataforma do serviço de aplicativo não têm suporte:

- Autenticação/Autorização
- Identidades gerenciadas

### <a name="docker-compose-options"></a>Opções de Docker Compose

As listas a seguir mostram opções de configuração com e sem suporte Docker Compose:

#### <a name="supported-options"></a>Opções com suporte

- .
- entrypoint
- ambiente
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções sem suporte

- build (não permitido)
- depends_on (ignorado)
- networks (ignorada)
- secrets (ignorados)
- portas diferentes de 80 e 8080 (ignoradas)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na visualização pública.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantação por meio de um repositório de contêiner particular](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)
