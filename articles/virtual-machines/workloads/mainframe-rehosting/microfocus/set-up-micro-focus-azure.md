---
title: Instale o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure | Microsoft Docs
description: Rehospede suas cargas de trabalho de mainframe ibm z/OS usando o ambiente de desenvolvimento e teste micro focus em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834562"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instale o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure

Este artigo mostra como configurar o [Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) e o Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) no Azure.

Uma carga de trabalho comum no Azure é um ambiente de desenvolvimento e teste. Este cenário é comum porque é tão econômico e fácil de implantar e derrubar. Com o Enterprise Server, a Micro Focus criou uma das maiores plataformas de rehospedagem de mainframe disponíveis. Você pode executar cargas de trabalho z/OS em uma plataforma x86 mais barata no Azure usando máquinas virtuais Windows ou Linux (VMs).

Essa configuração usa VMs do Azure executando a imagem do Windows Server 2016 do Azure Marketplace com o Microsoft SQL Server 2017 já instalado. Essa configuração também se aplica ao Azure Stack.

O ambiente de desenvolvimento correspondente para o Enterprise Server é o Enterprise Developer, que é executado no Microsoft Visual Studio 2017 ou posterior, Visual Studio Community (gratuito para download) ou Eclipse. Este artigo mostra como implantá-lo usando uma máquina virtual do Windows Server 2016 que vem com o Visual Studio 2017 ou posteriormente instalado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software Micro Focus e uma licença válida (ou licença de teste). Se você é um cliente micro focus existente, entre em contato com o representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

- Obtenha a documentação para [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Uma prática recomendada é configurar um túnel de rede virtual privada (VPN) ou uma caixa de salto para controlar o acesso às VMs do Azure.

## <a name="install-enterprise-server"></a>Instalar o Enterprise Server

1. Para obter melhor segurança e capacidade de gerenciamento, considere criar um novo grupo de recursos apenas para este projeto — por exemplo, **RGMicroFocusEntServer**. Use a primeira parte do nome no Azure para escolher o tipo de recurso para facilitar a locamento em uma lista.

2. Crie uma máquina virtual. No Azure Marketplace, selecione a máquina virtual e o sistema operacional que você deseja. Aqui está uma configuração recomendada:

    - **Servidor Corporativo**: Selecione ES2 v3 VM (com 2 vCPUs e memória de 16 GB) com o Windows Server 2016 e o SQL Server 2017 instalados. Esta imagem está disponível no Azure Marketplace. O Enterprise Server também pode usar o Banco de Dados SQL do Azure.

    - **Desenvolvedor Corporativo**: Selecione B2ms VM (com 2 vCPUs e 8 GB de memória) com Windows 10 e Visual Studio instalados. Esta imagem está disponível no Azure Marketplace.

3. Na seção **Básico,** digite seu nome de usuário e senha. Selecione a **Assinatura** e **Localização/Região** que você gostaria de usar para as VMs. Selecione **RGMicroFocusEntServer** para o Grupo de Recursos.

4. Coloque ambas as VMs na mesma rede virtual para que possam se comunicar entre si.

5. Aceite os padrões para o resto das configurações. Lembre-se do nome de usuário e senha que você cria para o administrador dessas VMs.

6. Quando as máquinas virtuais forem criadas, abra as portas de entrada 9003, 86 e 80 para HTTP e 3389 para RDP na máquina Enterprise Server e 3389 na máquina Desenvolvedor.

7. Para entrar na máquina virtual do Enterprise Server, no portal Azure, selecione o VM ES2 v3. Vá para a seção **Visão Geral** e selecione **Conectar** para iniciar uma sessão RDP. Faça login usando as credenciais criadas para a VM.

8. A partir da sessão RDP, carregue os dois arquivos a seguir. Como você está usando o Windows, você pode arrastar e soltar os arquivos na sessão RDP:

    - **es\_40.exe**, o arquivo de instalação do Enterprise Server.

    - **mflic**, o arquivo de licença correspondente — o Enterprise Server não será carregado sem ele.

9. Clique duas vezes no arquivo para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença do usuário final.

     ![Tela de configuração do micro focus enterprise server](media/01-enterprise-server.png)

     Quando a configuração estiver concluída, a seguinte mensagem será exibida:

     ![Tela de configuração do micro focus enterprise server](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Verificar atualizações

Após a instalação, certifique-se de verificar se há atualizações adicionais, uma vez que uma série de pré-requisitos como o Microsoft C++ Redistributable e .NET Framework são instalados juntamente com o Enterprise Server.

### <a name="upload-the-license"></a>Faça upload da licença

1. Inicie a Administração de Licença supérdia do Micro Focus.

2. Clique **em Iniciar a** \> administração de **licença**do Micro Focus **License Manager** \> e clique na guia **Instalar.** Escolha o tipo de formato de licença para carregar: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, em **arquivo de licença,** navegue até o arquivo **mflic** carregado anteriormente para a VM e selecione **Licenças de instalação**.

     ![Caixa de diálogo Micro Focus License Administration](media/03-enterprise-server.png)

3. Verifique se o Servidor Corporativo é carregado. Tente iniciar o site da Enterprise Server <http://localhost:86/> Administration a partir de um navegador usando essa URL . A página Administração do Servidor Corporativo é exibida como mostrado.

     ![Página de Administração de Servidores Corporativos](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instale o Enterprise Developer na máquina de desenvolvedores

1. Selecione o grupo de recursos criado anteriormente (por exemplo, **RGMicroFocusEntServer),** e selecione a imagem do desenvolvedor.

2. Para entrar na máquina virtual, vá até a seção **Visão Geral** e selecione **Conectar**. Este sinal inicia uma sessão de RDP. Faça login usando as credenciais criadas para a VM.

3. A partir da sessão RDP, carregue os dois arquivos a seguir (arraste e solte se quiser):

    - **edvs2017.exe**, o arquivo de instalação do Enterprise Server.

    - **mflic**, o arquivo de licença correspondente (O Enterprise Developer não será carregado sem ele).

4. Clique duas vezes no arquivo **edvs2017.exe** para iniciar a instalação. Na primeira janela, selecione o local de instalação e aceite o contrato de licença do usuário final. Se você quiser, escolha **Instalar Rumba 9.5** para instalar este emulador de terminal, que você provavelmente precisará.

     ![Micro Focus Enterprise Developer para visual studio 2017 setup caixa de diálogo](media/04-enterprise-server.png)

5. Depois que a configuração estiver concluída, a seguinte mensagem será exibida:

     ![Configurar mensagem de sucesso](media/05-enterprise-server.png)

6. Inicie o Micro Focus License Manager assim como você fez para o Enterprise Server. Escolha **Iniciar a** \> **administração de licença supérza** **do Micro Focus License** \> Manager e clique na guia **Instalar.**

7. Escolha o tipo de formato de licença para carregar: um arquivo de licença ou um código de licença de 16 caracteres. Por exemplo, para um arquivo, em **arquivo de licença,** navegue até o arquivo **mflic** carregado anteriormente para a VM e selecione **Licenças de instalação**.

     ![Caixa de diálogo Micro Focus License Administration](media/07-enterprise-server.png)

Quando o Enterprise Developer carrega, sua implantação de um ambiente de desenvolvimento e teste do Micro Focus no Azure está completa!

## <a name="next-steps"></a>Próximas etapas

- [Configure o aplicativo Demo do Banco](./demo.md)
- [Executar o Enterprise Server em contêineres Docker](./run-enterprise-server-container.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
