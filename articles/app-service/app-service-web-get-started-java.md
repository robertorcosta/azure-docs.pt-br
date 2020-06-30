---
title: 'Início Rápido: Criar um aplicativo Java no Windows'
description: Implante seu primeiro Olá, Mundo em Java no Serviço de Aplicativo do Azure no Windows em minutos. O Plug-in de Aplicativo Web do Azure para Maven torna-o conveniente para implantar aplicativos Java.
keywords: azure, serviço de aplicativo, aplicativo Web, windows, java, maven, início rápido
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d8f03d714ab44dc01d9e138a63a89892ead60fe9
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85249458"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Início Rápido: Criar um aplicativo Java no Serviço de Aplicativo do Azure no Windows

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar o Serviço de Aplicativo no _Linux_, confira [Criar um aplicativo Web Java no Linux](./containers/quickstart-java.md).
>

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este início rápido mostra como usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) com o [Plug-in de Aplicativo Web do Azure para Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implantar um arquivo WAR (Arquivo Web) Java.

> [!NOTE]
> A mesma coisa também pode ser feita usando IDEs populares, como o IntelliJ e Eclipse. Confira nossos documentos semelhantes em [Início Rápido: Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) ou [Início Rápido: Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Criar um aplicativo Java

Execute o seguinte comando do Maven no prompt do Cloud Shell para criar um aplicativo chamado `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Em seguida, altere o diretório de trabalho para a pasta do projeto:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Configurar o plug-in do Maven

O processo de implantação no Serviço de Aplicativo do Azure pode extrair suas credenciais da CLI do Azure automaticamente. Se você ainda não tiver a CLI do Azure instalada, o plug-in do Maven conectará você com o Oauth ou com o logon do dispositivo. Verifique os detalhes em [Autenticação com plug-ins do Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), se necessário.

Você pode executar o comando Maven a seguir no prompt de comando para configurar a implantação, escolher **'2'** para o sistema operacional **Windows** na primeira etapa e, em seguida, aceitar as configurações padrão pressionando **ENTER** até aparecer o prompt **Confirmar (S/N)** , em seguida, pressione **'S'** e a configuração é feita. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

Um processo de exemplo é semelhante a este:

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```

> [!NOTE]
> Neste artigo, estamos trabalhando apenas com aplicativos Java empacotados em arquivos WAR. O plug-in também oferece suporte a aplicativos Web JAR, visite [Implantar um arquivo JAR do Java SE para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para experimentá-lo.

Abra em `pom.xml` para ver a configuração atualizada.

```bash
code pom.xml
```

Você poderá modificar as configurações do Serviço de Aplicativo diretamente em seu arquivo pom, se necessário. Algumas comuns estão listadas abaixo:

 Propriedade | Obrigatório | Descrição | Versão
---|---|---|---
`<schemaVersion>` | false | Especifique a versão do esquema de configuração. Os valores suportados são: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Grupo de recursos do Azure para seu aplicativo Web. | 0.1.0+
`<appName>` | true | O nome do seu aplicativo Web. | 0.1.0+
`<region>` | true | Especifica a região onde seu aplicativo Web será hospedado; o valor padrão é **westeurope**. Todas as regiões válidas na seção [Regiões com suporte](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<pricingTier>` | false | O tipo de preço do seu aplicativo Web. O valor padrão é **P1V2**.| 0.1.0+
`<runtime>` | true | A configuração do ambiente de runtime. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | A configuração de implantação. Você pode ver os detalhes [aqui](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Implantar o aplicativo

O processo de implantação no Serviço de Aplicativo do Azure usa credenciais de conta da CLI do Azure. [Entre com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```
Em seguida, você poderá implantar seu aplicativo Java no Azure usando o seguinte comando:

```bash
mvn package azure-webapp:deploy
```

Após a conclusão da implantação, navegue até o aplicativo implantado usando a URL a seguir no navegador da Web, por exemplo, `http://<webapp>.azurewebsites.net/`.

![Aplicativo de exemplo em execução no Serviço de Aplicativo do Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Parabéns!** Você implantou seu primeiro aplicativo Java no Serviço de Aplicativo no Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Conectar-se ao Banco de Dados SQL do Azure com Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Conectar-se ao BD do Azure para MySQL com Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Conectar-se ao BD do Azure para PostgreSQL com Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Recursos do Azure para desenvolvedores Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre os plug-ins do Maven para o Azure](https://github.com/microsoft/azure-maven-plugins)
