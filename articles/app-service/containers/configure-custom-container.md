---
title: Configure um contêiner Linux personalizado
description: Saiba como configurar um contêiner Linux personalizado no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280138"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configure um contêiner Linux personalizado para o Azure App Service

Este artigo mostra como configurar um contêiner Linux personalizado para ser executado no Azure App Service.

Este guia fornece conceitos-chave e instruções para a contêinerização de aplicativos Linux no App Service. Se você nunca usou o Azure App Service, siga o quickstart e [o tutorial](tutorial-custom-docker-image.md) [personalizados](quickstart-docker-go.md) do contêiner primeiro. Há também um [aplicativo de vários contêineres quickstart](quickstart-multi-container.md) e [tutorial](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Configurar o número da porta

O servidor web em sua imagem personalizada pode usar uma porta diferente de 80. Você conta ao Azure sobre a porta `WEBSITES_PORT` que seu contêiner personalizado usa usando a configuração do aplicativo. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_. Você pode configurá-lo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Seu recipiente personalizado pode usar variáveis de ambiente que precisam ser fornecidas externamente. Você pode passá-los executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Este método funciona tanto para aplicativos de contêiner único quanto para aplicativos de vários contêineres, onde as variáveis de ambiente são especificadas no arquivo *docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Use armazenamento compartilhado persistente

Você pode usar o *diretório /home* no sistema de arquivos do aplicativo para persistir arquivos em reinicializações e compartilhá-los entre instâncias. O `/home` aplicativo em seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acesse o armazenamento persistente.

Quando o armazenamento persistente é `/home` desativado, então as gravações no diretório não são persistidas em reinicializações de aplicativos ou em várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar os registros docker e contêiner. Quando o armazenamento persistente é ativado, todas as gravações no `/home` diretório são persistidas e podem ser acessadas por todas as instâncias de um aplicativo de saída dimensionada.

Por padrão, o armazenamento persistente está *ativado* e a configuração não está exposta nas Configurações do aplicativo. Para desativá-lo, defina a configuração do `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicativo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando no Cloud Shell. Por exemplo: 

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Você também pode [configurar seu próprio armazenamento persistente.](how-to-serve-content-from-azure-storage.md)

## <a name="enable-ssh"></a>Habilitar SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que um contêiner personalizado suporte ssh, você deve adicioná-lo ao próprio Arquivo Docker.

> [!TIP]
> Todos os contêineres Linux incorporados adicionaram as instruções SSH em seus repositórios de imagem. Você pode passar pelas seguintes instruções com o [repositório Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como ele está ativado lá.

- Use a instrução [RUN](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH `"Docker!"`e defina a senha da conta raiz para . Por exemplo, para uma imagem baseada no [Alpine Linux,](https://hub.docker.com/_/alpine)você precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Esta configuração não permite conexões externas ao recipiente. O SSH está `https://<app-name>.scm.azurewebsites.net` disponível apenas através e autenticado com as credenciais de publicação.

- Adicione [este arquivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagem e use a instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) para copiar o arquivo para o diretório */etc/ssh..* Para obter mais informações sobre *arquivos sshd_config,* consulte [a documentação OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens:
    > - `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

- Use a instrução [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no contêiner. Embora a senha raiz seja conhecida, a porta 2222 está inacessível a partir da internet. É acessível apenas por contêineres dentro da rede de ponte de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de inicialinicializar o contêiner, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como o [contêiner Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) padrão inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurar aplicativos de vários contêineres

- [Use armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de visualização](#preview-limitations)
- [Opções de Composição do Docker](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Use armazenamento persistente no Docker Compose

Aplicativos de vários contêineres como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para habilitá-lo, a configuração Docker Compose deve apontar para um local de armazenamento *fora* do contêiner. Os locais de armazenamento dentro do contêiner não persistem alterações além da reinicialização do aplicativo.

Habilite o `WEBSITES_ENABLE_APP_SERVICE_STORAGE` armazenamento persistente definindo a configuração do aplicativo, usando o comando de configuração de [configuração de configuração de configuração](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) de configuração do webapp az no Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu arquivo *docker-compose.yml,* mapeie a `volumes` opção para `${WEBAPP_STORAGE_HOME}`. 

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

O multicontêiner está atualmente em pré-visualização. Os seguintes recursos da plataforma App Service não são suportados:

- Autenticação/Autorização
- Identidades gerenciadas

### <a name="docker-compose-options"></a>Opções de Composição do Docker

As seguintes listas mostram opções de configuração do Docker Compose suportadas e sem suporte:

#### <a name="supported-options"></a>Opções com suporte

- command
- entrypoint
- environment
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
> Quaisquer outras opções não explicitamente chamadas são ignoradas na Visualização Pública.

## <a name="configure-vnet-integration"></a>Configure a integração VNet

O uso de um contêiner personalizado com integração VNet pode exigir uma configuração adicional de contêiner. Consulte [Integrar seu aplicativo a uma Rede Virtual do Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantar a partir de um repositório de contêineres privados](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress multi-contêiner](tutorial-multi-container-app.md)
