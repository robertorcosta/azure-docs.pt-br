---
title: Configurar aplicativos PHP
description: Saiba como configurar um contêiner PHP pré-construído para o seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: ad121d605e521704597471b446fa79cb43dfccc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255844"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configure um aplicativo Linux PHP para o Azure App Service

Este guia mostra como configurar o tempo de execução php incorporado para aplicativos web, back ends móveis e aplicativos de API no Azure App Service.

Este guia fornece conceitos e instruções importantes para desenvolvedores PHP que usam um contêiner Linux incorporado no App Service. Se você nunca usou o Azure App Service, siga o [php quickstart](quickstart-php.md) e [PHP com tutorial MySQL](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar versão PHP

Para mostrar a versão PHP atual, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões PHP suportadas, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir a versão do PHP

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão PHP como 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Personalize a automação de compilação

Se você implantar seu aplicativo usando pacotes Git ou zip com automação de compilação ligada, o App Service construirá etapas de automação através da seguinte seqüência:

1. Executar o script personalizado, se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `php composer.phar install`.
1. Executar o script personalizado, se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis de ambiente que estão vazias por padrão. Para executar comandos de `PRE_BUILD_COMMAND`pré-construção, defina . Para executar comandos pós-compilação, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por commas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis adicionais de ambiente para personalizar a automação de construção, consulte [a configuração Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service executa e constrói aplicativos PHP no Linux, consulte [a documentação oryx: como os aplicativos PHP são detectados e construídos](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Personalizar a inicialização

Por padrão, o contêiner PHP incorporado executa o servidor Apache. Na partida, ele `apache2ctl -D FOREGROUND"`funciona. Se você quiser, você pode executar um comando diferente na inicialinicialidade, executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `DB_HOST`, use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura web de sua escolha pode usar um subdiretório como raiz do site. Por exemplo, [Laravel](https://laravel.com/) `public/` , usa o subdiretório como raiz do site.

A imagem PHP padrão para serviço de aplicativo usa apache, e não permite que você personalize a raiz do site para o seu aplicativo. Para contornar essa limitação, adicione um arquivo *.htaccess* à sua raiz de repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
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

## <a name="customize-phpini-settings"></a>Personalizar configurações php.ini

Se você precisar fazer alterações na instalação do PHP, você pode alterar qualquer uma das [diretivas php.ini seguindo estas etapas.](https://www.php.net/manual/ini.list.php)

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração atual *php.ini* é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar diretivas não-PHP_INI_SYSTEM

Para personalizar PHP_INI_USER, PHP_INI_PERDIR e diretivas PHP_INI_ALL (consulte [diretivas php.ini),](https://www.php.net/manual/ini.list.php)adicione um arquivo *.htaccess* ao diretório raiz do seu aplicativo.

No arquivo *.htaccess,* adicione as `php_value <directive-name> <value>` diretivas usando a sintaxe. Por exemplo: 

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante seu aplicativo com as alterações e reinicie-o. Se você implantá-lo com Kudu (por exemplo, usando [o Git),](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ele será reiniciado automaticamente após a implantação.

Como alternativa ao uso *do .htaccess,* você pode usar [ini_set()](https://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas não PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalize as diretrizes de PHP_INI_SYSTEM

Para personalizar PHP_INI_SYSTEM diretivas (consulte [diretivas php.ini),](https://www.php.net/manual/ini.list.php)você não pode usar a abordagem *.htaccess.* O App Service fornece `PHP_INI_SCAN_DIR` um mecanismo separado usando a configuração do aplicativo.

Primeiro, execute o seguinte comando no [Cloud](https://shell.azure.com) Shell `PHP_INI_SCAN_DIR`para adicionar uma configuração de aplicativo chamada :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`é o diretório padrão onde *php.ini* existe. `/home/site/ini`é o diretório personalizado no qual você adicionará um arquivo *personalizado .ini.* Você separa os `:`valores com um.

Navegue até a sessão Web SSH com seu contêiner Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Crie um diretório `/home/site` `ini`chamado, em seguida, crie `/home/site/ini` um arquivo *.ini* no diretório (por exemplo, *settings.ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um arquivo *php.ini.* 

> [!TIP]
> Nos contêineres Linux incorporados no App Service, */home* é usado como armazenamento compartilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) executar os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="enable-php-extensions"></a>Habilitar extensões PHP

As instalações PHP incorporadas contêm as extensões mais utilizadas. Você pode habilitar extensões adicionais da mesma forma que [personaliza as diretivas php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração atual *php.ini* é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, siga estas etapas:

Adicione `bin` um diretório ao diretório raiz do seu `.so` aplicativo e coloque os arquivos de extensão nele (por exemplo, *mongodb.so*). Certifique-se de que as extensões são compatíveis com a versão PHP no Azure e são compatíveis com VC9 e não-thread-safe (nts).

Implante suas mudanças.

Siga as etapas em [Personalizar PHP_INI_SYSTEM diretivas,](#customize-php_ini_system-directives)adicione as extensões ao arquivo *personalizado .ini* com as diretivas [de extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

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

Quando um aplicativo PHP em funcionamento se comportar de forma diferente no App Service ou tiver erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O App Service executa seus aplicativos Node.js no modo de produção, então você precisa ter certeza de que seu projeto funciona como esperado no modo de produção localmente. Por exemplo: 
    - Dependendo do seu *compositor.json,* diferentes pacotes podem`require` ser `require-dev`instalados para o modo de produção (vs. ).
    - Certos frameworks da Web podem implantar arquivos estáticos de forma diferente no modo de produção.
    - Certos frameworks da Web podem usar scripts de inicialização personalizados ao ser executados no modo de produção.
- Execute seu aplicativo no App Service no modo de depuração. Por exemplo, em [Laravel,](https://meanjs.org/)você pode configurar seu aplicativo para depurar mensagens de depuração na [ `APP_DEBUG` produção, definindo a configuração do aplicativo para `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: aplicativo PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)
