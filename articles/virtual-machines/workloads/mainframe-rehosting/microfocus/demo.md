---
title: Configure micro focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 em Máquinas Virtuais Azure
description: Execute o aplicativo Micro Focus BankDemo em VMs (Azure Virtual Machines) para aprender a usar o Micro Focus Enterprise Server e o Enterprise Developer.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67621330"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configure micro focus CICS BankDemo para Micro Focus Enterprise Developer 4.0 no Azure

Quando você configura o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure, você pode testar implantações de cargas de trabalho da IBM z/OS. Este artigo mostra como configurar o CICS BankDemo, um aplicativo de exemplo que vem com o Enterprise Developer.

CICs significa Customer Information Control System, a plataforma de transações usada por muitos dos aplicativos de mainframe on-line. O aplicativo BankDemo é ótimo para aprender como o Enterprise Server e o Enterprise Developer operam e como gerenciar e implantar um aplicativo real completo com terminais de tela verde.

## <a name="prerequisites"></a>Pré-requisitos

- Uma VM com [enterprise developer](set-up-micro-focus-azure.md). Tenha em mente que o Enterprise Developer tem uma instância completa do Enterprise Server nele para fins de desenvolvimento e teste. Este exemplo é o exemplo do Enterprise Server usado para a demonstração.

- [Edição Express do SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-editions-express). Baixe e instale-o no Enterprise Developer VM. O Enterprise Server requer um banco de dados para o gerenciamento de regiões CICS, e o aplicativo BankDemo também usa um banco de dados SQL Server chamado BANKDEMO. Esta demonstração pressupõe que você está usando o SQL Server Express para ambos os bancos de dados. Ao instalar, selecione a instalação básica.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). O SSMS é usado para gerenciar os bancos de dados e executar um script T-SQL. Baixe e instale-o no Enterprise Developer VM.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) com o mais recente service pack ou [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), que você pode baixar gratuitamente.

- Rumba Desktop ou outro emulador 3270.

## <a name="configure-the-windows-environment"></a>Configure o ambiente Windows

Depois de instalar o Enterprise Developer 4.0 na VM, você deve configurar a instância do Enterprise Server que vem com ele. Para fazer isso, você precisa instalar alguns recursos adicionais do Windows da seguinte forma.

1. Use RDP para fazer logon no Enterprise Server 4.0 VM que você criou.

2. Clique no ícone **pesquisar** ao lado do botão **Iniciar** e digitar **recursos do Windows**. O gerenciador de servidores adiciona funções e recursos do Assistente é aberto.

3. Selecione **a função Servidor Web (IIS)** e, em seguida, verifique as seguintes opções:

    - Ferramentas de gerenciamento da Web
    - Compatibilidade de gerenciamento IIS 6 (selecione todos os recursos disponíveis)
    - Console de Gerenciamento IIS
    - Scripts e Ferramentas de Gerenciamento do IIS
    - Serviço de Gestão do IIS

4. Selecione **world wide web services**e verifique as seguintes opções:

     Recursos de desenvolvimento de aplicativos:
    - Extensibilidade .NET
    - ASP.NET
    - Recursos HTTP comuns: Adicione todos os recursos disponíveis
    - Saúde e Diagnóstico: Adicione todos os recursos disponíveis
    - Segurança:
        - Autenticação Básica
        - Autenticação do Windows

5. Selecione **o Serviço de Ativação de Processos do Windows** e todos os seus filhos.

6. Para **Recursos,** consulte **a estrutura Microsoft .NET 3.5.1**e verifique as seguintes opções:

    - Ativação HTTP da Windows Communication Foundation
    - Ativação não-HTTP da Windows Communication Foundation

7. Para **Recursos,** consulte **o framework 4.6 da Microsoft e**verifique as seguintes opções:

   - Ativação de tubulação nomeada
   - Ativação TCP
   - Compartilhamento de porta TCP

     ![Adicionar funções e recursos assistente: serviços de função](media/01-demo-roles.png)

8. Quando tiver selecionado todas as opções, clique **em 'Ao lado** para instalar'.

9. Depois dos recursos do Windows, vá para **o Sistema do Painel de \> Controle e ferramentas administrativas de segurança \> **e selecione **Serviços**. Role para baixo e certifique-se de que os serviços a seguir estão sendo executados e definido como **Automático**:

    - **NetTcpPortSharing**
    - **Adaptador ouvinte do Net.Pipe**
    - **Adaptador de ouvinte Net.tcp**

10. Para configurar o suporte ao IIS e WAS, a partir do menu localize o **Micro Focus Enterprise Developer Command Prompt (64 bits)** e execute como **Administrador**.

11. Digite **wassetup –i** e **pressione Enter**.

12. Depois que o script é executado, você pode fechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configure a conta do sistema local para o SQL Server

Alguns processos do Enterprise Server precisam ser capazes de assinar o SQL Server e criar bancos de dados e outros objetos. Esses processos usam a conta do sistema local, então você deve dar autoridade sysadmin a essa conta.

1. Inicie o **SSMS** e clique **em Conectar-se** ao SQLEXPRESS Server local usando a Autenticação do Windows. Ele deve estar disponível na lista Nome do **servidor.**

2. À esquerda, expanda a pasta **Segurança** e selecione **Logins**.

3. Selecione **o SISTEMA NT AUTHORITY\\** e selecione **Propriedades**.

4. Selecione **Funções do servidor** e verifique **sysadmin**.

     ![Janela SSMS Object Explorer: Propriedades de login](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Crie o banco de dados bankdemo e todos os seus objetos

1. Abra **o Windows Explorer** e navegue para **C:\\Usuários\\\\Documentos públicos\\\\\\\\\\\\\\Micro Focus\\Enterprise Developer Samples Mainframe CICS DotNet BankDemo SQL**.

2. Copie o conteúdo do arquivo **BankDemoCreateAll.SQL** na sua área de transferência.

3. Abra **SSMS.** À direita, clique em **Servidor** e selecione **Nova consulta**.

4. Cole o conteúdo da área de transferência na **caixa Nova consulta.**

5. Execute o SQL clicando em **Executar** a partir da guia **Comando** acima da consulta.

A consulta deve ser executada sem erros. Quando estiver concluído, você tem o banco de dados de amostras para o aplicativo BankDemo.

![Saída sqLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verifique se as tabelas e objetos do banco de dados foram criados

1. Clique com o botão direito do mouse no banco de dados **BANKDEMO** e selecione **Atualizar**.

2. Expanda o **banco de dados** e selecione **Tabelas**. Você deveria ver algo como o seguinte.

     ![Tabela BANKDEMO expandida no Object Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Construa o aplicativo no Enterprise Developer

1. Abra o Visual Studio e entre.

2. Na opção **Menu Arquivo,** selecione **Abrir projeto/solução,** navegue até **C:\\Usuários\\Documentos Públicos\\\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**e selecione o arquivo **sln.**

3. Tire um tempo para examinar os objetos. Os programas COBOL são mostrados no Solution Explorer com a extensão CBL, juntamente com CopyBooks (CPY) e JCL.

4. Clique com o botão direito do mouse no Projeto **BankDemo2** e selecione **Definir como Projeto de Inicialização**.

    > [!NOTE]
    > O Projeto BankDemo faz uso do HCOSS (Host Compatibility Option for SQL Server), que não é usado para esta demonstração.

5. No **Solution Explorer,** clique com o botão direito do mouse no Projeto **BankDemo2** e selecione **Build**.

    > [!NOTE]
    > Construir no nível da solução resulta em erros, já que o HCOSS não foi configurado.

6. Quando o Projeto for construído, examine a janela **Saída.** Ele deverá ter a aparência mostrada na imagem abaixo.

     ![Janela de saída mostrando construção bem sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implantar o aplicativo BankDemo no banco de dados da Região

1. Abra um prompt de comando enterprise developer (64 bits) como administrador.

2. Navegar até os **documentos\\%PUBLIC%\\\\Micro\\Focus\\\\Enterprise Developer\\amostra mainframe\\CICS DotNet BankDemo**.

3. No prompt de comando, execute **bankdemodbdeploy** e inclua o parâmetro para o banco de dados ser implantado, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de usar uma barra para a\\frente (/) e não uma barra para trás( ). Este roteiro é executado por um tempo.

![Administração: Janela prompt de prompt de comando de desenvolvedor corporativo](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Crie a região de demonstração bancária no Administrador de Empresas para .NET

1. Abra o **Enterprise Server para a UI de Administração .NET.**

2. Para iniciar o snap-in do MMC, no **menu** Iniciar do Windows, escolha o Micro Focus Enterprise ** \> Developer Configuration \> Enterprise Server para .NET Admin**. (Para o Windows Server, escolha **micro focus enterprise developer \> enterprise enterprise server para .NET Admin**).

3. Expanda o recipiente **Regiões** no painel esquerdo e clique com o botão direito **do mouse CICS**.

4. Selecione **Definir Região** para criar uma nova região CICS chamada **BANKDEMO**, hospedada no banco de dados (local).

5. Forneça a instância do servidor de banco de dados, clique **em Avançar**e digite o nome da região **BANKDEMO**.

     ![Definir caixa de diálogo região](media/07-demo-cics.png)

6. Para selecionar o arquivo de definição de região para o banco de dados de região, localize **o banco de\_região\_db.config** em **C:\\\\Users Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definir Região - Nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **concluir**.

## <a name="create-xa-resource-definitions"></a>Criar definições de recursos XA

1. No painel esquerdo do **Enterprise Server para .NET Administration** UI, expanda o **Sistema**e, em seguida, **XA Definições de recursos**. Essa configuração define como a Região interopera com o Enterprise Server e os bancos de dados de aplicativos.

2. Clique com o botão direito do mouse em **Definições de recursos XA** e selecione **Adicionar instância do servidor**.

3. Na caixa de sestáção, selecione **'Instância de serviço de banco de dados'.** Será a máquina local SQLEXPRESS.

4. Selecione a instância no contêiner **XA\\Resource Definitions (machinename sqlexpress)** e clique **em Adicionar**.

5. Selecione **A definição de recurso Do banco de dados XA** e **digite BANKDEMO** para **nome** e **região**.

     ![Nova tela de definição de recursos XA do banco de dados](media/09-demo-xa.png)

6. Clique nas elipses **(...**) para trazer o assistente connection string. Para **nome do servidor,** digite **(local)\\SQLEXPRESS**. Para **Logon,** selecione **Autenticação do Windows**. Para nome do banco de dados, **digite BANKDEMO**

     ![Editar tela de string de conexão](media/10-demo-string.png)

7. Teste a conexão.

## <a name="start-the-bankdemo-region"></a>Inicie a Região DO BANCO DEMO

> [!NOTE]
> O primeiro passo é importante: você deve definir a Região para usar a definição de recurso XA que você acabou de criar.

1. Navegue até a **região do BANDEMO CICS** o **Contêiner regiões**e selecione Editar arquivo de **inicialização** da região no painel **Ações.** Desça até as propriedades SQL e **insira demonstração bancária** para o nome do **recurso XA**ou use as elipses para selecioná-la.

2. Clique no ícone **Salvar** para salvar suas alterações.

3. Clique com o botão direito **do mouse BANKDEMO CICS Region** no painel **Console** e selecione Região **de Início/Parada**.

4. Na parte inferior da caixa **Região de Início/Parada** que aparece no painel do meio, selecione **Iniciar**. Depois de alguns segundos, a região começa.

     ![Caixa sql start/stop](media/11-demo-sql.png)

     ![CICS Region BANKDEMO - Tela iniciada](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Crie um ouvinte

Crie um ouvinte para as sessões TN3270 que acessam o aplicativo BankDemo.

1. No painel esquerdo, expanda os **Editores de Configuração** e selecione **Ouvinte**.

2. Clique no ícone **Arquivo aberto** e selecione o arquivo **seelistener.exe.config.** Este arquivo será editado e será carregado toda vez que o Enterprise Server for iniciado.

3. Observe as duas Regiões previamente definidas (ESDEMO e JCLDEMO).

4. Para criar uma nova região para BANKDEMO, clique com o botão direito do mouse **Regiões**e selecione **Adicionar região**.

5. Selecione **Região DEDEMONSTRAÇÃO DE BANCO**.

6. Adicione um canal TN3270 clicando com o botão direito **do mouse NA REGIÃO BANKDEMO** e selecionando Adicionar **canal**.

7. Para **Nome,** digite **TN3270**. Para **Porto,** digite **9024**. O aplicativo ESDEMO usa a porta 9230, então você precisa usar uma porta diferente.

8. Para salvar o arquivo, clique no ícone **Salvar** ou escolha **Salvar** **arquivos** \> .

9. Para iniciar o ouvinte, clique no ícone **Iniciar ouvinte** ou escolha **Opções** \> **Iniciar ouvinte**.

     ![Janelas do editor de configuração do ouvinte](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configure rumba para acessar o aplicativo BankDemo

A última coisa que você precisa fazer é configurar uma sessão 3270 usando Rumba, um emulador 3270. Esta etapa permite que você acesse o aplicativo BankDemo através do ouvinte que você criou.

1. No **menu** Iniciar do Windows, inicie o Rumba Desktop.

2. No item **menu Conexões,** selecione **TN3270**.

3. Clique **em Inserir** e digite **127.0.0.1** para o endereço IP e **9024** para a porta definida pelo usuário.

4. Na parte inferior da caixa de diálogo, clique **em Conectar**. Uma tela CICS preta é exibida.

5. Digite o **banco** para exibir a tela inicial 3270 para o aplicativo BankDemo.

6. Para iD do usuário, **digite B0001** e para a senha, digite qualquer coisa. A primeira tela BANK20 abre.

![Mainframe Display](media/14-demo.png)
![Tela de boas-vindas Tela principal Display - Rumba - Tela de demonstração do subsistema](media/15-demo.png)

Parabéns! Agora você está executando um aplicativo CICS no Azure usando o Micro Focus Enterprise Server.

## <a name="next-steps"></a>Próximas etapas

- [Execute o Enterprise Server em contêineres Docker no Azure](run-enterprise-server-container.md)
- [Migração de mainframe - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Solução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
