---
title: Instale o ambiente de dev/teste de t de&IBM no Azure | Microsoft Docs
description: Implantar o ibm Z Development and Test Environment (zD&T) na infra-estrutura da Azure Virtual Machine (VM) como um serviço (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025944"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Instale o ambiente de dev/teste IBM zD&T no Azure

Para criar um ambiente de desenvolvimento/teste para cargas de trabalho de mainframe em Sistemas IBM Z, você pode implantar o IBM Z Development and Test Environment (zD&T) na infra-estrutura Da Zure Virtual Machine (VM) como um serviço (IaaS).

Com zD&T, você pode aproveitar a economia de custos da plataforma x86 para seus ambientes de desenvolvimento e teste menos críticos e, em seguida, empurrar as atualizações de volta para um ambiente de produção do Sistema Z. Para obter mais informações, consulte as instruções de [instalação do IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

O Azure e o Azure Stack suportam as seguintes versões:

- zD&T Edição Pessoal
- zD&T Sysplex Paralelo
- zD&T Enterprise Edition

Todas as edições do zD&T são executadas apenas em sistemas X86 Linux, não no Windows Server. O Enterprise Edition é suportado no Red Hat Enterprise Linux (RHEL) ou no Ubuntu/Debian. As imagens RHEL e Debian VM estão disponíveis para o Azure.

Este artigo mostra como configurar o zD&T Enterprise Edition no Azure para que você possa usar o servidor web zD&T Enterprise Edition para criação e gerenciamento de ambientes. A instalação do zD&T não instala ambientes. Você deve criá-los separadamente como pacotes de instalação. Por exemplo, ADCD (Application Developers Controlled Distributions, distribuições controladas por desenvolvedores de aplicativos) são imagens de volume de ambientes de teste. Eles estão contidos em imagens zip na distribuição de mídia disponível pela IBM. Veja como [configurar um ambiente ADCD no Azure](demo.md).

Para obter mais informações, consulte a [visão geral do zD&T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) no Centro de Conhecimento da IBM.

Este artigo mostra como configurar z Development and Test Environment (zD&T) Enterprise Edition no Azure. Em seguida, você pode usar o servidor web zD&T Enterprise Edition para criar e gerenciar ambientes baseados em Z no Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A IBM permite que o ZD&T Enterprise Edition seja instalado apenas em ambientes de desenvolvimento/teste,*não* em ambientes de produção.

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Você precisa ter acesso à mídia, que está disponível apenas para clientes e parceiros da IBM. Para obter mais informações, entre em contato com o representante da IBM ou consulte as informações de contato no site [zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para o acesso aos ambientes. A maneira como você o cria depende de como você licencia o software da IBM:

     - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para acessar todas as partes do software. Você deve obter isso da IBM.

     - **O servidor de licenciamento baseado em software** exige que você configure um servidor centralizado para o gerenciamento das chaves de licenciamento. Este método é preferido e exige que você configure as chaves que você recebe da IBM no servidor de gerenciamento.

## <a name="create-the-base-image-and-connect"></a>Crie a imagem base e conecte

1. No portal Azure, [crie uma VM](/azure/virtual-machines/linux/quick-create-portal) com a configuração do sistema operacional desejada. Este artigo pressupõe um VM B4ms (com 4 vCPUs e 16 GB de memória) executando o Ubuntu 16.04.

2. Depois que a VM for criada, abra as portas de entrada 22 para SSH, 21 para FTP e 9443 para o servidor web.

3. Obtenha as credenciais SSH mostradas na lâmina **visão geral** da VM através do botão **Conectar.** Selecione a guia **SSH** e copie o comando ssh logon na área de transferência.

4. Faça logon em um [shell Bash](/azure/cloud-shell/quickstart) do seu PC local e cole o comando. Ele estará no formulário **ssh\<\>\@\<user\>id IP Address**. Quando solicitado para suas credenciais, insira-as para estabelecer uma conexão com seu diretório Home.

## <a name="copy-the-installation-file-to-the-server"></a>Copie o arquivo de instalação para o servidor

O arquivo de instalação do servidor web é **ZDT\_Install\_EE\_V12.0.0.1.tgz**. Está incluído na mídia fornecida pela IBM. Você deve carregar este arquivo para o seu Ubuntu VM.

1. A partir da linha de comando, digite o seguinte comando para ter certeza de que tudo está atualizado na imagem recém-criada:

    ```
    sudo apt-get update
    ```

2. Criar o diretório para instalar em:

    ```
    mkdir ZDT
    ```

3. Copie o arquivo da sua máquina local para a VM:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Este comando copia o arquivo de instalação para o diretório ZDT em seu diretório Home, que varia dependendo se seu cliente executa o Windows ou Linux.

## <a name="install-the-enterprise-edition"></a>Instale o Enterprise Edition

1. Vá para o diretório ZDT e descompacte o arquivo ZDT\_Install\_EE\_V12.0.0.1.tgz usando os seguintes comandos:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Execute o instalador:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecione **1** para instalar o Enterprise Server.

4. Pressione **Enter** e leia atentamente os contratos de licença. No final da licença, digite **Sim** para prosseguir.

5. Quando solicitado a alterar a senha para o usuário recém-criado, **ibmsys1**, use o comando **sudo passwd ibmsys1** e digite a nova senha.

6. Para verificar se a instalação foi bem sucedida, digite

    ```
    dpkg -l | grep zdtapp
    ```

7. Verifique se a saída contém a string **zdtapp 12.0.0.0**, indicando que o gás da embalagem foi instalado com sucesso

### <a name="starting-enterprise-edition"></a>Iniciando a Enterprise Edition

Tenha em mente que quando o servidor web é iniciado, ele é executado o ID do usuário zD&T que foi criado durante o processo de instalação.

1. Para iniciar o servidor web, use o ID do usuário raiz para executar o seguinte comando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copie a saída de URL pelo script, que se parece com:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Cole a URL em um navegador da Web para abrir o componente de gerenciamento para sua instalação zD&T.

## <a name="next-steps"></a>Próximas etapas

[Configure uma ADCD (Application Developers Controlled Distribution, distribuição controlada de desenvolvedores de aplicativos) no IBM zD&T v1](./demo.md)
