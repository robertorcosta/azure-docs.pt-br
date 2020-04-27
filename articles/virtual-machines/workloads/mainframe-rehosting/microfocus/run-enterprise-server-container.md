---
title: Executar o micro Focus Enterprise Server 4,0 em um contêiner do Docker em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe do IBM z/OS executando o micro Focus Enterprise Server em um contêiner do Docker em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488259"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Executar o micro Focus Enterprise Server 4,0 em um contêiner do Docker no Azure

Você pode executar o micro Focus Enterprise Server 4,0 em um contêiner do Docker no Azure. Este tutorial mostra como. Ele usa a demonstração de acctdemo do Windows CICS (sistema de controle de informações do cliente) para o Enterprise Server.

O Docker adiciona portabilidade e isolamento a aplicativos. Por exemplo, você pode exportar uma imagem do Docker de uma VM do Windows para ser executada em outra ou de um repositório para um Windows Server com o Docker. A imagem do Docker é executada no novo local com a mesma configuração — sem precisar instalar o Enterprise Server. Faz parte da imagem. As considerações de licenciamento ainda se aplicam.

Este tutorial instala o **Windows 2016 datacenter com** VM (máquina virtual) de contêineres no Azure Marketplace. Essa VM inclui o **Docker 18.09.0**. As etapas a seguir mostram como implantar o contêiner, executá-lo e, em seguida, conectar-se a ele com um emulador 3270.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software micro Focus e uma licença válida (ou licença de avaliação). Se você for um cliente do micro Focus existente, entre em contato com seu representante do micro Focus. Caso contrário, [solicite uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Os arquivos de demonstração do Docker estão incluídos no Enterprise Server 4,0. Este tutorial usa o\_servidor\_ent\_dockerfiles\_4,0 Windows. zip. Acesse-o no mesmo local em que você acessou o arquivo de instalação do servidor corporativo ou vá até *micro Focus* para começar.

- A documentação do [Enterprise Server e do Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Proteja a mídia do arquivo ent\_server\_dockerfiles\_4,0\_Windows. zip. Proteja o arquivo de licenciamento ES-Docker-prod-XXXXXXXX. mflic (necessário para criar as imagens do Docker).

2. Crie a VM. Para fazer isso, abra portal do Azure, selecione **criar um recurso** na parte superior esquerda e filtre pelo *Windows Server*. Nos resultados, selecione **Windows Server 2016 datacenter – com contêineres**.

     ![portal do Azure resultados da pesquisa](media/01-portal.png)

3. Para configurar as propriedades da VM, escolha detalhes da instância:

    1. Escolha um tamanho de VM. Para este tutorial, considere o uso de uma VM **\_DS2 v2 padrão** com 2 vCPUs e 7 GB de memória.

    2. Selecione a **região** e o **grupo de recursos** que você deseja implantar.

    3. Para **Opções de disponibilidade**, use a configuração padrão.

    4. Para **nome de usuário**, digite a conta de administrador que você deseja usar e a senha.

    5. Verifique se a **porta 3389 RDP** está aberta. Somente essa porta precisa ser exposta publicamente, para que você possa entrar na VM. Em seguida, aceite todos os valores padrão e clique em **revisar + criar**.

     ![Criar um painel de máquina virtual](media/container-02.png)

4. Aguarde a conclusão da implantação (alguns minutos). Uma mensagem informa que sua VM foi criada.

5. Clique em **ir para o recurso** para ir para a folha de **visão geral** de sua VM.

6. À direita, clique no botão **conectar** . As opções **conectar a máquina virtual** aparecem à direita.

7. Clique no botão **baixar arquivo RDP** para baixar o arquivo RDP que permite anexar à VM.

8. Depois que o download do arquivo for concluído, abra-o e digite o nome de usuário e a senha que você criou para a VM.

     > [!NOTE]
     > Não use suas credenciais corporativas para entrar. (O cliente RDP pressupõe que você talvez queira usá-los. Você não faz isso.)

9.  Selecione **mais opções**e, em seguida, selecione suas credenciais de VM.

Neste ponto, a VM está em execução e anexada via RDP. Você está conectado e pronto para a próxima etapa.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Criar um diretório de área restrita e carregar o arquivo zip

1.  Crie um diretório na VM em que você possa carregar os arquivos de demonstração e de licença. Por exemplo, **C:\\sandbox**.

2.  Carregue **o\_servidor\_ent\_dockerfiles\_4,0 Windows. zip** e o arquivo **es-Docker-prod-xxxxxxxx. mflic** para o diretório que você criou.

3.  Extraia o conteúdo do arquivo zip para o **diretório\_dockerfiles\_\_4,0\_do Windows do servidor ent** criado pelo processo de extração. Esse diretório inclui um arquivo Leiame (como arquivo. html e. txt) e dois subdiretórios, **EnterpriseServer** e **exemplos**.

4.  Copie **es-Docker-prod-xxxxxxxx. mflic** \\para o servidor\\\_\_ent do C: sandbox\_dockerfiles\_4,0\\Windows EnterpriseServer e C\\:\\sandbox\_ent\_Server\_dockerfiles\_4,0\\Windows\\examples CICS diretórios.

> [!NOTE]
> Certifique-se de copiar o arquivo de licenciamento para ambos os diretórios. Eles são necessários para a etapa de Build do Docker para garantir que as imagens sejam licenciadas corretamente.

## <a name="check-docker-version-and-create-base-image"></a>Verificar a versão do Docker e criar a imagem base

> [!IMPORTANT]
> A criação da imagem apropriada do Docker é um processo de duas etapas. Primeiro, crie a imagem base do Enterprise Server 4,0.Em seguida, crie outra imagem para a plataforma x64. Embora você possa criar uma imagem x86 (32 bits), use a imagem de 64 bits.

1. Abra um prompt de comando.

2. Verifique se o Docker está instalado. No prompt de comando, digite:

    ```
        docker version
    ```

     Por exemplo, a versão foi 18.09.0 quando foi gravada.

3. Para alterar o diretório, digite **CD \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. Digite **bld. bat IacceptEULA** para iniciar o processo de compilação para a imagem base inicial. Aguarde alguns minutos para que esse processo seja executado. Nos resultados, observe as duas imagens que foram criadas — uma para x64 e outra para x86:

     ![janela Comando mostrando imagens](media/container-04.png)

5. Para criar a imagem final para a demonstração do CICS, mude para o diretório CICS digitando **\\CD\\sandbox\_ent\_Server\_dockerfiles\_4,0\\Windows\\examples CICS**.

6. Para criar a imagem, digite **bld. bat x64**. Aguarde alguns minutos para que o processo seja executado e a mensagem que diz que a imagem foi criada.

7. Digite **imagens do Docker** para exibir uma lista de todas as imagens do Docker instaladas na VM. Verifique se o **microfocus/es-acctdemo** é um deles.

     ![Janela Comando mostrando a imagem es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Executar a imagem 

1.  Para iniciar o Enterprise Server 4,0 e o aplicativo acctdemo, no prompt de comando, digite:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Instale um emulador de terminal 3270, como [x3270](http://x3270.bgp.nu/) , e use-o para anexar, por meio da porta 9040, à imagem que está em execução.

3.  Obtenha o endereço IP do contêiner acctdemo, de modo que o Docker possa atuar como um servidor DHCP para os contêineres que ele gerencia:

    1.  Obtenha a ID do contêiner em execução. Digite **Docker PS** no prompt de comando e anote a ID (**22a0fe3159d0** neste exemplo). Salve-o para a próxima etapa.

    2.  Para obter o endereço IP para o contêiner acctdemo, use a ID do contêiner da etapa anterior da seguinte maneira:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Por exemplo:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Anote o endereço IP para a imagem acctdemo. Por exemplo, o endereço na saída a seguir é 172.19.202.52.

     ![janela Comando mostrando o endereço IP](media/container-06.png)

5. Monte a imagem usando o emulador. Configure o emulador para usar o endereço da imagem acctdemo e a porta 9040. Aqui está **172.19.202.52:9040**. O seu será semelhante. A tela **Sign-to-CICS** é aberta.

    ![Tela de logon no CICS](media/container-07.png)

6. Entre na região CICS digitando **SYSAD** para o **userid** e **SYSAD** para a **senha**.

7. Limpe a tela, usando o mapa de medida do emulador. Para x3270, selecione a opção de menu de **mapa** de atalho.

8. Para iniciar o aplicativo acctdemo, digite **Acct**. A tela inicial do aplicativo é exibida.

     ![Tela de demonstração da conta](media/container-08.png)

9. Experimente os tipos de conta de exibição. Por exemplo, digite **D** para a solicitação e **11111** para a **conta**. Outros números de conta a serem experimentados são 22222, 33333 e assim por diante.

     ![Tela de demonstração da conta](media/container-09.png)

10. Para exibir o console de administração do servidor corporativo, vá para o prompt de comando e digite **Start http: 172.19.202.52:86**

     ![Console de administração do servidor corporativo](media/container-010.png)

Pronto! Agora você está executando e gerenciando um aplicativo CICS em um contêiner do Docker.

## <a name="next-steps"></a>Próximas etapas

- [Instalar o micro Focus Enterprise Server 4,0 e o desenvolvedor Enterprise 4,0 no Azure](./set-up-micro-focus-azure.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
