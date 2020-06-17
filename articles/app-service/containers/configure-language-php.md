---
title: Configurar aplicativos de PHP
description: Saiba como configurar um contêiner pré-criado de PHP para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9933205095587d9e8e0d8a5641d213f159512450
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234948"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurar um aplicativo de PHP do Linux para o Serviço de Aplicativo do Azure

Este guia mostra como configurar o runtime do PHP interno para aplicativos Web, back-ends móveis e aplicativos de API no Serviço de Aplicativo do Azure.

Este guia fornece conceitos e instruções essenciais para desenvolvedores de PHP que usam um contêiner interno do Linux no Serviço de Aplicativo. Se você nunca usou o Serviço de Aplicativo do Azure, siga o [Início rápido do PHP](quickstart-php.md) e o [tutorial de PHP com MySQL](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar a versão do PHP

Para mostrar a versão atual do PHP, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do PHP compatíveis, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir a versão do PHP

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do PHP como 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Personalizar a automação de build

Se você implantar seu aplicativo usando pacotes Git ou zip com a automação de build ativada, a automação de build do Serviço de Aplicativo percorrerá a seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `php composer.phar install`.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis de ambiente vazias por padrão. Para executar comandos pré-build, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-build, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis de ambiente adicionais para personalizar a automação de build, confira [Configuração do Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o Serviço de Aplicativo executa e compila aplicativos de PHP no Linux, confira a [Documentação do Oryx: Como aplicativos PHP são detectados e compilados](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Personalizar a inicialização

Por padrão, o contêiner de PHP interno executa o servidor Apache. Na inicialização, ele executa `apache2ctl -D FOREGROUND"`. Se desejar, você poderá executar um comando diferente na inicialização. Para isso, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do código do aplicativo. Em seguida, pode acessá-las usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php). Por exemplo, para acessar uma configuração de aplicativo chamada `DB_HOST`, use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura da Web de sua escolha pode usar um subdiretório como a raiz do site. Por exemplo, a [Laravel](https://laravel.com/), usa o subdiretório `public/` como raiz do site.

A imagem do PHP padrão para o Serviço de Aplicativo usa o Apache e não permite a personalização da raiz do site para seu aplicativo. Para contornar essa limitação, adicione um arquivo *.htaccess* à raiz do repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se você preferir não usar a regeneração de *.htaccess*, implante seu aplicativo Laravel com uma [imagem personalizada do Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. Em [CodeIgniter](https://codeigniter.com/), o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por padrão.

## <a name="customize-phpini-settings"></a>Personalizar configurações do php.ini

Se você precisar fazer alterações na instalação do PHP, altere qualquer uma das [diretivas php.ini](https://www.php.net/manual/ini.list.php) seguindo estas etapas.

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração *php.ini* atual é chamar [phpinfo()](https://www.php.net/manual/function.phpinfo.php) em seu aplicativo.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar diretivas diferentes de PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (consulte [diretivas php.ini](https://www.php.net/manual/ini.list.php)), adicione um arquivo *.htaccess* ao diretório raiz do seu aplicativo.

No arquivo *.htaccess*, adicione as diretivas usando a sintaxe `php_value <directive-name> <value>`. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante seu aplicativo com as alterações e reinicie-o. Se você implantá-lo com Kudu (por exemplo, usando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), ele será automaticamente reiniciado após a implantação.

Como alternativa ao uso do *.htaccess*, você pode usar [ini_set()](https://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas que não são PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar diretivas PHP_INI_SYSTEM

Para personalizar diretivas PHP_INI_SYSTEM (consulte [diretivas php.ini](https://www.php.net/manual/ini.list.php)), você não pode usar a abordagem do *.htaccess*. O Serviço de Aplicativo oferece um mecanismo separado usando a configuração `PHP_INI_SCAN_DIR` do aplicativo.

Primeiro, execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para adicionar uma configuração de aplicativo chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` é o diretório padrão em que o *php.ini* existe. `/home/site/ini` é o diretório personalizado no qual você adicionará um arquivo *.ini* personalizado. Separe os valores com um `:`.

Navegue até a sessão SSH da Web com seu contêiner do Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Crie um diretório em `/home/site` chamado `ini` e, em seguida, crie um arquivo *.ini* no diretório `/home/site/ini` (por exemplo, *settings.ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um arquivo *php.ini*. 

> [!TIP]
> Nos contêineres internos do Linux no Serviço de Aplicativo, */home* é usado como armazenamento compartilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="enable-php-extensions"></a>Habilitar extensões PHP

As instalações internas do PHP contêm as extensões usadas com mais frequência. Você pode habilitar outras extensões da mesma forma que [personaliza as diretivas do php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração *php.ini* atual é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, siga estas etapas:

Adicione um diretório `bin` ao diretório raiz do seu aplicativo e coloque os arquivos de extensão `.so` nele (por exemplo, *mongodb.so*). Verifique se as extensões são compatíveis com a versão do PHP no Azure e também com nts (non-thread-safe) e VC9.

Implante suas alterações.

Siga as etapas em [Personalizar diretivas PHP_INI_SYSTEM](#customize-php_ini_system-directives) e adicione as extensões ao arquivo personalizado *.ini* com as diretivas [extension](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Solução de problemas

Quando um aplicativo PHP que esteja funcionando se comporta de maneira diferente no Serviço de Aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O Serviço de Aplicativo executa seus aplicativos do Node.js no modo de produção, portanto, verifique se seu projeto funciona como esperado localmente no modo de produção. Por exemplo:
    - Dependendo do *composer.json*, diferentes pacotes podem ser instalados para o modo de produção (`require` versus `require-dev`).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de maneira diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no Serviço de Aplicativo no modo de depuração. Por exemplo, no [Laravel](https://meanjs.org/), você pode configurar seu aplicativo para gerar mensagens de depuração em produção [definindo a configuração do aplicativo `APP_DEBUG` como `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo PHP com o MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)
