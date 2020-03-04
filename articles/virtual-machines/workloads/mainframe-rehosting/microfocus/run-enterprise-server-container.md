---
title: Executar Micro Focus Enterprise Server 4.0 em um contêiner do Docker em máquinas virtuais do Azure
description: Hospede novamente suas cargas de trabalho de mainframe do IBM z/OS, executando Micro Focus Enterprise Server em um contêiner do Docker em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61488259"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Execute o Micro Focus Enterprise Server 4.0 em um contêiner do Docker no Azure

Você pode executar Micro foco Enterprise Server 4.0 em um contêiner do Docker no Azure. Este tutorial mostra como fazer isso. Ele usa a demonstração de acctdemo Windows CICS (sistema de controle de informações do cliente) para o Enterprise Server.

Docker adiciona isolamento e portabilidade para aplicativos. Por exemplo, você pode exportar uma imagem do Docker de uma VM do Windows para executar em outro, ou de um repositório para um servidor Windows com o Docker. A imagem do Docker é executado no novo local com a mesma configuração — sem precisar instalar o servidor corporativo. É um parte da imagem. Considerações sobre licenciamento ainda se aplicam.

Este tutorial instala o **Windows 2016 Datacenter com contêineres** VM (máquina virtual) do Azure Marketplace. Essa VM inclui **Docker 18.09.0**. As etapas a seguir mostram como implantar o contêiner, executá-lo e, em seguida, conecte-se a ele com o emulador 3270.

## <a name="prerequisites"></a>Prerequisites

Antes de começar, confira estes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software do Micro Focus e uma licença válida (ou licença de avaliação). Se você for um cliente existente do Micro Focus, entre em contato com seu representante do Micro Focus. Caso contrário, [solicite uma avaliação](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Os arquivos de demonstração do Docker são incluídos com o Enterprise Server 4.0. Este tutorial usa ent\_server\_dockerfiles\_4.0\_zip. Acessá-lo no mesmo local que você acessou o arquivo de instalação do servidor corporativo, ou vá para *Micro Focus* para começar a usar.

- A documentação para [Enterprise Server e do Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Proteger a mídia contra o ent\_server\_dockerfiles\_4.0\_arquivo zip. Proteger o arquivo de licenciamento de ES-Docker-Prod-XXXXXXXX.mflic (necessário para compilar as imagens do Docker).

2. Crie a VM. Para fazer isso, abra o portal, Azure, escolha **criar um recurso** na parte superior esquerda e filtrar por *do windows server*. Nos resultados, selecione **Windows Server 2016 Datacenter – com contêineres**.

     ![Resultados de pesquisa do portal do Azure](media/01-portal.png)

3. Para configurar as propriedades para a VM, escolha os detalhes da instância:

    1. Escolha um tamanho de VM. Para este tutorial, considere o uso de um **Standard DS2\_v2** VM com vCPUs de 2 a 7 GB de memória.

    2. Selecione o **região** e **grupo de recursos** você gostaria de implantar.

    3. Para **opções de disponibilidade**, use a configuração padrão.

    4. Para **nome de usuário**, digite a conta de administrador que você deseja usar e a senha.

    5. Certifique-se **porta RDP 3389** está aberto. Somente essa porta precisa ser exposto publicamente, para que você possa entrar na VM. Em seguida, aceite todos os valores padrão e clique em **revisar + criar**.

     ![Criar um painel de máquina virtual](media/container-02.png)

4. Aguarde até que a implantação seja concluída (alguns minutos). Uma mensagem informa que sua VM foi criada.

5. Clique em **ir para o recurso** para ir para o **visão geral** folha para sua VM.

6. À direita, clique o **Connect** botão. O **conectar-se a máquina virtual** opções são exibidas à direita.

7. Clique o **baixar o arquivo RDP** botão para baixar o arquivo RDP que permite que você anexe à VM.

8. Depois que o arquivo de download for concluído, abra ele e digite o nome de usuário e senha que você criou para a VM.

     > [!NOTE]
     > Não use suas credenciais corporativas para entrar. (O cliente RDP pressupõe que você talvez queira usá-los. Você não fizer isso.)

9.  Selecione **mais escolhas**, em seguida, selecione suas credenciais de VM.

Neste ponto, a VM está em execução e conectado via RDP. Você está conectado no e prontos para a próxima etapa.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Crie um diretório de área restrita e carregue o arquivo zip

1.  Crie um diretório na máquina virtual em que você pode carregar os arquivos de licença e demonstração. Por exemplo, **c:\\Sandbox**.

2.  Carregue **ent\_server\_dockerfiles\_4.0\_ZIP** e o **ES-Docker-Prod-XXXXXXXX.mflic** arquivo para o diretório que você criou.

3.  Extraia o conteúdo do arquivo zip para o **ent\_server\_dockerfiles\_4.0\_windows** diretório criado pelo processo de extração. Esse diretório inclui um arquivo Leiame (como o arquivo. HTML e. txt) e dois subdiretórios, **EnterpriseServer** e **exemplos**.

4.  Cópia **ES-Docker-Prod-XXXXXXXX.mflic** para a unidade c:\\Sandbox\\ent\_servidor\_dockerfiles\_4.0\_windows\\ EnterpriseServer e c:\\área restrita\\ent\_server\_dockerfiles\_4.0\_windows\\exemplos\\diretórios do CICS.

> [!NOTE]
> Verifique se que você copiar o arquivo de licenciamento para os dois diretórios. Eles são necessários para a etapa de build do Docker garantir que as imagens estão licenciadas corretamente.

## <a name="check-docker-version-and-create-base-image"></a>Verificar versão do Docker e criar a imagem base

> [!IMPORTANT]
> Criando a imagem apropriada do Docker é um processo em duas etapas. Primeiro, crie a imagem base do Enterprise Server 4.0. Em seguida, crie outra imagem para o x64 plataforma. Embora você possa criar um x86 (32 bits) da imagem, use a imagem de 64 bits.

1. Abra um prompt de comando.

2. Verifique se o Docker está instalado. No prompt de comando, digite:

    ```
        docker version
    ```

     Por exemplo, a versão foi 18.09.0 quando este texto foi escrito.

3. Para alterar o diretório, digite **\Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer cd**.

4. Tipo de **bld.bat IacceptEULA** para iniciar o processo de compilação para a imagem de base inicial. Aguarde alguns minutos para executar este processo. Nos resultados, observe as duas imagens que foram criados — um para x64 e outro para x86:

     ![Janela de comando mostrando imagens](media/container-04.png)

5. Para criar a imagem final para a demonstração do CICS, alterne para o diretório do CICS digitando **cd\\Sandbox\\ent\_servidor\_dockerfiles\_4.0\_windows\\ Exemplos\\CICS**.

6. Para criar a imagem, digite **bld.bat x64**. Aguarde alguns minutos para que o processo seja executado e a mensagem que informa que a imagem foi criada.

7. Tipo de **imagens do docker** para exibir uma lista de todas as imagens do Docker instaladas na VM. Certifique-se **microfocus/es-acctdemo** é um deles.

     ![Janela de comando que mostra a imagem de es acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Executar a imagem 

1.  Para iniciar o Enterprise Server 4.0 e o aplicativo de acctdemo, no prompt de comando digite:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Instalar um emulador de terminal 3270 como [x3270](http://x3270.bgp.nu/) e usá-lo para anexar, através da porta 9040, para a imagem que está sendo executado.

3.  Obtenha o endereço IP do contêiner acctdemo, portanto, o Docker pode agir como um servidor DHCP para os contêineres que ele gerencia:

    1.  Obter a ID do contêiner em execução. Tipo de **Docker ps** no prompt de comando e observe a ID (**22a0fe3159d0** neste exemplo). Salve-o para a próxima etapa.

    2.  Para obter o endereço IP para o contêiner acctdemo, use a ID do contêiner da etapa anterior como segue:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Por exemplo:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Anote o endereço IP para a imagem acctdemo. Por exemplo, o endereço na seguinte saída é 172.19.202.52.

     ![Janela de comando mostrando o endereço IP](media/container-06.png)

5. Monte a imagem usando o emulador. Configure o emulador para usar o endereço da imagem acctdemo e porta 9040. Aqui, ele tem **172.19.202.52:9040**. Seu será semelhante. O **logon para CICS** tela abre.

    ![Logon à tela do CICS](media/container-07.png)

6. Entrar para a região do CICS inserindo **SYSAD** para o **USERID** e **SYSAD** para o **senha**.

7. Limpe a tela, usando o mapa de teclas do emulador. Para x3270, selecione o **mapa de teclas** opção de menu.

8. Para iniciar o aplicativo acctdemo, digite **ACCT**. A tela inicial para o aplicativo é exibida.

     ![Tela de demonstração de conta](media/container-08.png)

9. Experimente com tipos de conta do vídeo. Por exemplo, digite **1!d** para a solicitação e **11111** para o **conta**. Outros números de conta para tentar são 22222, 33333 e assim por diante.

     ![Tela de demonstração de conta](media/container-09.png)

10. Para exibir o console de administração de servidor corporativo, vá para o prompt de comando e digite **iniciar http:172.19.202.52:86**

     ![Console de administração de servidor corporativo](media/container-010.png)

É isso! Agora você está executando e gerenciando um aplicativo do CICS em um contêiner do Docker.

## <a name="next-steps"></a>Próximas etapas

- [Instalar o Micro Focus Enterprise Server 4.0 e do Enterprise Developer 4.0 no Azure](./set-up-micro-focus-azure.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
