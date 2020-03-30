---
title: Usar o túnel SSH para acessar o Azure HDInsight
description: Saiba como usar um túnel SSH para navegar com segurança em recursos da Web hospedados em seus nós HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991345"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Use o túnel SSH para acessar a ui web Apache Ambari, JobHistory, NameNode, Apache Oozie e outras UIs

Os clusters HDInsight oferecem acesso à interface do usuário da Web do Apache Ambari pela Internet, mas alguns recursos requerem um túnel SSH. Por exemplo, a ui web para o serviço Apache Oozie não pode ser acessada pela internet sem um túnel SSh.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários menus no Ambari só funcionam por meio de um túnel SSH. Esses menus dependem de sites e serviços em execução em outros tipos de nó, como nós de trabalho.

As seguintes interfaces do usuário da Web exigem um túnel SSH:

* JobHistory
* NameNode
* Pilhas de Threads
* Interface do usuário da Web do Oozie
* Interface do usuário mestre e de logs do HBase

Se você usar as Ações de Script para personalizar seu cluster, todos os serviços ou utilitários que forem instalados e que expuserem um serviço Web exigirão um túnel SSH. Por exemplo, se você instalar o Hue usando uma Ação de Script, deverá usar um túnel SSH para acessar a interface do usuário da Web do Hue.

> [!IMPORTANT]  
> Se tiver acesso direto ao HDInsight por meio de uma rede virtual, você não precisará usar túneis SSH. Para obter um exemplo de acesso direto ao HDInsight por meio de uma rede virtual, consulte o documento [Conectar HDInsight em sua rede local](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

[O túnel Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) se conecta a uma porta em seu computador local para um nó principal no HDInsight. O tráfego enviado para a porta local é roteado por meio de uma conexão SSH para o nó principal. A solicitação é resolvida como se ela tivesse sido originada no nó principal. A resposta é então roteada de volta pelo túnel até a sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador da Web que pode ser configurado para usar um proxy SOCKS5.

    > [!WARNING]  
    > O suporte a proxy SOCKS integrado das configurações de Internet do Windows não dá suporte a SOCKS5 e não funciona com as etapas neste documento. Os navegadores a seguir contam com as configurações de proxy do Windows e não funcionam com as etapas neste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > O Google Chrome também conta com as configurações de proxy do Windows. No entanto, você pode instalar extensões que dão suporte a SOCKS5. Recomendamos o [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Criar um túnel usando o comando SSH

Use o comando a seguir para criar um túnel SSH usando o comando `ssh` . Substitua por `sshuser` um usuário SSH pelo `CLUSTERNAME` seu cluster HDInsight e substitua pelo nome do seu cluster HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Esse comando cria uma conexão que encaminha o tráfego para a porta local 9876 do cluster via SSH. As opções são:

* **D 9876**: a porta local que roteará o tráfego pelo túnel.
* **C** : compactar todos os dados, porque o tráfego da Web é texto, em sua maioria.
* **2** : forçar o SSH para tentar somente a versão 2 do protocolo.
* **q** : modo silencioso.
* **T** - Desabilitar a alocação pseudo-tty, já que você está apenas encaminhando uma porta.
* **n** - Evite a leitura de STDIN, já que você está apenas encaminhando uma porta.
* **N** - Não execute um comando remoto, já que você está apenas encaminhando uma porta.
* **f** : executar em segundo plano.

Quando o comando terminar, o tráfego enviado para a porta 9876 no computador local será roteado para o nó de cabeçalho do cluster.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Criar um túnel usando o PuTTY

O [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) é um cliente SSH gráfico do Windows. Se você não estiver familiarizado com putty, consulte a [documentação PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Use as etapas a seguir para criar um túnel SSH usando o PuTTY:

### <a name="create-or-load-a-session"></a>Criar ou carregar uma sessão

1. Abra o PuTTY e certifique-se de que **Sessão** está selecionada no menu esquerdo. Se você já salvou uma sessão, selecione o nome da sessão na lista **'Sessões salvas'** e selecione **'Carregar '''Carga'.**

1. Se você ainda não tiver uma sessão salva, insira suas informações de conexão:

    |Propriedade |Valor |
    |---|---|
    |Nome do host (ou endereço IP)|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Porta|22|
    |Tipo de Conexão|SSH|

1. Selecione **Salvar**

    ![HDInsight cria sessão de massa](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Na seção **Categoria** à esquerda da caixa de diálogo, expanda **Conexão**, expanda **SSH** e selecione **Túneis**.

1. Forneça as seguintes informações sobre o **formulário de encaminhamento da porta SSH que controla as opções:**

    |Propriedade |Valor |
    |---|---|
    |Porta de origem|A porta do cliente que você deseja encaminhar. Por exemplo, **9876**.|
    |Destino|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Dinâmico|Habilita o roteamento de proxy dynamic SOCKS.|

    ![Opções de tunelamento de configuração PuTTY](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Selecione **Adicionar** para adicionar as configurações e, em seguida, selecione **Abrir** para abrir uma conexão SSH.

1. Quando solicitado, faça login no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Usar o túnel de seu navegador

> [!IMPORTANT]  
> As etapas nesta seção usam o navegador Mozilla FireFox, pois ele fornece as mesmas configurações de proxy em todas as plataformas. Outros navegadores modernos como Google Chrome podem exigir uma extensão como o FoxyProxy para trabalhar com o túnel.

1. Configure o navegador para usar **localhost** e a porta usada ao criar o túnel como um proxy **SOCKS v5**. Aqui está a aparência das configurações do Firefox. Se você tiver usado uma porta diferente da 9876, altere a porta que usou:

    ![configurações proxy do navegador firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Selecionar **DNS Remoto** resolve as solicitações de DNS (Sistema de Nomes de Domínio) usando o cluster HDInsight. Essa configuração resolve o DNS usando o nó principal do cluster.

2. Verifique se o túnel funciona visitando [https://www.whatismyip.com/](https://www.whatismyip.com/)um local como . O IP retornado deve ser um IP usado pelo data center do Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verifique com a interface do usuário do Ambari Web

Assim que o cluster tiver sido estabelecido, use as etapas a seguir para verificar se você pode acessar as interfaces do usuário da Web do serviço Ambari Web:

1. No navegador, acesse `http://headnodehost:8080`. O endereço `headnodehost` é enviado pelo túnel para o cluster e resolverá o nó principal que o Ambari está executando. Quando solicitado, insira o nome de usuário do administrador (admin) e a senha do seu cluster. Talvez a interface do usuário do Ambari Web seja solicitada uma segunda vez. Nesse caso, insira novamente as informações.

   > [!NOTE]  
   > Ao usar o endereço `http://headnodehost:8080` para conectar o cluster, você estará conectando através do túnel. A comunicação é protegida usando o túnel SSH em vez de HTTPS. Para conectar pela internet usando `https://clustername.azurehdinsight.net`HTTPS, use , onde `clustername` está o nome do cluster.

2. Na interface do usuário do Ambari na Web, selecione HDFS na lista à esquerda da página.

    ![Serviço apache ambari hdfs selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Quando as informações do serviço HDFS forem exibidas, selecione **Links Rápidos**. Uma lista de nós de cabeçalho do cluster é exibida. Escolha um dos nós de cabeça e **Interface de Usuário do NameNode**.

    ![Imagem do menu Links Rápidos expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Quando você seleciona __Links rápidos__, pode receber um indicador de espera. Essa condição poderá ocorrer se você tiver uma conexão de Internet lenta. Aguarde um minuto ou dois pelos dados a serem recebidos do servidor e experimente a lista novamente.
    >
    > Algumas entradas no menu **Links rápidos** podem ser cortadas pelo lado direito da tela. Nesse caso, expanda o menu usando o mouse e a tecla de seta para a direita para rolar a tela para a direita e ver o restante do menu.

4. Uma página semelhante à seguinte imagem será exibida:

    ![Imagem da UI Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Observe a URL dessa página, que deve ser semelhante a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Essa URI está usando o nome de domínio totalmente qualificado (FQDN) interno do nó e é acessada apenas ao utilizar um túnel SSH.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar e usar um túnel SSH, consulte o seguinte documento para outras maneiras de usar o Ambari:

* [Gerenciar clusters HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
