---
title: Conecte-se ao servidor SFTP com SSH
description: Automatize tarefas que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP usando o SSH e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/13/2020
tags: connectors
ms.openlocfilehash: d7fafdd5830ec2825771d4d611a5f4bd5d87260a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393628"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitore, crie e gerencie arquivos SFTP usando SSH e os Aplicativos Lógicos do Azure

Para automatizar tarefas que monitoram, criam, enviam e recebem arquivos em um servidor [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) usando o protocolo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), você pode criar e automatizar fluxos de trabalho de integração usando os Aplicativos Lógicos do Azure e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a arquivos, transferência de arquivos e gerenciamento de arquivos em qualquer fluxo de dados confiável. Aqui estão algumas tarefas de exemplo que você pode automatizar:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, renomear, atualizar, listar e excluir arquivos.
* Crie pastas.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e disponibilizam a saída para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usando a saída das ações do SFTP. Por exemplo, se você recuperar regularmente arquivos do servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Office 365 Outlook ou o conector Outlook.com. Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Para obter diferenças entre o conector SFTP-SSH e o conector SFTP, revise a seção [Compare SFTP-SSH versus SFTP](#comparison) mais tarde neste tópico.

## <a name="limits"></a>limites

* As ações do SFTP-SSH que [suportam o chunking](../logic-apps/logic-apps-handle-large-messages.md) podem lidar com arquivos de até 1 GB, enquanto as ações SFTP-SSH que não suportam o chunking podem lidar com arquivos de até 50 MB. Embora o tamanho do pedaço padrão seja de 15 MB, esse tamanho pode mudar dinamicamente, começando de 5 MB e aumentando gradualmente para o máximo de 50 MB, com base em fatores como latência da rede, tempo de resposta do servidor e assim por diante.

  > [!NOTE]
  > Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Você pode substituir esse comportamento adaptativo quando [especificar um tamanho de pedaço constante](#change-chunk-size) para usar. Este tamanho pode variar de 5 MB a 50 MB. Por exemplo, suponha que você tenha um arquivo de 45 MB e uma rede que possa suportar esse tamanho de arquivo sem latência. O empequenos adaptativos resulta em várias chamadas, mas sim em uma chamada. Para reduzir o número de chamadas, você pode tentar definir um tamanho de pedaço de 50 MB. Em diferentes cenários, se o seu aplicativo lógico está cronometrando, por exemplo, ao usar pedaços de 15 MB, você pode tentar reduzir o tamanho para 5 MB.

  O tamanho do pedaço está associado a uma conexão, o que significa que você pode usar a mesma conexão para ações que suportam o emaçamento e, em seguida, para ações que não suportam o emaçamento. Neste caso, o tamanho do pedaço para ações que não suportam fatias varia de 5 MB a 50 MB. Esta tabela mostra quais ações do SFTP-SSH suportam o emalargamento:

  | Ação | Suporte de parcelamento | Suporte de tamanho de pedaço de substituição |
  |--------|------------------|-----------------------------|
  | **Copiar arquivo** | Não | Não aplicável |
  | **Criar arquivo** | Sim | Sim |
  | **Criar pasta** | Não aplicável | Não aplicável |
  | **Excluir arquivo** | Não aplicável | Não aplicável |
  | **Extrair o arquivo morto para a pasta** | Não aplicável | Não aplicável |
  | **Obter conteúdo do arquivo** | Sim | Sim |
  | **Obter o conteúdo do arquivo usando o caminho** | Sim | Sim |
  | **Obter metadados de arquivos** | Não aplicável | Não aplicável |
  | **Obtenha metadados de arquivos usando o caminho** | Não aplicável | Não aplicável |
  | **Listar arquivos na pasta** | Não aplicável | Não aplicável |
  | **Renomear arquivo** | Não aplicável | Não aplicável |
  | **Atualizar arquivo** | Não | Não aplicável |
  ||||

* Os gatilhos SFTP-SSH não suportam o acúmulo de mensagens. Ao solicitar conteúdo de arquivo, os gatilhos selecionam apenas arquivos de 15 MB ou menores. Para obter arquivos maiores que 15 MB, siga este padrão em vez disso:

  1. Use um gatilho SFTP-SSH que retorna apenas propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (apenas propriedades)**.

  1. Siga o gatilho com a ação de conteúdo do arquivo SFTP-SSH **Get,** que lê o arquivo completo e usa implicitamente o compartilhamento de mensagens.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparar SFTP-SSH vs SFTP

Aqui estão outras diferenças importantes entre o conector SFTP-SSH e o conector SFTP, onde o conector SFTP-SSH tem esses recursos:

* Usa a [biblioteca SSH.NET](https://github.com/sshnet/SSH.NET), que é uma biblioteca SSH (Secure Shell) de código aberto que suporta .NET.

* Fornece a ação **Criar pasta**, que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **Renomear arquivo**, que renomeia um arquivo no servidor SFTP.

* Armazena em cache a conexão com o servidor SFTP *por até 1 hora*, o que melhora o desempenho e reduz o número de tentativas de conexão com o servidor. Para definir a duração desse comportamento de armazenamento em cache, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração do SSH em seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor SFTP e credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Você também precisa acessar uma chave privada SSH e a senha da chave privada SSH. Para usar o chunking ao carregar arquivos grandes, você precisa de permissões de leitura e gravação para a pasta raiz em seu servidor SFTP. Caso contrário, você recebe um erro "401 Não Autorizado".

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH suporta *somente* estes formatos de chave privada, algoritmos e impressões digitais:
  >
  > * **Formatos de chave privada : teclas**RSA (Rivest Shamir Adleman) e DSA (Digital Signature Algorithm) em ambos os formatos OpenSSH e ssh.com. Se a sua chave privada estiver no formato de arquivo PuTTY (.ppk), [primeiro converta a chave para o formato de arquivo OpenSSH (.pem).](#convert-to-openssh)
  >
  > * **Algoritmos de criptografia**: EDE3-DES-CBC, EDE3 CFB DES, DES-CBC, CBC de AES-128, AES-192-CBC e AES-256-CBC
  >
  > * **Impressão digital**: MD5
  >
  > Depois de adicionar o gatilho ou ação SFTP-SSH que você deseja ao seu aplicativo lógico, você tem que fornecer informações de conexão para o seu servidor SFTP. Quando você fornecer sua chave privada SSH para essa conexão, ***não digite ou edite manualmente a chave, o***que pode causar a falha da conexão. Em vez disso, certifique-se de ***copiar a chave*** do arquivo de chave privada SSH e ***cole*** essa chave nos detalhes da conexão. 
  > Para obter mais informações, consulte a [seção Conectar-se ao SFTP com a](#connect) seção SSH mais tarde neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta SFTP. Para começar com um acionador SFTP-SSH, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação SFTP-SSH, inicie seu aplicativo lógico com outro acionador, por exemplo, o acionador **Recorrência**.

## <a name="how-sftp-ssh-triggers-work"></a>Como funcionam os gatilhos do SFTP-SSH

O SFTP-SSH aciona o trabalho pesquisando o sistema de arquivos SFTP e procurando por qualquer arquivo que tenha sido alterado desde a última pesquisa. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Ir para **opções** > **preferências** > **transferir edição** > **Edit** > Preservar carimbo de**tempo** > **Desativar** |
| FileZilla | Ir para **Transferir** > **Preservar carimbos de tempo de arquivos** > transferidos**Desativar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter a chave baseada em PuTTY para OpenSSH

Se a sua chave privada estiver no formato PuTTY, que usa a extensão de nome de arquivo .ppk (PuTTY Private Key), primeiro converta a chave para o formato OpenSSH, que usa a extensão de nome do arquivo .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operacional baseado em Unix

1. Se as ferramentas PuTTY ainda não estão instaladas no seu sistema, faça isso agora, por exemplo:

   `sudo apt-get install -y putty`

1. Execute este comando, que cria um arquivo que você pode usar com o conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por exemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Sistema operacional Windows

1. Se você ainda não fez isso, [baixe a ferramenta mais recente do PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e, em seguida, inicie a ferramenta.

1. Nesta tela, selecione **'Carregar**'

   ![Selecione "Carregar"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navegue até seu arquivo de chave privada no formato PuTTY e selecione **Abrir**.

1. No menu **Conversões,** selecione **Exportar a tecla OpenSSH**.

   ![Selecione "Exportar chave OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salve o arquivo de `.pem` chave privada com a extensão do nome do arquivo.

## <a name="considerations"></a>Considerações

Esta seção descreve considerações a serem revisadas para os gatilhos e ações deste conector.

<a name="create-file"></a>

### <a name="create-file"></a>Criar arquivo

Para criar um arquivo em seu servidor SFTP, você pode usar a ação de arquivo SFTP-SSH **Create.** Quando essa ação cria o arquivo, o serviço Logic Apps também liga automaticamente para o servidor SFTP para obter os metadados do arquivo. No entanto, se você mover o arquivo recém-criado antes que o serviço `404` Logic Apps `'A reference was made to a file or folder which does not exist'`possa fazer a chamada para obter os metadados, você receberá uma mensagem de erro, . Para pular a leitura dos metadados do arquivo após a criação do arquivo, siga as etapas para [adicionar e definir a propriedade Obter todos os **metadados do arquivo** como **No**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Conectar-se ao SFTP com SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos de lógica em branco, na caixa de pesquisa, digite `sftp ssh` como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para aplicativos lógicos existentes, sob a última etapa em que você deseja adicionar uma ação, selecione **Nova etapa**. Na caixa de pesquisa, insira `sftp ssh` como o filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT]
   >
   > Ao inserir sua chave privada SSH na propriedade **chave privada SSH**, siga estas etapas adicionais, que ajudam a fornecer o valor completo e correto para essa propriedade. Uma chave inválida faz com que a conexão falhe.

   Embora você possa usar qualquer editor de texto, aqui estão etapas de exemplo que mostram como copiar e colar corretamente a chave usando o Notepad.exe como exemplo.

   1. Abra seu arquivo de chave privada SSH em um editor de texto. Essas etapas usam o bloco de notas do exemplo.

   1. No menu **Editar** bloco de notas, **selecione Selecionar Tudo**.

   1. Selecione **Editar** > **cópia**.

   1. No acionador ou ação SFTP-SSH que você adicionou, cole a chave *completa* que você copiou na propriedade **chave privada SSH**, que suporta várias linhas.  ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando terminar de inserir os detalhes da conexão, selecione **Criar**.

1. Agora, forneça os detalhes necessários para o acionador ou a ação selecionada e continue criando o fluxo de trabalho do seu aplicativo lógico.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Tamanho do pedaço de substituição

Para substituir o comportamento adaptativo padrão que o chunking usa, você pode especificar um tamanho de pedaço constante de 5 MB a 50 MB.

1. No canto superior direito da ação, selecione o botão elipses **(...**) e, em seguida, selecione **Configurações**.

   ![Abrir configurações SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Em **Transferência de Conteúdo,** na propriedade **Tamanho chunk,** digite um valor inteiro de `5` para `50`, por exemplo: 

   ![Especificar o tamanho do pedaço para usar em vez disso](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Quando tiver terminado, selecione **Concluído**.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Disparador SSH: quando um arquivo é adicionado ou modificado

Esse acionador inicia um fluxo de trabalho de aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP.

**Exemplo corporativo**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Em seguida, você pode usar uma ação de SFTP, como **Obter conteúdo de arquivo**, para obter o conteúdo do pedido para processamento posterior e armazenar esse pedido em um banco de dados de pedidos.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - Ação SSH: Obtenha conteúdo de arquivo usando o caminho

Essa ação obtém o conteúdo de um arquivo em um servidor SFTP especificando o caminho do arquivo. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Esta seção descreve possíveis soluções para erros ou problemas comuns.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>Erro 404: "Uma referência foi feita a um arquivo ou pasta que não existe"

Esse erro pode acontecer quando seu aplicativo lógico cria um novo arquivo em seu servidor SFTP através da ação de arquivo SFTP-SSH **Create,** mas o arquivo recém-criado é imediatamente movido antes que o serviço Logic Apps possa obter os metadados do arquivo. Quando seu aplicativo de lógica executa a ação **criar arquivos,** o serviço Logic Apps também liga automaticamente para o servidor SFTP para obter os metadados do arquivo. No entanto, se o arquivo for movido, o serviço Logic `404` Apps não poderá mais encontrar o arquivo para que você receba a mensagem de erro.

Se você não puder evitar ou atrasar a movimentação do arquivo, você pode pular a leitura dos metadados do arquivo após a criação do arquivo, seguindo estas etapas:

1. Na ação **Criar arquivo,** abra a lista **Adicionar novo parâmetro,** selecione a propriedade **Obter todos os metadados de arquivo** e defina o valor como **Não**.

1. Se você precisar deste arquivo metadados mais tarde, você pode usar a ação **Obter metadados de arquivo.**

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
