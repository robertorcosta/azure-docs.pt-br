---
title: Instale o TmaxSoft OpenFrame em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe ibm z/OS usando o ambiente TmaxSoft OpenFrame em VMs (Azure Virtual Machines).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436057"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instale o TmaxSoft OpenFrame no Azure

Saiba como configurar um ambiente OpenFrame no Azure adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Este tutorial orienta você a cada etapa.

O OpenFrame inclui vários componentes que criam o ambiente de emulação do mainframe no Azure. Por exemplo, os serviços on-line OpenFrame substituem o middleware mainframe, como o IBM Customer Information Information Control System (CICS) e o OpenFrame Batch, com seu componente TJES, substitui o Subsistema de Entrada de Emprego (JES) do mainframe da IBM.

O OpenFrame funciona com qualquer banco de dados relacional, incluindo Oracle Database, Microsoft SQL Server, IBM Db2 e MySQL. Esta instalação do OpenFrame usa o banco de dados relacional TmaxSoft Tibero. Tanto o OpenFrame quanto o Tibero são executados em um sistema operacional Linux. Este tutorial instala o CentOS 7.3, embora você possa usar outras distribuições Linux suportadas. O servidor de aplicativos OpenFrame e o banco de dados Tibero são instalados em uma máquina virtual (VM).

O tutorial passo solado você através da instalação dos componentes do pacote OpenFrame. Alguns devem ser instalados separadamente.

Principais componentes do OpenFrame:

- Pacotes de instalação necessários.
- Banco de dados tibero.
- Open Database Connectivity (ODBC) é usado por aplicativos no OpenFrame para se comunicar com o banco de dados Tibero.
- OpenFrame Base, o middleware que gerencia todo o sistema.
- OpenFrame Batch, a solução que substitui os sistemas de lote do mainframe.
- TACF, um módulo de serviço que controla o acesso do usuário a sistemas e recursos.
- ProSort, uma ferramenta de classificação para transações em lote.
- OFCOBOL, um compilador que interpreta os programas COBOL do mainframe.
- OFASM, um compilador que interpreta os programas de montagem do mainframe.
- OpenFrame Server Type C (OSC), a solução que substitui o middleware do mainframe e o IBM CICS.
- Java Enterprise User Solution (JEUS), um servidor de aplicativo web certificado para Java Enterprise Edition 6.
- OFGW, o componente do gateway OpenFrame que fornece um ouvinte 3270.
- OFManager, uma solução que fornece as funções de operação e gerenciamento do OpenFrame no ambiente web.

Outros componentes do OpenFrame necessários:

- OSI, a solução que substitui o middleware mainframe e o IMS DC.
- TJES, a solução que fornece o ambiente JES do mainframe.
- OFTSAM, a solução que permite que (V)arquivos SAM sejam usados no sistema aberto.
- OFHiDB, a solução que substitui o IMS DB do mainframe.
- OFPLI, um compilador que interpreta os programas PL/I do mainframe.
- PROTRIEVE, uma solução que executa a linguagem de mainframe CA-Easytrieve.
- OFMiner, uma solução que analisa os ativos dos mainframes e depois os migra para o Azure.

## <a name="architecture"></a>Arquitetura

A figura a seguir fornece uma visão geral dos componentes arquitetônicos do OpenFrame 7.0 instalados neste tutorial:

![Componentes OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisitos do sistema Azure

A tabela a seguir lista os requisitos para a instalação no Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrição</th></tr>
</thead>
<tbody>
<tr><td>Distribuições Linux suportadas no Azure
</td>
<td>
Linux x86 2.6 (32 bits, 64 bits)<br/>
Chapéu Vermelho 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Núcleos: 2 (mínimo)<br/>
Memória: 4 GB (mínimo)<br/>
Espaço de troca: 1 GB (mínimo)<br/>
Disco rígido: 100 GB (mínimo)<br/>
</td>
</tr>
<tr><td>Software opcional para usuários do Windows
</td>
<td>PuTTY: Usado neste guia para configurar recursos de VM<br/>
WinSCP: um cliente SFTP popular e cliente FTP que você pode usar<br/>
Eclipse para Windows: uma plataforma de desenvolvimento suportada pela TmaxSoft<br/>
(O Microsoft Visual Studio não é suportado no momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Pré-requisitos

Planeje passar alguns dias para montar todos os softwares necessários e completar todos os processos manuais.

Antes de começar, faça o seguinte:

- Obtenha a mídia de instalação OpenFrame da TmaxSoft. Se você é um cliente TmaxSoft existente, entre em contato com seu representante da TmaxSoft para obter uma cópia licenciada. Caso contrário, solicite uma versão de teste da [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Solicite a documentação do <support@tmaxsoft.com>OpenFrame enviando e-mail para .

- Obtenha uma assinatura do Azure se você ainda não tiver uma. Você também pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Opcional. Configure um túnel VPN site-to-site ou uma caixa de salto que restringe o acesso ao VM do Azure aos usuários permitidos em sua organização. Este passo não é necessário, mas é uma prática recomendada.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configure um VM no Azure para OpenFrame e Tibero

Você pode configurar o ambiente OpenFrame usando vários padrões de implantação, mas o procedimento a seguir mostra como implantar o servidor de aplicativos OpenFrame e o banco de dados Tibero em uma VM. Em ambientes maiores e para cargas de trabalho consideráveis, uma prática recomendada é implantar o banco de dados separadamente em sua própria VM para um melhor desempenho.

**Para criar uma VM**

1. Acesse o portal azure <https://portal.azure.com> e faça login na sua conta.

2. Clique em **Máquinas Virtuais**.

    ![Lista de recursos no portal Azure](media/vm-01.png)

3. Clique em **Adicionar**.

    ![Adicionar opção no portal Azure](media/vm-02.png)

4. À direita dos **sistemas operacionais,** clique em **Mais**.

     ![Mais opções no portal Azure](media/vm-03.png)

5. Clique **no 7.3 baseado no CentOS** para acompanhar exatamente esse passo a passo, ou você pode escolher outra distribuição Linux suportada.

     ![Opções do sistema operacional no portal Azure](media/vm-04.png)

6. Nas **configurações básicas,** digite **Nome,** **Nome do usuário,** **tipo de autenticação,** **Assinatura** (Pay-As-You-Go é o estilo de pagamento da AWS) e grupo **de recursos** (use um existente ou crie um grupo TmaxSoft).

7. Quando estiver concluído (incluindo o par de tecla público/privado para **o tipo de autenticação),** clique **em Enviar**.

> [!NOTE]
> Se usar uma chave pública SSH para **o tipo autenticação,** consulte as etapas na próxima seção para gerar o par de chaves público/privado e, em seguida, retome as etapas aqui.

### <a name="generate-a-publicprivate-key-pair"></a>Gerar um par de chaves públicas/privadas

Se você estiver usando um sistema operacional Windows, você precisa do PuTTYgen para gerar um par de chaves público/privado.

A chave pública pode ser compartilhada livremente, mas a chave privada deve ser mantida em segredo e nunca deve ser compartilhada com outra parte. Depois de gerar as teclas, você deve colar a **chave pública SSH** na configuração — na verdade, carregando-a para a VM Linux. Ele é armazenado\_dentro de \~chaves autorizadas dentro do diretório /.ssh do diretório inicial da conta de usuário. O Linux VM é então capaz de reconhecer e validar a conexão uma vez que você forneça a **chave privada SSH** associada no cliente SSH (no nosso caso, PuTTY).

Ao dar a novos indivíduos acesso à VM: 

- Cada novo indivíduo gera suas próprias chaves públicas/privadas usando PuTTYgen.
- Os indivíduos armazenam suas próprias chaves privadas separadamente e enviam as informações-chave públicas para o administrador da VM.
- O administrador colhe o conteúdo da \~chave pública no arquivo\_/.ssh/chaves autorizadas.
- O novo indivíduo se conecta via PuTTY.

**Para gerar um par de chaves públicas/privadas**

1.  Baixe PuTTYgen <https://www.putty.org/> e instale-o usando todas as configurações padrão.

2.  Para abrir o PuTTYgen, localize o diretório\\de\\instalação PuTTY em C: Arquivos do programa PuTTY.

    ![Interface PuTTY](media/puttygen-01.png)

3.  Clique em **Gerar**.

    ![Caixa de diálogo do gerador de chaves PuTTY](media/puttygen-02.png)

4.  Após geração, salve a chave pública e a chave privada. Cole o conteúdo da chave pública na seção **de chave pública SSH** do **painel Criar \> máquina virtual Basics** (mostrado nas etapas 6 e 7 na seção anterior).

    ![Caixa de diálogo do gerador de chaves PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurar recursos de VM

1. No portal Azure, no Escolha uma lâmina **de tamanho,** escolha as configurações de hardware da máquina Linux desejadas. Os requisitos *mínimos* para instalar tanto o Tibero quanto o OpenFrame são 2 CPUs e 4 GB de RAM, como mostrado nesta instalação de exemplo:

    ![Criar máquina virtual - Noções básicas](media/create-vm-01.png)

2. Clique **em 3 Configurações** e use as configurações padrão para configurar recursos opcionais.
3. Revise seus detalhes de pagamento.

    ![Criar máquina virtual - Comprar](media/create-vm-02.png)

4. Enviem suas seleções. O Azure começa a implantar a VM. Esse processo normalmente leva alguns minutos.

5. Quando a VM é implantada, seu painel é exibido, mostrando todas as configurações que foram selecionadas durante a configuração. Anote o **endereço IP público**.

    ![tmax no painel do Azure](media/create-vm-03.png)

6. Abra o PuTTY.

7. Para **nome de host,** digite seu nome de usuário e o endereço IP público copiado. Por exemplo, **publicip de nome de\@usuário**.

    ![Caixa de diálogo de configuração PuTTY](media/putty-01.png)

8. Na caixa **Categoria,** clique **em \> Conexão \> SSH Auth**. Forneça o caminho para o seu arquivo **de chave privada.**

    ![Caixa de diálogo de configuração PuTTY](media/putty-02.png)

9. Clique **em Abrir** para iniciar a janela PuTTY. Se for bem-sucedido, você está conectado ao seu novo Centos VM em execução no Azure.

10. Para fazer logon como usuário raiz, **digite sudo bash**.

    ![Logon de usuário raiz na janela de comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configure o ambiente e os pacotes

Agora que a VM foi criada e você está conectado, você deve executar algumas etapas de configuração e instalar os pacotes de pré-instalação necessários.

1. Mapeie o nome **da demonstração** para o endereço`vi /etc/hosts`IP local usando vi para editar o arquivo hosts (). Supondo que nosso IP seja 192.168.96.148 ofdemo, isso é antes da mudança:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Isto é depois da mudança:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Criar grupos e usuários:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Alterar a senha para oframe 7 do usuário:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Atualize os parâmetros do kernel em /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Atualize os parâmetros do kernel dinamicamente sem reinicialização:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obtenha os pacotes necessários: Certifique-se de que o servidor está conectado à Internet, baixe os seguintes pacotes e instale-os:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - Ncurses

          > [!NOTE]
          > Depois de instalar o pacote ncurses, crie os seguintes links simbólicos:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - Strace
     - Ltrace
     - gdb

7. No caso da instalação java RPM, faça o seguinte:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instale o banco de dados Tibero

O Tibero fornece as várias funções-chave no ambiente OpenFrame no Azure:

- Tibero é usado como o armazenamento de dados interno OpenFrame para várias funções do sistema.
- Os arquivos VSAM, incluindo KSDS, RRDS e ESDS, usam o banco de dados Tibero internamente para armazenamento de dados.
- O repositório de dados TACF é armazenado em Tibero.
- As informações do catálogo OpenFrame são armazenadas no Tibero.
- O banco de dados Tibero pode ser usado como um substituto para o IBM Db2 armazenar dados de aplicativos.

**Para instalar o Tibero**

1. Verifique se o arquivo instalador binário Tibero está presente e revise o número da versão.
2. Copie o software Tibero para a conta de usuário Tibero (oframe). Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Abra o\_perfil .bash em vi (`vi .bash_profile`) e cole o seguinte nele:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Para executar o perfil de bash, no tipo de prompt de comando:

    ```
    source .bash_profile
    ```

5. Gerar o arquivo de ponta (um arquivo de configuração para Tibero), em seguida, abri-lo em vi. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. \$Modifique\_o TB HOME/client/config/tbdsn.tbr e coloque 127.0.0.1 em vez de host local como mostrado:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Crie o banco de dados. A saída a seguir é exibida:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Para reciclar Tibero, primeiro desligue `tbdown` usando o comando. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Agora bota Tibero usando `tbboot`. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Para criar um espaço de tabela, acesse o banco de dados usando o usuário SYS (sys/tmax) e crie o espaço de tabela necessário para o volume padrão e o TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Agora digite os seguintes comandos SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Inicie o Tibero e verifique se os processos do Tibero estão em execução:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Saída:

![Saída de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalar o ODBC

Os aplicativos no OpenFrame se comunicam com o banco de dados Tibero usando a API ODBC fornecida pelo projeto unixODBC de código aberto.

Para instalar o ODBC:

1. Verifique se o arquivo instalador unixODBC-2.3.4.tar.gz `wget unixODBC-2.3.4.tar.gz` está presente ou use o comando. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Descompacte o binário. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navegue até o diretório unixODBC-2.3.4 e gere o Makefile usando as informações da máquina de verificação. Por exemplo: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Por padrão, unixODBC é instalado em /usr `--prefix` /local, então passa um valor para alterar a localização. Da mesma forma, os arquivos de configuração `--sysconfdir` são instalados em /etc por padrão, então passa o valor do local desejado.

4. Executar arquivo de make:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copie o arquivo executável no diretório do programa após a compilação. Por exemplo: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Use vi para editar`vi ~/.bash_profile`o perfil de bash ( ) e adicione o seguinte:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Aplique o ODBC. Edite os seguintes arquivos de acordo. Por exemplo: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Crie um link simbólico e valide a conexão do banco de dados Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

É exibida a saída a seguir:

![Saída ODBC mostrando-se conectada ao SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instale a base openframe

O servidor de aplicativo Base é instalado antes dos serviços individuais que o OpenFrame usa para gerenciar o sistema no Azure, incluindo os processos de servidor de manipulação de transações.

**Para instalar a base openframe**

1. Certifique-se de que a instalação do Tibero\_foi\_\_bem\_sucedida e verifique se o seguinte arquivo de instalação OpenFrame Base7 0 Linux x86\_64.bin e o arquivo de configuração base.properties estão presentes.

2. Atualize o perfil do bash com as seguintes informações específicas do Tibero:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Execute o perfil de bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Certifique-se de que os processos tibero estão em execução. Por exemplo: 

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Certifique-se de iniciar o Tibero antes da instalação.

5. Gerar licença em [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e colocar as licenças OpenFrame Base, Batch, TACF, OSC na pasta apropriada:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Baixe os arquivos binários e base.properties da Base OpenFrame:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Execute o instalador usando o arquivo base.properties. Por exemplo: 

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Quando concluída, a mensagem Instalação Completa é diproduzida.

8. Verifique a estrutura do diretório `ls -ltr` OpenFrame Base usando o comando. Por exemplo: 

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Iniciar base openframe:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![saída de comando tmboot](media/base-02.png)

10. Verifique se o status do processo está pronto usando o comando tmadmin em si. O RDY é exibido na coluna **de status** de cada um dos processos:

     ![saída de comando tmadmin](media/base-03.png)

11. Desligar a base openframe:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Instalar o openframe batch

OpenFrame Batch consiste em vários componentes que simulam ambientes de lote de mainframe e é usado para executar trabalhos em lote no Azure.

**Para instalar o Batch**

1. Certifique-se de que a instalação base\_foi\_bem\_sucedida\_e\_\_verifique se o arquivo de configuração do Instalador OpenFrame Batch7 0 Fix2 MVS Linux x86\_64.bin e o arquivo de configuração batch.properties estão presentes:

2. No prompt de `vi batch.properties` comando, digite para editar o arquivo batch.properties usando vi.

3. Modifique os parâmetros da seguinte forma:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Para executar o instalador de lote, no tipo de prompt de comando:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Quando a instalação estiver concluída, inicie as suítes OpenFrame instaladas digitando `tmboot` no prompt de comando.

    ![saída tmboot](media/tmboot-01.png)

6. Digite `tmadmin` no prompt de comando para verificar o processo OpenFrame.

    ![Tela de admin Tmax](media/tmadmin-01.png)

7. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Use `tmdown` o comando para iniciar e desligar o lote:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Instalar tacf

O TACF Manager é um módulo de serviço OpenFrame que controla o acesso do usuário a sistemas e recursos através da segurança RACF.

**Para instalar o TACF**

1. Verifique se o\_arquivo\_de\_configuração do Instalador OpenFrame Tacf7 0 Fix2\_Linux\_x86\_64.bin e o arquivo de configuração tacf.properties estão presentes.
2. Certifique-se de que a instalação do lote foi bem`vi tacf.properties`sucedida e use vi para abrir o arquivo tacf.properties ().
3. Modificar os parâmetros do TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Depois de concluir o instalador TACF, aplique as variáveis de ambiente TACF. No prompt de comando, digite:

     ```
     source \~/.bash\_profile
     ```

5. Execute o instalador TACF. No prompt de comando, digite:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A saída é semelhante ao seguinte:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. No prompt de `tmboot` comando, digite para reiniciar openframe. A saída é semelhante ao seguinte:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Verifique se o status `tmadmin` do `si` processo está pronto usando no comando. Por exemplo: 

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Na coluna **status,** RDY aparece:

    ![RDY na coluna de status](media/tmboot-02.png)

8. Execute os seguintes comandos:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Desligue o servidor `tmdown` usando o comando. A saída é semelhante ao seguinte:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Instalar o ProSort

ProSort é um utilitário usado em transações em lote para classificar dados.

**Para instalar o ProSort**

1. Certifique-se de que a instalação do Batch foi bem sucedida e, em seguida, verifique se o arquivo de **instalador prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** está presente.

2. Execute o instalador usando o arquivo propriedades. No prompt de comando, digite:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Mova o diretório prosort para o local de origem. No prompt de comando, digite:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Crie um subdiretório de licença e copie o arquivo de licença lá. Por exemplo: 

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Abra o bash.profile`vi .bash_profile`em vi ( ) e atualize-o da seguinte forma:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Para executar o perfil de bash, no prompt de comando, digite:`. .bash_profile`

7. Crie o arquivo de configuração. Por exemplo: 

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Crie o link simbólico. Por exemplo: 

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifique a instalação do ProSort executando o `prosort -h` comando. Por exemplo: 

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Instalar o OFCOBOL

OFCOBOL é o compilador OpenFrame que interpreta os programas COBOL do mainframe. 

**Para instalar o OFCOBOL**

1. Certifique-se de que a instalação batch/online foi\_bem\_sucedida\_e\_\_verifique se\_o arquivo instalador OpenFrame COBOL3 0 40 Linux x86 64.bin está presente.

2. Para executar o instalador OFCOBOL, no prompt de comando, digite:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leia o contrato de licenciamento e pressione Enter para continuar.

4. Aceite o contrato de licenciamento. Quando a instalação estiver concluída, aparecerão os seguintes:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Abra o perfil de`vi .bash_profile`bash em vi ( ) e verifique se está atualizado com variáveis OFCOBOL.
6. Execute o perfil do bash. No prompt de comando, digite:

     ```
      source ~/.bash_profile
     ```

7. Copie a licença OFCOBOL para a pasta instalada. Por exemplo: 
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Vá para o arquivo de configuração openFrame tjclrun.conf e abra-o em vi. Por exemplo: 
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Aqui está a seção SYSLIB antes da alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Aqui está a seção SYSLIB após a alteração:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Revise o\_arquivo\_OpenFrame COBOL InstallLog.log em vi e verifique se não há erros. Por exemplo: 
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Use `ofcob --version` o comando e revise o número da versão para verificar a instalação. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Reinicie openframe `tmdown/tmboot` usando o comando.

## <a name="install-ofasm"></a>Instalar OFASM

OFASM é o compilador OpenFrame que interpreta os programas de montagem do mainframe.

**Para instalar o OFASM**

1. Certifique-se de que a instalação Batch/Online foi\_bem\_sucedida\_\_e verifique\_se o arquivo instalador OpenFrame ASM3 0 Linux x86 64.bin está presente.

2. Execute o instalador. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leia o contrato de licenciamento e pressione Enter para continuar.
4. Aceite o contrato de licenciamento.
5. Verifique se o perfil de bash é atualizado com variáveis OFASM. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Abra o arquivo de configuração OpenFrame tjclrun.conf em vi e edite-o da seguinte forma:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Aqui está a seção [SYSLIB] *antes* da alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Aqui está a seção [SYSLIB] *após* a alteração:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Abra o\_arquivo\_OpenFrame ASM InstallLog.log em vi e verifique se não há erros. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Reinicie o OpenFrame emitindo um dos seguintes comandos:

     ```
     tmdown / tmboot
     ```

     — ou —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalar osC

OSC é o ambiente OpenFrame semelhante ao IBM CICS que suporta transações OLTP de alta velocidade e outras funções de gerenciamento.

**Para instalar o OSC**

1. Certifique-se de que a instalação base\_foi bem\_\_sucedida\_e\_verifique\_se o arquivo de configuração do Instalador OpenFrame OSC7 0 Fix2 Linux x86 64.bin e o arquivo de configuração osc.properties estão presentes.
2. Editar os seguintes parâmetros no arquivo osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Execute o instalador usando o arquivo de propriedades como mostrado:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Quando concluída, a mensagem "Instalação Completa" é exibida.

4. Verifique se o perfil de bash é atualizado com variáveis OSC.
5. Revise o\_arquivo\_OpenFrame OSC7 0\_Fix2\_InstallLog.log. O resultado deve ser algo como:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Use vi para abrir o arquivo de configuração ofsys.seq. Por exemplo: 

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nas \#seções \#BASE e BATCH, edite os parâmetros como mostrado.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Copie o arquivo da licença. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Para iniciar e desligar o OSC, inicialize a memória `osctdlinit OSCOIVP1` compartilhada da região cics digitando no prompt de comando.

10. Corra `oscboot` para inicializar o OSC. A saída é semelhante ao seguinte:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Para verificar se o status do `tmadmin` processo está pronto, use o comando em si. Todos os processos devem exibir RDY na coluna **status.**

    ![Processos exibindo RDY](media/tmadmin-02.png)

12. Desligue o OSC `oscdown` usando o comando.

## <a name="install-jeus"></a>Instalar JEUS

O JEUS (Java Enterprise User Solution) fornece a camada de apresentação do servidor de aplicativo web OpenFrame.

Antes de instalar o JEUS, instale o pacote Apache Ant, que fornece as bibliotecas e as ferramentas de linha de comando necessárias para instalar o JEUS.

**Para instalar a Formiga Apache**

1. Baixe o binário `wget` Ant usando o comando. Por exemplo: 

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Use `tar` o utilitário para extrair o arquivo binário e movê-lo para um local apropriado. Por exemplo: 

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Para eficiência, crie um link simbólico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Abra o perfil de`vi .bash_profile`bash em vi ( )e atualize-o com as seguintes variáveis:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Aplique a variável de ambiente modificada. Por exemplo: 

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Para instalar o JEUS**

1. Expanda o instalador usando o `tar` utilitário. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Crie uma pasta`mkdir jeus7` **jeus** ( ) e descompacte o binário.
3. Mude para o diretório **de configuração** (ou use o parâmetro JEUS para seu próprio ambiente). Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Execute `ant clean-all` antes de executar a compilação. A saída é semelhante ao seguinte:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Faça um backup do arquivo domain-config-template.properties. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Abra o arquivo domain-config-template.properties in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Altere `jeus.password=jeusadmin nodename=Tmaxsoft` para `jeus.password=tmax1234 nodename=ofdemo`

8. Execute `ant install` o comando para construir JEUS.
9.  Atualize o\_arquivo de perfil .bash com as variáveis JEUS como mostrado:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Execute o perfil do bash. Por exemplo: 

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Opcional*. Crie um alias para fácil desligamento e inicialização de componentes JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Para verificar a instalação, inicie o servidor de admin de domínio como mostrado:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verifique por logon da Web usando a sintaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Por exemplo, <http://192.168.92.133:9736/webadmin/login.> a tela de logon é exibida:
    
     ![Tela de logon jeus WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se você tiver algum problema com a segurança da porta, abra`systemctl stop firewall`a porta 9736 ou desative o firewall ( ).

14. Para alterar o nome de host para servidor1, clique **em Bloquear & Editar**e, em seguida, clique em **servidor1**. Na janela Servidor, altere o nome do host da seguinte forma:

    1.  Alterar **nome de nó** para **ofdemo**.
    2.  Clique em **OK** no lado direito da janela.
    3.  Clique **em Aplicar alterações** no lado esquerdo inferior da janela e, para obter a descrição, digite *'Alteração de nome de host '.*

    ![Tela JEUS WebAdmin](media/jeus-02.png)

15. Verifique se a configuração é bem sucedida na tela de confirmação.

    ![Tela do Servidor jeus_domain](media/jeus-03.png)

16. Inicie o processo de servidor gerenciado "server1" usando o seguinte comando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalação de OFGW

OFGW É o gateway OpenFrame que suporta a comunicação entre o emulador de terminal 3270 e a base OSI e gerencia as sessões entre o emulador de terminal e o OSI.

**Para instalar o OFGW**

1. Certifique-se de que o JEUS foi instalado\_com\_\_sucesso e verifique se o arquivo de instalador OFGW7 0 1 Generic.bin está presente.
2. Execute o instalador. Por exemplo: 

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Use os seguintes locais para as solicitações correspondentes:
     -   Diretório doméstico jeus
     -   Nome de domínio de JEUS
     -   Nome do servidor JEUS
     -   Tibero Driver
     -   Tmax Node ID ofdemo

4. Aceite o resto dos padrões e pressione Enter para sair do instalador.

5. Verifique se a URL do OFGW está funcionando como esperado:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     A seguinte tela aparece:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalar o OFManager

O OFManager fornece funções de operação e gerenciamento para openframe no ambiente web.

**Para instalar o OFManager**

1. Verifique se o\_arquivo do instalador OFManager7 Generic.bin está presente.
2. Execute o instalador. Por exemplo: 

     ```
     OFManager7_Generic.bin
     ```

3.  Pressione Enter para continuar e, em seguida, aceite o contrato de licença.
4.  Escolha a pasta instalar.
5.  Aceite os padrões.
6.  Escolha Tibero como banco de dados.
7.  Pressione Enter para sair do instalador.
8.  Verifique se a URL do OFManager está funcionando como esperado:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

A tela inicial é exibida:

![Tela de logon do Tmax OpenFrame Manager](media/ofmanager-01.png)

Isso completa a instalação dos componentes do OpenFrame.

## <a name="next-steps"></a>Próximas etapas

Se você está considerando uma migração de mainframe, nosso ecossistema de parceiros em expansão está disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Aliança de Modernização de Plataforma](https://datamigration.microsoft.com/).

-   [Introdução ao Azure](https://docs.microsoft.com/azure/)
-   [Documentação do Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guia de elevação e mudança do Azure Virtual Data Center](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
