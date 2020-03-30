---
title: Configure uma ADCD (Application Developers Controlled Distribution, distribuição controlada de desenvolvedores de aplicativos) no IBM zD&T v1 | Microsoft Docs
description: Execute um ambiente de desenvolvimento e teste IBM Z (zD&T) em VMs (Azure Virtual Machines).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841378"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Configure uma ADCD (Application Developers Controlled Distribution, distribuição controlada de desenvolvedores de aplicativos) no IBM zD&T v1

Você pode executar um ambiente de desenvolvimento e teste IBM Z (zD&T) em VMs (Azure Virtual Machines). Este ambiente emula a arquitetura IBM Z Series. Ele pode hospedar uma variedade de sistemas operacionais ou instalações da Série Z (também chamados de Instâncias Z ou Pacotes), que são disponibilizados através de pacotes personalizados chamados a ADCDs (Ibm Application Developers Controlled Distributions).

Este artigo mostra como configurar uma instância ADCD em um ambiente zD&T no Azure. Os ADCDs criam implementações completas do sistema operacional Série Z para ambientes de desenvolvimento e teste que são executados em zD&T.

Como zD&T, os ADCDs estão disponíveis apenas para clientes e parceiros da IBM e são exclusivamente para fins de desenvolvimento e teste. Eles não devem ser usados para ambientes de produção. Vários pacotes de instalação da IBM estão disponíveis para download através do [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou [ibm PartnerWorld](https://www.ibm.com/partnerworld/public).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O [ambiente zD&T][ibm-install-z] previamente configurado no Azure. Este artigo pressupõe que você está usando a mesma imagem de VM Ubuntu 16.04 criada anteriormente.

- Acesso à mídia ADCD através do IBM PartnerWorld ou Passport Advantage.

- Um [servidor de licenciamento](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Isso é necessário para executar o IBM zD&T. A maneira como você o cria depende de como você licencia o software da IBM:

  - **O servidor de licenciamento baseado em hardware** requer um dispositivo de hardware USB que contenha os Tokens Racionais necessários para acessar todas as partes do software. Você deve obter isso da IBM.

  - **O servidor de licenciamento baseado em software** exige que você configure um servidor centralizado para o gerenciamento das chaves de licenciamento. Este método é preferido e exige que você configure as chaves que você recebe da IBM no servidor de gerenciamento.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Baixe os pacotes de instalação do Passport Advantage

É necessário acesso à mídia ADCD. As etapas abaixo supõem que você é um cliente ibm e pode usar o Passport Advantage. Os parceiros ibm podem usar [o IBM PartnerWorld](https://www.ibm.com/partnerworld/public).

> [!NOTE]
> Este artigo pressupõe que um PC Windows é usado para acessar o portal Azure e para baixar a mídia IBM. Se você estiver usando um desktop Mac ou Ubuntu, os comandos e o processo para obter a mídia IBM podem diferir ligeiramente.

1. Faça logon no [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Selecione **Downloads de software** e **acesso à mídia**.

3. Selecione **a oferta do programa e o número do contrato**e clique em **Continuar**.

4. Digite a descrição da peça ou o número da peça e clique em **Finder**.

5. Opcionalmente, clique na lista de pedidos alfabéticos para exibir e exibir o produto pelo nome.

6. Selecione **Todos os sistemas operacionais** no campo do sistema **operacional**e todas **as linguagens** no campo **Idiomas**. Em seguida, clique em **Go**.

7. Clique **em Selecionar arquivos individuais** para expandir a lista e exibir a mídia individual para baixar.

8. Verifique os pacotes que deseja baixar, selecione **Baixar**e baixe os arquivos para o diretório que deseja.

## <a name="upload-the-adcd-packages"></a>Faça upload do pacote ADCD

Agora que você tem o pacote(s), você deve carregá-los para o seu VM no Azure.

1. No portal Azure, inicie uma sessão **ssh** com o VM Ubuntu que você criou. Vá para sua VM, selecione a **lâmina Visão geral** e, em seguida, selecione **Conectar**.

2. Selecione a guia **SSH** e copie o comando ssh na área de transferência.

3. Faça logon em sua VM usando suas credenciais e o [cliente SSH](/azure/virtual-machines/linux/use-remote-desktop) de escolha. Esta demonstração usa as extensões Linux para windows 10, que adiciona um shell bash ao prompt de comando do Windows. PuTTY funciona tão bem.

4. Ao fazer logon, crie um diretório para carregar os pacotes ibm. Tenha em mente que o Linux é sensível a casos. Por exemplo, esta demonstração assume que os pacotes são carregados para:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Faça upload dos arquivos usando um cliente SSH, como[o WinSCP](https://winscp.net/eng/index.php). Como o SCP faz parte do SSH, ele usa a porta 22, que é o que o SSH usa. Se o computador local não for o Windows, você pode digitar o [comando scp](http://man7.org/linux/man-pages/man1/scp.1.html) na sessão SSH.

6. Inicie o upload para o diretório Azure VM que você criou, que se torna o armazenamento de imagem para zD&T.

    > [!NOTE]
    > Certifique-se de que **a ADCDTOOLS. O XML** está incluído no upload para o diretório **home/MyUserID/ZDT/adcd/nov2017.** Você precisará dela mais tarde.

7. Aguarde o upload dos arquivos, o que pode levar algum tempo dependendo da sua conexão com o Azure.

8. Quando os uploads estiverem concluídos, navegue até o diretório de volumes e descompacte todos os volumes **gz:**

    ```
        gunzip \*.gz
    ```
    
![Explorador de arquivos mostrando volumes gz descomprimidos](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configure o armazenamento de imagens

O próximo passo é configurar zD&T para usar os pacotes carregados. O processo de armazenamento de imagem dentro do zD&T permite que você monte e use as imagens. Pode usar SSH ou FTP.

1. Inicie o **zDTServer**. Para fazer isso, você deve estar no nível raiz. Digite os dois comandos a seguir em ordem:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Observe a saída de URL pelo comando e use esta URL para acessar o servidor web. É semelhante a:
     > https://(seu nome VM ou endereço IP):9443/ZDTMC/index.html
     >
     > Lembre-se, seu acesso à Web usa a porta 9443. Use isso para fazer logon no servidor web. O ID do usuário para ZD&T é **zdtadmin** e a senha é **senha**.

    ![IBM zD&tela de boas-vindas da T Enterprise Edition](media/02-welcome.png)

3. Na página **Início rápido,** em **Configure, selecione** **Armazenamento de imagens**.

     ![IBM zD&tela de início rápido do T Enterprise Edition](media/03-quickstart.png)

4. Na página **Configurar armazenamento de imagem,** selecione **SSH File Transfer Protocol**.

5. Para **nome do host,** digite **Localhost** e digite o caminho do diretório para onde você carregou as imagens. Por exemplo, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Digite o **ID do Usuário** e **a senha** da VM. Não use o ID e senha do usuário ZD&T.

7. Teste a conexão para ter certeza de que você tem acesso e, em seguida, **selecione Salvar** para salvar a configuração.

## <a name="configure-the-target-environments"></a>Configure os ambientes de destino

O próximo passo é configurar o ambiente de destino zD&T. Este ambiente hospedado emulado é onde suas imagens são executadas.

1. Na página **Iniciar rápido,** em **Configure, selecione** **'Segmentar'.**

2. Na página **Configurar ambientes de destino,** selecione **Adicionar destino**.

3. Selecione **Linux**. A IBM suporta dois tipos de ambientes, Linux e Cloud (OpenStack), mas essa demonstração é executada no Linux.

4. Na página **Adicionar ambiente de destino,** para **nome do host,** digite **host local**. Mantenha a **porta SSH** definida como **22**.

5. Na caixa **de etiquetas Target Environment,** digite um rótulo como **MyCICS.**

     ![Adicionar tela de ambiente de destino](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configure a ADCD e implante

Depois de concluir as etapas de configuração anteriores, você deve configurar zD&T para usar o ambiente de pacotes e de destino. Novamente, você usa o processo de armazenamento de imagem em zD&T, que permite montar e usar as imagens. Pode usar SSH ou FTP.

1. Na página **Início rápido,** em **Configure, selecione** **ADCD**. Um conjunto de instruções aparecem, inddizendo-lhe as etapas que precisam ser concluídas antes que um pacote ADCD possa ser montado. Isso explica por que nomeamos o diretório alvo do jeito que fizemos antes.

2. Supondo que todas as imagens foram enviadas para os diretórios corretos, clique na imagem do link **ADCD** exibido no lado inferior direito (mostrado na etapa 7 na captura de tela a seguir).

     ![IBM zD&T Enterprise Edition - Configurar tela ADCD](media/05-adcd.png)

## <a name="create-the-image"></a>Criar a imagem

Quando a etapa de configuração anterior estiver concluída, a **página Criar uma imagem usando componentes ADCD** será exibida.

1. Selecione o volume (Nov 2017 neste caso) para exibir os diferentes pacotes que estão nesse volume.

2. Para esta demonstração, selecione O Sistema de **Controle de Informações ao Cliente (CICS) - 5.3**.

3. Na **caixa Nome da imagem,** digite um nome para a imagem como **MyCICS Image**.

4. Selecione o botão **Criar imagem** no lado inferior direito.

     ![IBM zD&T Enterprise Edition - Crie uma imagem usando a tela aDCD Components](media/06-adcd.png)

5. Na janela que aparece, indando que a imagem foi implantada com sucesso, escolha **Implantar imagens**.

6. Na **Página Implantar uma imagem em um ambiente de destino,** selecione a imagem criada na página anterior (**MyCICS Image**) e o ambiente de destino criado anteriormente **(MyCICS).**

7. Na próxima tela, forneça suas credenciais para o VM (ou seja, não a credencial ztadmin).

8. No painel Propriedades, digite o número de **processadores Centrais (CPs),** a quantidade de memória do **sistema (GB)** e o diretório de **implantação** da imagem em execução. Já que é uma demonstração, mantenha-a pequena.

9. Certifique-se de que a caixa está selecionada para emitir automaticamente o **comando IPL para z/OS após a implantação**.

     ![Tela de propriedades](media/07-properties.png)

10. Selecione **Concluído**.

11. Selecione **Implantar imagem** a partir da implantação de uma imagem para uma página de ambiente **de destino.**

Sua imagem agora pode ser implantada e está pronta para ser montada por um emulador de terminal 3270.

> [!NOTE]
> Se você receber um erro dizendo que não tem espaço em disco suficiente, observe que a região requer 151 Gb.

Parabéns! Agora você está executando um ambiente de mainframe da IBM no Azure.

## <a name="learn-more"></a>Saiba mais

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Solução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
