---
title: 'Tutorial: Usar o IntelliJ para implantar aplicativos do Azure Spring Cloud'
description: Usar o IntelliJ para implantar aplicativos ao Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: ec78b62f15851c41a3b9759f32d9c35c189f72c4
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142030"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Usar o IntelliJ para implantar aplicativos do Azure Spring Cloud
O plug-in IntelliJ para o Azure Spring Cloud é compatível com a implantação de aplicativos da IntelliJ IDEA.  

## <a name="prerequisites"></a>Pré-requisitos
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA, Community/Ultimate Edition, versão 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Instalar o plug-in
Você pode adicionar o Azure Toolkit for IntelliJ IDEA 3.35.0 da interface do usuário **Plug-ins** do IntelliJ.

1. Inicie o IntelliJ.  Se você tiver aberto um projeto anteriormente, feche-o para mostrar a caixa de diálogo de boas-vindas. Selecione **Configurar** na parte inferior direita do link e, em seguida, clique em **Plug-ins** para abrir a caixa de diálogo configuração de plug-in e selecione **Instalar Plug-ins do disco**.

    ![Selecionar Configurar](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Pesquise o Azure Toolkit for IntelliJ.  Clique em **Instalar**.

    ![Instalar o plug-in](media/spring-cloud-intellij-howto/install-plugin.png)

1. Clique em **Reiniciar IDE**.

## <a name="tutorial-procedures"></a>Procedimentos do tutorial
Os procedimentos a seguir implantam um aplicativo Olá, Mundo usando o IntelliJ IDEA.

* Abrir o projeto gs-spring-boot
* Implantar no Azure Spring Cloud
* Mostrar logs de streaming

## <a name="open-gs-spring-boot-project"></a>Abrir o projeto gs-spring-boot

1. Baixe e descompacte o repositório de origem para este tutorial ou clone-lo usando Git: git clone https://github.com/spring-guides/gs-spring-boot.git 
1. cd into gs-spring-boot\complete.
1. Abra a caixa de diálogo **Bem-vindo(a)** do IntelliJ e selecione **Importar Projeto** para abrir o assistente de importação.
1. Selecione a pasta `gs-spring-boot\complete`.

    ![Importar projeto](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Implantar no Azure Spring Cloud
Para implantar no Azure, você deve entrar com sua conta do Azure e escolher sua assinatura.  Para obter detalhes de entrada, confira [Instalação e entrada](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Clique com o botão direito do mouse no seu projeto no explorador de projeto do IntelliJ e selecione **Azure** -> **Implantar no Azure Spring Cloud**.

    ![Implantar no Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Aceite o nome do aplicativo no campo **Nome**. **Nome** refere-se à configuração, não ao nome do aplicativo. Normalmente, os usuários não precisam alterá-lo.
1. Aceite o identificador do projeto para o **Artefato**.
1. Selecione **Aplicativo:** e clique em **Criar aplicativo...** .

    ![Implantar no Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Insira **Nome do aplicativo** e clique em **OK**.

    ![Implantar no Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Inicie a implantação clicando no botão **Executar**. 

    ![Implantar no Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. O plug-in executará o comando `mvn package` no projeto e, em seguida, criará o novo aplicativo e implantará o jar gerado pelo comando `package`.

1. Se a URL do aplicativo não for mostrada na janela de saída, obtenha-a do portal do Azure. Navegue do seu grupo de recursos para a instância do Azure Spring Cloud.  Em seguida, clique em **Aplicativos**.  O aplicativo em execução será listado.

    ![Obter URL de teste](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navegue até a URL em um navegador.

    ![Navegar no Navegador 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Mostrar logs de streaming
Para obter os logs:
1. Selecione **Azure Explorer** e, em seguida, **Spring Cloud**.
1. Clique com o botão direito do mouse no aplicativo em execução.
1. Selecione **Logs de Streaming** na lista suspensa.

    ![Selecionar os logs de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecione a instância.

    ![Selecionar instância](media/spring-cloud-intellij-howto/select-instance.png)

1. O log de streaming ficará visível na janela de saída.

    ![Saída do log de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Próximas etapas
* [Preparar o aplicativo Spring para o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Saiba mais sobre o Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
