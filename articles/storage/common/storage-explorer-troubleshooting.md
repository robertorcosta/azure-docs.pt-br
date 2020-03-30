---
title: Guia de solução de problemas do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral das técnicas de depuração do Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: aec8048c7ef2eb0d944cdd2a863e23578f4f87e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561673"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas do Gerenciador de Armazenamento do Azure

O Microsoft Azure Storage Explorer é um aplicativo autônomo que facilita o trabalho com dados de armazenamento do Azure no Windows, macOS e Linux. O aplicativo pode se conectar a contas de armazenamento hospedadas no Azure, nuvens nacionais e Azure Stack.

Este guia resume as soluções para problemas que são comumente vistos no Storage Explorer.

## <a name="rbac-permissions-issues"></a>Problemas de permissões do RBAC

O [RBAC,](https://docs.microsoft.com/azure/role-based-access-control/overview) com controle de acesso baseado em função, permite um gerenciamento de acesso altamente granular dos recursos do Azure, combinando conjuntos de permissões em _funções_. Aqui estão algumas estratégias para fazer o RBAC funcionar de forma ideal no Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Como acessar meus recursos no Storage Explorer?

Se você está tendo problemas para acessar recursos de armazenamento através do RBAC, você pode não ter sido atribuído as funções apropriadas. As seções a seguir descrevem as permissões que o Storage Explorer atualmente requer para acesso aos recursos de armazenamento. Entre em contato com o administrador da conta do Azure se não tiver certeza se tem as funções ou permissões apropriadas.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"Leia: Lista/Obter conta de armazenamento(s)" emissão de permissões

Você deve ter permissão para listar contas de armazenamento. Para obter essa permissão, você deve ser designado para a função _Leitor._

#### <a name="list-storage-account-keys"></a>Listar chaves da conta de armazenamento

O Storage Explorer também pode usar as chaves da conta para autenticar solicitações. Você pode ter acesso às chaves da conta através de funções mais poderosas, como a função _Contribuinte._

> [!NOTE]
> As chaves de acesso concedem permissões irrestritas a qualquer um que as detenha. Portanto, não recomendamos que você distribua essas chaves para os usuários da conta. Se você precisar revogar as chaves de acesso, você pode regenerá-las a partir do [portal Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Funções de dados

Você deve ser designado pelo menos uma função que concede acesso à leitura de dados de recursos. Por exemplo, se você quiser listar ou baixar blobs, você precisará pelo menos da função _Leitor de Dados blob de armazenamento._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Por que preciso de uma função de camada de gerenciamento para ver meus recursos no Storage Explorer?

O Azure Storage tem duas camadas de acesso: _gerenciamento_ e _dados._ Assinaturas e contas de armazenamento são acessadas através da camada de gerenciamento. Contêineres, blobs e outros recursos de dados são acessados através da camada de dados. Por exemplo, se você quiser obter uma lista de suas contas de armazenamento do Azure, envie uma solicitação para o ponto final do gerenciamento. Se você quiser uma lista de recipientes blob em uma conta, você envia uma solicitação para o ponto final de serviço apropriado.

As funções RBAC podem conter permissões para o gerenciamento ou acesso à camada de dados. A função Leitor, por exemplo, concede acesso somente à leitura aos recursos da camada de gerenciamento.

Estritamente falando, a função Reader não fornece permissões de camada de dados e não é necessária para acessar a camada de dados.

O Storage Explorer facilita o acesso aos seus recursos coletando as informações necessárias para se conectar aos recursos do Azure. Por exemplo, para exibir seus recipientes blob, o Storage Explorer envia uma solicitação de "contêineres de lista" para o ponto final do serviço blob. Para obter esse ponto final, o Storage Explorer pesquisa a lista de assinaturas e contas de armazenamento a que você tem acesso. Para encontrar suas assinaturas e contas de armazenamento, o Storage Explorer também precisa ter acesso à camada de gerenciamento.

Se você não tiver uma função que conceda quaisquer permissões de camada de gerenciamento, o Storage Explorer não poderá obter as informações de que precisa para se conectar à camada de dados.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>E se eu não conseguir as permissões de camada de gerenciamento que preciso do meu administrador?

No momento, não temos uma solução relacionada ao RBAC para este problema. Como solução de solução, você pode solicitar um Uri SAS para [anexar ao seu recurso](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-built-in-rbac-roles"></a>Funções RBAC incorporadas recomendadas

Existem várias funções de RBAC incorporadas que podem fornecer as permissões necessárias para usar o Storage Explorer. Alguns desses papéis são:
- [Proprietário](/azure/role-based-access-control/built-in-roles#owner): Gerencie tudo, inclusive acesso a recursos. **Nota**: esta função lhe dará acesso chave.
- [Contribuinte](/azure/role-based-access-control/built-in-roles#contributor): Gerencie tudo, excluindo o acesso aos recursos. **Nota**: esta função lhe dará acesso chave.
- [Leitor](/azure/role-based-access-control/built-in-roles#reader): Leia e liste recursos.
- [Contribuinte da conta de armazenamento](/azure/role-based-access-control/built-in-roles#storage-account-contributor): Gerenciamento completo das contas de armazenamento. **Nota**: esta função lhe dará acesso chave.
- [Armazenamento Blob Data Owner](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): Acesso total aos contêineres e dados do Azure Storage Blob.
- [Armazenamento Blob Data Contributor](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): Leia, escreva e exclua recipientes e blobs do Azure Storage.
- [Armazenamento Blob Data Reader](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): Leia e liste recipientes e blobs do Azure Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado auto-assinado na cadeia de certificados (e erros semelhantes)

Erros de certificado geralmente ocorrem em uma das seguintes situações:

- O aplicativo é conectado por meio de um _proxy transparente,_ o que significa que um servidor (como o servidor da empresa) está interceptando tráfego HTTPS, descriptografando-o e, em seguida, criptografando-o usando um certificado auto-assinado.
- Você está executando um aplicativo que está injetando um certificado SSL auto-assinado nas mensagens HTTPS que você recebe. Exemplos de aplicativos que injetam certificados incluem software de inspeção de tráfego de rede e antivírus.

Quando o Storage Explorer vê um certificado auto-assinado ou não confiável, ele não sabe mais se a mensagem HTTPS recebida foi alterada. Se você tiver uma cópia do certificado auto-assinado, você pode instruir o Storage Explorer a confiar nele seguindo estas etapas:

1. Obtenha uma cópia X.509 (.cer) codificada da Base-64.
2. Vá para **Editar** > **certificados SSL Certificados** > **de importação**e, em seguida, use o seletor de arquivos para encontrar, selecionar e abrir o arquivo .cer.

Esse problema também pode ocorrer se houver vários certificados (raiz e intermediário). Para corrigir esse erro, ambos os certificados devem ser adicionados.

Se você não tem certeza de onde o certificado está vindo, siga estas etapas para encontrá-lo:

1. Instale o OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Qualquer uma das versões de luz deve ser suficiente.
    * Mac e Linux: Devem ser incluídos no seu sistema operacional.
2. Run OpenSSL.
    * Windows: Abra o diretório de instalação, selecione **/bin/** e clique duas vezes **em openssl.exe**.
    * Mac e Linux: Corra `openssl` de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`.
4. Procurar certificados autoassinados. Se você não tem certeza de quais certificados são auto-assinados, anote em qualquer lugar que o assunto `("s:")` e o emissor `("i:")` sejam os mesmos.
5. Quando você encontrar certificados auto-assinados, para cada um, copie `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` e cole tudo desde (e incluindo) até um novo arquivo .cer.
6. Abra o Storage Explorer e vá para **editar** > **certificados SSL Certificados** > **de importação .** Em seguida, use o seletor de arquivos para encontrar, selecionar e abrir os arquivos .cer que você criou.

Se você não conseguir encontrar nenhum certificado auto-assinado seguindo essas etapas, entre em contato conosco através da ferramenta de feedback. Você também pode abrir o Storage Explorer `--ignore-certificate-errors` a partir da linha de comando usando o sinalizador. Quando aberto com este sinalizador, o Storage Explorer ignora erros de certificado.

## <a name="sign-in-issues"></a>Problemas de credenciais

### <a name="blank-sign-in-dialog-box"></a>Caixa de diálogo de login em branco

As caixas de diálogo de login em branco geralmente ocorrem quando o Active Directory Federation Services (AD FS) solicita ao Storage Explorer para executar um redirecionamento, que não é suportado pela Electron. Para contornar esse problema, você pode tentar usar o Fluxo de Código do Dispositivo para o login. Para fazer isso, siga estas etapas:

1. Na barra de ferramentas vertical esquerda, abra **configurações**. No Painel de Configurações, vá para**Login de** **aplicativo** > . Habilitar **o login de fluxo de fluxo de código do dispositivo**.
2. Abra a caixa de diálogo **Conectar** (seja através do ícone de plug na barra vertical do lado esquerdo ou selecionando **Adicionar conta** no painel da conta).
3. Escolha o ambiente para o qual deseja entrar.
4. Selecione **Entrar**.
5. Siga as instruções no painel seguinte.

Se você não puder fazer login na conta que deseja usar porque seu navegador padrão já está conectado a uma conta diferente, faça um dos seguintes:

- Copiar manualmente o link e o código para uma sessão privada do seu navegador.
- Copiar manualmente o link e o código para um navegador diferente.

### <a name="reauthentication-loop-or-upn-change"></a>Loop de reautenticação ou alteração de UPN

Se você estiver em um loop de reautenticação ou tiver alterado o UPN de uma de suas contas, siga estas etapas:

1. Remova todas as contas e feche o Storage Explorer.
2. Exclua a pasta .IdentityService do seu computador. No Windows, a pasta está localizada em `C:\users\<username>\AppData\Local`. Para Mac e Linux, você pode encontrar a pasta na raiz do seu diretório de usuário.
3. Se você estiver executando Mac ou Linux, você também precisará excluir a entrada Microsoft.Developer.IdentityService do armazenamento de chaves do seu sistema operacional. No Mac, o keystore é o aplicativo *Gnome Keychain.* No Linux, o aplicativo é tipicamente chamado _de Keyring,_ mas o nome pode diferir dependendo da sua distribuição.

### <a name="conditional-access"></a>Acesso Condicional

Devido a uma limitação na Biblioteca AD do Azure usada pelo Storage Explorer, o Conditional Access não é suportado quando o Storage Explorer está sendo usado no Windows 10, Linux ou macOS.

## <a name="mac-keychain-errors"></a>Erros de conjunto de chaves do Mac

O chaveiro macOS às vezes pode inserir um estado que causa problemas para a biblioteca de autenticação do Storage Explorer. Para tirar o Chaveiro deste estado, siga estas etapas:

1. Feche o Gerenciador de Armazenamento.
2. Abra o Chaveiro (pressione Command+Spacebar, **digite keychain**e pressione Enter).
3. Selecione o chaveiro "login".
4. Selecione o ícone de cadeado para bloquear o chaveiro. (O cadeado aparecerá bloqueado quando o processo estiver concluído. Pode levar alguns segundos, dependendo dos aplicativos abertos).

    ![Ícone de cadeado](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Abra o Explorer do Armazenamento.
6. Você é solicitado com uma mensagem como "O centro de serviço quer acessar o Chaveiro". Digite a senha da sua conta de administração mac e selecione **Sempre Permitir** (ou **Permitir** se **sempre permitir** não estiver disponível).
7. Tente entrar.

### <a name="general-sign-in-troubleshooting-steps"></a>Etapas gerais de solução de problemas de entrada

* Se você estiver no macOS e a janela de login nunca aparecer na caixa de diálogo **Esperando autenticação,** tente [essas etapas](#mac-keychain-errors).
* Reiniciar o Explorador de Armazenamento.
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Certifique-se de que as configurações de proxy e certificado estão configuradas corretamente para a máquina e para o Storage Explorer.
* Se você está executando o Windows e tem acesso ao Visual Studio 2019 na mesma máquina e nas credenciais de login, tente entrar no Visual Studio 2019. Depois de um login bem-sucedido no Visual Studio 2019, você pode abrir o Storage Explorer e ver sua conta no painel da conta.

Se nenhum desses métodos funcionar, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Assinaturas ausentes e locatários desfeitos

Se você não conseguir recuperar suas assinaturas depois de fazer login com sucesso, tente os seguintes métodos de solução de problemas:

* Verifique se sua conta tem acesso às assinaturas que você espera. Você pode verificar seu acesso fazendo login no portal para o ambiente Azure que você está tentando usar.
* Certifique-se de ter feito o adesão através do ambiente Azure correto (Azure, Azure China 21Vianet, Azure Germany, Azure US Government ou Custom Environment).
* Se você estiver atrás de um servidor proxy, certifique-se de configurar o proxy do Explorador de armazenamento corretamente.
* Tente remover e readicionar a conta.
* Se houver um link "Mais informações", verifique quais mensagens de erro estão sendo relatadas para os inquilinos que estão falhando. Se você não tem certeza de como responder às mensagens de erro, sinta-se livre para [abrir um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Não é possível remover uma conta anexada ou recurso de armazenamento

Se você não puder remover uma conta anexada ou recurso de armazenamento através da ui, você pode excluir manualmente todos os recursos anexados excluindo as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS - `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Feche o Storage Explorer antes de excluir essas pastas.

> [!NOTE]
> Se você já importou algum certificado SSL, faça `certs` backup do conteúdo do diretório. Posteriormente, você pode usar o backup para reimportar os certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

Primeiro, certifique-se de que as seguintes informações inseridas estão corretas:

* A URL do proxy e o número da porta
* Nome de usuário e senha se o proxy exigir

> [!NOTE]
> O Storage Explorer não suporta arquivos de configuração automática proxy para configurar configurações de proxy.

### <a name="common-solutions"></a>Soluções comuns

Se você ainda estiver enfrentando problemas, tente os seguintes métodos de solução de problemas:

* Se você puder se conectar à internet sem usar seu proxy, verifique se o Storage Explorer funciona sem configurações de proxy ativadas. Se esse for o caso, talvez haja um problema com as configurações de proxy. Trabalhe com o administrador para identificar os problemas.
* Verifique se outros aplicativos que usam o servidor proxy funcionam como esperado.
* Verifique se você pode se conectar ao portal para o ambiente Azure que você está tentando usar.
* Verifique se que você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, você deve receber InvalidQueryParameterValue ou uma resposta XML semelhante.
* Se outra pessoa também estiver usando o Gerenciador de Armazenamento com o servidor proxy, verifique se eles podem se conectar. Se eles puderem, você pode ter que entrar em contato com o seu admin servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se você tem ferramentas de rede, como o Fiddler for Windows, você pode diagnosticar os problemas da seguinte forma:

* Se você tiver que trabalhar por meio do proxy, será necessário configurar a ferramenta de rede para se conectar por meio do proxy.
* Verifique o número da porta usado por sua ferramenta de rede.
* Insira a URL do host local e o número de porta da ferramenta de rede como configurações de proxy no Gerenciador de Armazenamento. Quando você faz isso corretamente, sua ferramenta de rede inicia o registro de solicitações de rede feitas pelo Storage Explorer para gerenciamento e pontos finais de serviço. Por exemplo, `https://cawablobgrs.blob.core.windows.net/` digite o ponto final do blob em um navegador e você receberá uma resposta que se assemelha ao seguinte:

  ![Exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Essa resposta sugere que o recurso existe, mesmo que você não possa acessá-lo.

### <a name="contact-proxy-server-admin"></a>Entre em contato com o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, você pode ter que entrar em contato com o admin do servidor proxy para:

* Certifique-se de que seu proxy não bloqueie o tráfego para o gerenciamento do Azure ou pontos finais de recursos.
* Verifique o protocolo de autenticação usado por seu servidor proxy. O Storage Explorer não suporta atualmente proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é Possível Recuperar Filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações de proxy estão corretas. Se lhe foi concedido acesso a um recurso do proprietário da assinatura ou conta, verifique se você leu ou listou permissões para esse recurso.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>A seqüência de conexões não tem configurações completas

Se você receber esta mensagem de erro, é possível que você não tenha as permissões necessárias para obter as chaves da sua conta de armazenamento. Para confirmar que esse é o caso, acesse o portal e localize sua conta de armazenamento. Você pode fazer isso clicando com o botão direito do mouse no nó da sua conta de armazenamento e selecionando **Abrir no Portal**. Em seguida, vá para a lâmina **das teclas de acesso.** Se você não tiver permissões para visualizar as chaves, você verá uma mensagem "Você não tem acesso". Para contornar esse problema, você pode obter a chave da conta de outra pessoa e anexar através do nome e da chave, ou pode pedir um SAS para a conta de armazenamento e usá-la para anexar a conta de armazenamento.

Se você ver as chaves da conta, faça um problema no GitHub para que possamos ajudá-lo a resolver o problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Ocorreu um erro ao adicionar nova conexão: TypeError: Não é possível ler a 'versão' da propriedade de indefinida

Se você receber esta mensagem de erro quando tentar adicionar uma conexão personalizada, os dados de conexão armazenados no gerenciador de credenciais local podem estar corrompidos. Para contornar esse problema, tente excluir suas conexões locais corrompidas e, em seguida, adicioná-las novamente:

1. Inicie o Storage Explorer. No menu, vá para **Ajudar** > **Alternar ferramentas de desenvolvedor**.
2. Na janela aberta, na guia **Aplicativo,** vá para **o > de armazenamento local** (lado esquerdo) **file://**.
3. Dependendo do tipo de conexão com a que você está tendo um problema, procure sua chave e, em seguida, copie seu valor em um editor de texto. O valor é uma matriz de seus nomes de conexão personalizados, como o seguinte:
    * Contas de armazenamento
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Contêineres de blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Compartilhamentos de arquivos
        * `StorageExplorer_CustomConnections_Files_v1`
    * Filas
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelas
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Depois de salvar os nomes de conexão `[]`atuais, defina o valor em Ferramentas de desenvolvedor para .

Se você quiser preservar as conexões que não estão corrompidas, você pode usar as seguintes etapas para localizar as conexões corrompidas. Se você não se importa em perder todas as conexões existentes, você pode pular essas etapas e seguir as instruções específicas da plataforma para limpar seus dados de conexão.

1. A partir de um editor de texto, adicione novamente cada nome de conexão às Ferramentas do Desenvolvedor e, em seguida, verifique se a conexão ainda está funcionando.
2. Se uma conexão está funcionando corretamente, ela não está corrompida e você pode deixá-la lá com segurança. Se uma conexão não estiver funcionando, remova seu valor das Ferramentas do Desenvolvedor e registre-a para que você possa adicioná-la de volta mais tarde.
3. Repita até examinar todas as suas conexões.

Depois de passar por todas as suas conexões, para todos os nomes de conexões que não foram adicionados de volta, você deve limpar seus dados corrompidos (se houver algum) e adicioná-los de volta usando as etapas padrão no Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. No menu **Iniciar,** procure o **Gerente de Credencial** e abra-o.
2. Vá para **as credenciais do Windows**.
3. Em **Credenciais Genéricas,** procure `<connection_type_key>/<corrupted_connection_name>` entradas que `StorageExplorer_CustomConnections_Accounts_v1/account1`tenham a chave (por exemplo, ).
4. Exclua essas entradas e adicione as conexões.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Destaque aberto (Command+Spacebar) e procure **acesso a chaveiro**.
2. Procure por entradas `<connection_type_key>/<corrupted_connection_name>` que tenham a `StorageExplorer_CustomConnections_Accounts_v1/account1`chave (por exemplo, ).
3. Exclua essas entradas e adicione as conexões.

# <a name="linux"></a>[Linux](#tab/Linux)

O gerenciamento de credenciais locais varia dependendo da distribuição Linux. Se a sua distribuição Linux não fornecer uma ferramenta de GUI incorporada para o gerenciamento de credenciais locais, você pode instalar uma ferramenta de terceiros para gerenciar suas credenciais locais. Por exemplo, você pode usar [o Seahorse](https://wiki.gnome.org/Apps/Seahorse/), uma ferramenta de GUI de código aberto para gerenciar credenciais locais do Linux.

1. Abra sua ferramenta de gerenciamento de credenciais local e encontre suas credenciais salvas.
2. Procure por entradas `<connection_type_key>/<corrupted_connection_name>` que tenham a `StorageExplorer_CustomConnections_Accounts_v1/account1`chave (por exemplo, ).
3. Exclua essas entradas e adicione as conexões.
---

Se você ainda encontrar esse erro depois de executar essas etapas ou se quiser compartilhar o que você suspeita ter corrompido as conexões, [abra um problema](https://github.com/microsoft/AzureStorageExplorer/issues) em nossa página do GitHub.

## <a name="issues-with-sas-url"></a>Problemas com a URL SAS

Se você estiver se conectando a um serviço através de uma URL SAS e experimentando um erro:

* Verifique se a URL fornece as permissões necessárias para ler ou lista recursos.
* Verifique se a URL não expirou.
* Se a URL SAS tiver base em uma política de acesso, verifique se a política de acesso não foi revogada.

Se você acidentalmente anexado usando uma URL SAS inválida e agora não puder desacoplado, siga estas etapas:

1. Ao executar o Storage Explorer, pressione F12 para abrir a janela Ferramentas do Desenvolvedor.
2. Na guia **Aplicativo,** selecione**file://** **de armazenamento** > local na árvore à esquerda.
3. Localize a chave associada ao tipo de serviço do URI SAS problemático. Por exemplo, se o URI da SAS incorreta for para um contêiner de blob, procure a chave nomeada `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. O valor da chave deve ser uma matriz JSON. Encontre o objeto associado ao URI ruim e, em seguida, exclua-o.
5. Pressione Ctrl+R para recarregar o Gerenciador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências do Linux

O Storage Explorer 1.10.0 e posteriormente está disponível como um snap da Snap Store. O snap do Storage Explorer instala todas as suas dependências automaticamente, e é atualizado quando uma nova versão do snap está disponível. Instalar o snap do Storage Explorer é o método de instalação recomendado.

O Storage Explorer requer o uso de um gerenciador de senhas, que você pode precisar para se conectar manualmente antes que o Storage Explorer funcione corretamente. Você pode conectar o Storage Explorer ao gerenciador de senhas do sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Você também pode baixar o aplicativo como um arquivo .tar.gz, mas você terá que instalar dependências manualmente.

> [!IMPORTANT]
> O Storage Explorer, conforme fornecido no download do .tar.gz, é suportado apenas para distribuições do Ubuntu. Outras distribuições não foram verificadas e podem exigir pacotes alternativos ou adicionais.

Esses pacotes são os requisitos mais comuns para o Storage Explorer no Linux:

* [.NET Core 2.2 Tempo de execução](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` ou `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> A versão 1.7.0 do Storage Explorer e anteriormente exigem o .NET Core 2.0. Se você tiver uma versão mais recente do .NET Core instalada, você terá que [corrigir o Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Se você estiver executando o Storage Explorer 1.8.0 ou posterior, você deve ser capaz de usar até .NET Core 2.2. Versões além do 2.2 não foram verificadas para funcionar no momento.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Baixe O Storage Explorer.
2. Instale o [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Execute o comando a seguir:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Baixe O Storage Explorer.
2. Instale o [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Execute o comando a seguir:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Baixe O Storage Explorer.
2. Instale o [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Execute o comando a seguir:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Baixe O Storage Explorer.
2. Instale o [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Execute o comando a seguir:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Patching Storage Explorer para versões mais recentes do .NET Core

Para o Storage Explorer 1.7.0 ou anterior, você pode ter que corrigir a versão do .NET Core usado pelo Storage Explorer:

1. Baixe a versão 1.5.43 do StreamJsonRpc [da NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Procure o link "Download package" no lado direito da página.
2. Depois de baixar o pacote, `.nupkg` altere sua extensão de arquivo de . `.zip`
3. Descompacte o pacote.
4. Abra a pasta `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiar `StreamJsonRpc.dll` para os seguintes locais na pasta Explorador de armazenamento:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Open In Explorer" do portal Azure não funciona

Se o botão **Abrir no Explorer** no portal Azure não funcionar, certifique-se de que você está usando um navegador compatível. Os seguintes navegadores foram testados para compatibilidade:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Próximas etapas

Se nenhuma dessas soluções funcionar para você, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Você também pode fazer isso selecionando o problema Relatório para o botão **GitHub** no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)
