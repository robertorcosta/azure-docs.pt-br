---
title: Execute o Micro Focus Enterprise Server 4.0 em um contêiner Docker em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe do IBM z/OS executando o Micro Focus Enterprise Server em um contêiner Docker em Máquinas Virtuais Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488259"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Execute o Micro Focus Enterprise Server 4.0 em um contêiner Docker no Azure

Você pode executar o Micro Focus Enterprise Server 4.0 em um contêiner Docker no Azure. Este tutorial mostra como. Ele usa a demonstração de acctdemo do Windows CICS (Customer Information Control System) para o Enterprise Server.

O Docker adiciona portabilidade e isolamento aos aplicativos. Por exemplo, você pode exportar uma imagem Docker de uma VM do Windows para ser executada em outra ou de um repositório para um servidor Windows com Docker. A imagem do Docker é executada no novo local com a mesma configuração — sem ter que instalar o Enterprise Server. Faz parte da imagem. As considerações de licenciamento ainda se aplicam.

Este tutorial instala o **Datacenter windows 2016 com** a máquina virtual containers (VM) do Azure Marketplace. Esta VM inclui **Docker 18.09.0**. As etapas abaixo mostram como implantar o contêiner, executá-lo e, em seguida, conectá-lo com um emulador 3270.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confira estes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- O software Micro Focus e uma licença válida (ou licença de teste). Se você é um cliente micro focus existente, entre em contato com o representante da Micro Focus. Caso contrário, [solicite um julgamento.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

     > [!NOTE]
     > Os arquivos de demonstração do Docker estão incluídos no Enterprise Server 4.0. Este tutorial\_usa\_o\_ent\_server dockerfiles 4.0 windows.zip. Acesse-o a partir do mesmo local em que você acessou o arquivo de instalação do Enterprise Server ou vá ao *Micro Focus* para começar.

- A documentação para [Enterprise Server e Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Proteja a mídia\_do\_arquivo\_ent\_server dockerfiles 4.0 windows.zip. Proteja o arquivo de licenciamento ES-Docker-Prod-XXXXXXXX.mflic (necessário para construir as imagens do Docker).

2. Crie a VM. Para isso, abra o portal Azure, **selecione Criar um recurso** do canto superior esquerdo e filtrar pelo *servidor windows*. Nos resultados, selecione **o Datacenter do Windows Server 2016 – com Containers**.

     ![Resultados de pesquisa do portal Azure](media/01-portal.png)

3. Para configurar as propriedades da VM, escolha os detalhes da ocorrência:

    1. Escolha um tamanho de VM. Para este tutorial, considere usar um **VM Padrão\_DS2 v2** com 2 vCPUs e 7 GB de memória.

    2. Selecione o **Grupo de Região** e **Recursos** para o que você deseja implantar.

    3. Para **opções disponibilidade,** use a configuração padrão.

    4. Para **Nome de Usuário,** digite a conta de administrador que deseja usar e a senha.

    5. Certifique-se de que **a porta 3389 RDP** está aberta. Só que esta porta precisa ser exposta publicamente, para que você possa entrar na VM. Em seguida, aceite todos os valores padrão e clique **em 'Revisar+ criar**.

     ![Crie um painel de máquina virtual](media/container-02.png)

4. Aguarde o término da implantação (alguns minutos). Uma mensagem afirma que sua VM foi criada.

5. Clique **em Ir para Recurso** para ir para a lâmina Visão **Geral** para sua VM.

6. À direita, clique no botão **Conectar.** As opções **Conectar à máquina virtual** aparecem à direita.

7. Clique no botão **Baixar arquivo RDP** para baixar o arquivo RDP que permite anexar à VM.

8. Depois que o arquivo terminar de baixar, abra-o e digite o nome de usuário e a senha que você criou para a VM.

     > [!NOTE]
     > Não use suas credenciais corporativas para fazer login. (O cliente RDP assume que você pode querer usá-los. Você não.)

9.  Selecione **Mais Escolhas**e selecione suas credenciais de VM.

Neste ponto, a VM está sendo rodada e anexada via RDP. Você está inscrito e pronto para o próximo passo.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Crie um diretório de caixa de areia e carregue o arquivo zip

1.  Crie um diretório na VM onde você pode carregar os arquivos de demonstração e licença. Por exemplo, **C:\\Sandbox**.

2.  Carregue **\_o\_servidor\_ent\_dockerfiles 4.0 windows.zip** e o arquivo **ES-Docker-Prod-XXXXXXXX.mflic** para o diretório que você criou.

3.  Extrair o conteúdo do arquivo zip para o **\_\_servidor ent dockerfiles\_\_4.0 windows** diretório criado pelo processo de extração. Este diretório inclui um arquivo readme (como arquivo .html e .txt) e dois subdiretórios, **EnterpriseServer** e **Examples**.

4.  Copie **ES-Docker-Prod-XXXXXXXX.mflic** para\\o servidor\\\_C: Sandbox ent server\_dockerfiles\_4.0\_windows\\EnterpriseServer\_\\e\\C:\\Sandbox\\ent\_server\_dockerfiles\_4.0 windows Exemplos diretórios CICS.

> [!NOTE]
> Certifique-se de copiar o arquivo de licenciamento para ambos os diretórios. Eles são necessários para a etapa de compilação do Docker para garantir que as imagens sejam devidamente licenciadas.

## <a name="check-docker-version-and-create-base-image"></a>Verifique a versão do Docker e crie imagem base

> [!IMPORTANT]
> Criar a imagem Docker apropriada é um processo de duas etapas. Primeiro, crie a imagem base do Enterprise Server 4.0.Em seguida, crie outra imagem para a plataforma x64. Embora você possa criar uma imagem x86 (32 bits), use a imagem de 64 bits.

1. Abra um prompt de comando.

2. Verifique se o Docker está instalado. No prompt de comando, digite:

    ```
        docker version
    ```

     Por exemplo, a versão era 18.09.0 quando esta foi escrita.

3. Para alterar o diretório, digite **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Digite **bld.bat IacceptEULA** para iniciar o processo de compilação da imagem base inicial. Aguarde alguns minutos para que esse processo seja executado. Nos resultados, observe as duas imagens que foram criadas — uma para x64 e outra para x86:

     ![Janela de comando mostrando imagens](media/container-04.png)

5. Para criar a imagem final para a demonstração do CICS, mude para o diretório cics digitando **\\cd\\Sandbox ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS**.

6. Para criar a imagem, **digite bld.bat x64**. Aguarde alguns minutos para que o processo seja executado e a mensagem dizendo que a imagem foi criada.

7. Digite **imagens de docker** para exibir uma lista de todas as imagens do Docker instaladas na VM. Certifique-se de que **microfoco/es-acctdemo** é um deles.

     ![Janela de comando mostrando imagem es-acctdemo](media/container-05.png)

## <a name="run-the-image"></a>Executar a imagem 

1.  Para iniciar o Enterprise Server 4.0 e o aplicativo acctdemo, no tipo de prompt de comando:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Instale um emulador de terminal 3270 como [x3270](http://x3270.bgp.nu/) e use-o para anexar, via porta 9040, à imagem que está em execução.

3.  Obtenha o endereço IP do contêiner de adesão, para que o Docker possa atuar como um servidor DHCP para os contêineres que gerencia:

    1.  Pegue a id do recipiente em execução. Digite **Docker ps** no prompt de comando e observe o ID **(22a0fe3159d0** neste exemplo). Guarde para o próximo passo.

    2.  Para obter o endereço IP do contêiner de adesão, use o ID do contêiner da etapa anterior da seguinte forma:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Por exemplo: 

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Observe o endereço IP da imagem de adesão. Por exemplo, o endereço na saída a seguir é 172.19.202.52.

     ![Janela de comando mostrando endereço IP](media/container-06.png)

5. Monte a imagem usando o emulador. Configure o emulador para usar o endereço da imagem de adesão e a porta 9040. Aqui, é **172.19.202.52:9040**. O seu será parecido. A **tela Signon to CICS** é aberta.

    ![Signon para tela CICS](media/container-07.png)

6. Faça login na Região CICS inserindo **o SYSAD** para o **USERID** e **o SYSAD** para a **Senha**.

7. Limpe a tela, usando o mapa-chave do emulador. Para x3270, selecione a opção **Menu Keymap.**

8. Para iniciar o aplicativo de acctdemo, digite **ACCT**. A tela inicial do aplicativo é exibida.

     ![Tela de demonstração da conta](media/container-08.png)

9. Experimente com tipos de conta de exibição. Por exemplo, tipo **D** para a Solicitação e **11111** para a **CONTA**. Outros números de conta para tentar são 22222, 33333, e assim por diante.

     ![Tela de demonstração da conta](media/container-09.png)

10. Para exibir o console Enterprise Server Administration, vá para o prompt de comando e **digite o http:172.19.202.52:86**

     ![Console de Administração de Servidores Corporativos](media/container-010.png)

É isso! Agora você está executando e gerenciando um aplicativo CICS em um contêiner Docker.

## <a name="next-steps"></a>Próximas etapas

- [Instale o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure](./set-up-micro-focus-azure.md)
- [Migração de aplicativos de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
