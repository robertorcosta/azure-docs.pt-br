---
title: Conectar-se ao servidor SFTP com SSH
description: Automatize tarefas que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP usando o SSH e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 3370eea8909f30563babcf2a84f727ba51f67e29
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647653"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitore, crie e gerencie arquivos SFTP usando SSH e os Aplicativos Lógicos do Azure

Para automatizar tarefas que monitoram, criam, enviam e recebem arquivos em um servidor [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) usando o protocolo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), você pode criar e automatizar fluxos de trabalho de integração usando os Aplicativos Lógicos do Azure e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a arquivos, transferência de arquivos e gerenciamento de arquivos em qualquer fluxo de dados confiável. Aqui estão algumas tarefas de exemplo que você pode automatizar:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, renomear, atualizar, listar e excluir arquivos.
* Crie pastas.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e disponibilizam a saída para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usando a saída das ações do SFTP. Por exemplo, se você recuperar regularmente arquivos do servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Office 365 Outlook ou o conector Outlook.com. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

Para obter diferenças entre o conector SFTP-SSH e o conector SFTP, examine a seção [comparar SFTP-SSH versus SFTP](#comparison) mais adiante neste tópico.

## <a name="limits"></a>limites

* Por padrão, as ações de SFTP-SSH podem ler ou gravar arquivos que são de *1 GB ou menores* , mas apenas em partes de *15 MB* por vez. Para lidar com arquivos com mais de 15 MB, as ações SFTP-SSH dão suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md), exceto para a ação copiar arquivo, que pode manipular apenas 15 arquivos. A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento de mensagens.

* Os gatilhos SFTP-SSH não dão suporte ao agrupamento. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 15 MB ou menores. Para obter arquivos com mais de 15 MB, siga este padrão em vez disso:

  * Use um gatilho SFTP-SSH que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação SFTP-SSH **Get file Content** , que lê o arquivo completo e usa implicitamente o agrupamento de mensagens.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparar SFTP-SSH vs SFTP

Aqui estão outras diferenças importantes entre o conector SFTP-SSH e o conector SFTP, onde o conector SFTP-SSH tem esses recursos:

* Usa a [biblioteca SSH.net](https://github.com/sshnet/SSH.NET), que é uma biblioteca de Secure Shell de código aberto (SSH) que dá suporte ao .net.

* Por padrão, as ações de SFTP-SSH podem ler ou gravar arquivos que são de *1 GB ou menores* , mas apenas em partes de *15 MB* por vez.

  Para lidar com arquivos com mais de 15 MB, as ações do SFTP-SSH podem usar o [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). No entanto, a ação copiar arquivo dá suporte a apenas 15 arquivos, pois essa ação não dá suporte ao agrupamento de mensagens. Os gatilhos SFTP-SSH não dão suporte ao agrupamento. Para carregar arquivos grandes, você precisa de permissões de leitura e gravação para a pasta raiz em seu servidor SFTP.

* Fornece a ação **Criar pasta**, que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **Renomear arquivo**, que renomeia um arquivo no servidor SFTP.

* Armazena em cache a conexão com o servidor SFTP *por até 1 hora*, o que melhora o desempenho e reduz o número de tentativas de conexão com o servidor. Para definir a duração desse comportamento de armazenamento em cache, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração do SSH em seu servidor SFTP.

## <a name="prerequisites"></a>Prerequisites

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor SFTP e credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Você também precisa acessar uma chave privada SSH e a senha da chave privada SSH. Para usar o Agrupamento ao carregar arquivos grandes, você precisará de permissões de leitura e gravação para a pasta raiz em seu servidor SFTP. Caso contrário, você receberá um erro "401 não autorizado".

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH suporta *somente* estes formatos de chave privada, algoritmos e impressões digitais:
  >
  > * **Formatos de chave privada**: chaves RSA (Rivest Shamir Adleman) e DSA (algoritmo de assinatura digital) nos formatos OpenSSH e SSH.com. Se sua chave privada estiver no formato de arquivo de reversões (. PPK), primeiro [converta a chave para o formato de arquivo OpenSSH (. pem)](#convert-to-openssh).
  >
  > * **Algoritmos de criptografia**: EDE3-DES-CBC, EDE3 CFB DES, DES-CBC, CBC de AES-128, AES-192-CBC e AES-256-CBC
  >
  > * **Impressão digital**: MD5
  >
  > Depois de adicionar o gatilho SFTP-SSH ou a ação que você deseja ao seu aplicativo lógico, você precisa fornecer informações de conexão para seu servidor SFTP. Ao fornecer sua chave privada SSH para essa conexão, ***não insira ou edite manualmente a chave***, o que pode causar falha na conexão. Em vez disso, certifique-se de ***copiar a chave*** de seu arquivo de chave privada SSH e ***Cole*** essa chave nos detalhes da conexão. 
  > Para obter mais informações, consulte a seção [conectar-se ao SFTP com SSH](#connect) posteriormente neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta SFTP. Para começar com um acionador SFTP-SSH, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação SFTP-SSH, inicie seu aplicativo lógico com outro acionador, por exemplo, o acionador **Recorrência**.

## <a name="how-sftp-ssh-triggers-work"></a>Como os gatilhos de SFTP-SSH funcionam

SFTP-os gatilhos SSH funcionam sondando o sistema de arquivos SFTP e procurando por qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Vá para **opções** > **preferências** > **transferir** > **editar** > **Preservar o carimbo de hora** > **desabilitar** |
| FileZilla | Ir para **Transferir** > **Preservar registros de data e hora de arquivos transferidos** > **Desativar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter chave baseada em saída para OpenSSH

Se sua chave privada estiver no formato de reversões, que usa a extensão de nome de arquivo. PPK (chave privada de saída), primeiro converta a chave para o formato OpenSSH, que usa a extensão de nome de arquivo. PEM (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operacional baseado em UNIX

1. Se as ferramentas de saída ainda não estiverem instaladas no sistema, faça isso agora, por exemplo:

   `sudo apt-get install -y putty`

1. Execute este comando, que cria um arquivo que você pode usar com o conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por exemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Sistema operacional Windows

1. Se você ainda não fez isso, [Baixe a ferramenta mais recente do gerador de geração (puttygen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e, em seguida, inicie a ferramenta.

1. Nessa tela, selecione **carregar**.

   ![Selecione "carregar"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navegue até o arquivo de chave privada no formato de saída e selecione **abrir**.

1. No menu **conversões** , selecione **Exportar chave OpenSSH**.

   ![Selecione "Exportar chave OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salve o arquivo de chave privada com a extensão de nome de arquivo `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Conectar-se ao SFTP com SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "sftp ssh" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação, escolha **Nova etapa**. Na caixa de pesquisa, digite "sftp ssh" como seu filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT]
   >
   > Ao inserir sua chave privada SSH na propriedade **chave privada SSH**, siga estas etapas adicionais, que ajudam a fornecer o valor completo e correto para essa propriedade. Uma chave inválida faz com que a conexão falhe.

   Embora você possa usar qualquer editor de texto, aqui estão etapas de exemplo que mostram como copiar e colar corretamente a chave usando o Notepad.exe como exemplo.

   1. Abra seu arquivo de chave privada SSH em um editor de texto. Essas etapas usam o bloco de notas do exemplo.

   1. No menu **Editar** do bloco de notas, selecione **selecionar tudo**.

   1. Selecione **Editar** > **Copiar**.

   1. No acionador ou ação SFTP-SSH que você adicionou, cole a chave *completa* que você copiou na propriedade **chave privada SSH**, que suporta várias linhas.  ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando você terminar inserindo os detalhes de conexão, escolha **criar**.

1. Agora, forneça os detalhes necessários para o acionador ou a ação selecionada e continue criando o fluxo de trabalho do seu aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Disparador SSH: quando um arquivo é adicionado ou modificado

Esse acionador inicia um fluxo de trabalho de aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP.

**Exemplo corporativo**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Em seguida, você pode usar uma ação de SFTP, como **Obter conteúdo de arquivo**, para obter o conteúdo do pedido para processamento posterior e armazenar esse pedido em um banco de dados de pedidos.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-ação SSH: obter conteúdo usando o caminho

Esta ação obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
