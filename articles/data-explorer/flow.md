---
title: Conector de fluxo do Microsoft Azure Data Explorer (Visualização)
description: Saiba como usar o conector Microsoft Flow para criar fluxos de tarefas agendadas ou acionadas automaticamente.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501775"
---
# <a name="microsoft-flow-connector-preview"></a>Conector de fluxo da Microsoft (Visualização)

O conector de fluxo do Azure Data Explorer permite que o Azure Data Explorer use os [recursos flow do Microsoft Power Automate](https://flow.microsoft.com/) para executar consultas e comandos do Kusto automaticamente como parte de uma tarefa programada ou acionada.

Os cenários de uso comuns incluem:

* Envio de relatórios diários contendo tabelas e gráficos
* Definindo notificações com base nos resultados da consulta
* Agendamento de comandos de controle em clusters
* Exportação e importação de dados entre o Azure Data Explorer e outros bancos de dados 

Para obter mais informações, consulte [exemplos de uso do conector Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Fazer logon 

1. Faça login no [Microsoft Power Automate](https://flow.microsoft.com/).

1. Ao se conectar pela primeira vez, você será solicitado a fazer login.

1. Selecione **Iniciar sessão** e insira suas credenciais.

![Caixa de diálogo Entrar](./media/flow/flow-signin.png)

## <a name="authentication"></a>Autenticação

Você pode autenticar com credenciais de usuário ou um aplicativo AAD.

> [!Note]
> Certifique-se de que seu aplicativo é [um aplicativo AAD](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) e está autorizado a executar consultas em seu cluster.

1. Selecione os três pontos no canto superior direito ![do conector Microsoft Flow: Adicione uma conexão](./media/flow/flow-addconnection.png)

1. Selecione **Adicionar nova conexão** e, em seguida, **selecione Conectar com o diretor de serviço**.
![Faça login na caixa de diálogo](./media/flow/flow-signin.png)

1. Insira as informações necessárias:
    * Nome da conexão: Um nome descritivo e significativo para a nova conexão
    * ID do cliente: Seu ID de aplicativo
    * Client Secret: Sua chave de aplicativo
    * Inquilino: O ID do diretório AAD no qual você criou o aplicativo. Por exemplo, o ID de inquilino da Microsoft é: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Autenticação do aplicativo](./media/flow/flow-appauth.png)

Quando a autenticação estiver completa, você verá que seu fluxo usa a conexão recém-adicionada.

![Autenticação completa do aplicativo](./media/flow/flow-appauthcomplete.png)

A partir de agora, esse fluxo será executado usando essas credenciais de aplicativo.

## <a name="find-the-azure-kusto-connector"></a>Encontre o conector Azure Kusto

Para usar o conector Microsoft Flow, você precisa primeiro adicionar um gatilho. Um gatilho pode ser definido com base em um período de tempo recorrente, ou como resposta a uma ação de fluxo anterior.

1. [Crie um novo fluxo](https://flow.microsoft.com/manage/flows/new) ou, a partir da página inicial, selecione a ação **Meus fluxos** e selecione **+ Novo**.

    ![Criar um novo fluxo](./media/flow/flow-newflow.png)

1. Adicionar programado em branco.

    ![Novo fluxo programado](./media/flow/flow-scheduled-from-blank.png)

1. Digite as informações necessárias na Página de fluxo programada para construir.
    ![Construir um fluxo programado](./media/flow/flow-build-scheduled-flow.png)
1. Selecione **Criar**.
1. Selecione **+ Nova Etapa**.
1. Na caixa de pesquisa, digite "Kusto".

    ![Selecione ações de fluxo](./media/flow/flow-actions.png)

1. Selecione **o Azure Data Explorer**.

## <a name="flow-actions"></a>Ações de Fluxo

Quando você abre o conector Azure Data Explorer, existem três ações possíveis que você pode adicionar ao seu fluxo.

Esta seção descreve os recursos e parâmetros para cada ação do Microsoft Flow.

![Ações do Flow Azure Data Explorer](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Executar comando de controle e visualizar resultados

Use o comando Executar controle e visualizar a ação de resultados para executar um [comando de controle](https://docs.microsoft.com/azure/kusto/management/index).

1. Especifique a URL do cluster. Por exemplo, https://clusterName.eastus.kusto.windows.net
1. Digite o nome do banco de dados.
1. Especifique o comando de controle:
    * Selecione o conteúdo dinâmico dos aplicativos e conectores usados no fluxo
    * Adicione uma expressão para acessar, converter e comparar valores
1. Para enviar os resultados desta ação por e-mail como uma tabela ou um gráfico, especifique o tipo de gráfico, que pode ser:
    * Uma tabela HTML
    * Um gráfico de tortas
    * Um gráfico de tempo
    * Um gráfico de barras

![Executar comando de controle de fluxo](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

### <a name="run-query-and-list-results"></a>Executar consulta e listar resultados

> [!Note]
> Se sua consulta começar com um dot (o que significa que é um comando de [controle),](https://docs.microsoft.com/azure/kusto/management/index)use [o comando Executar controle e visualizar resultados](#run-control-command-and-visualize-results)

Esta ação envia uma consulta ao cluster Kusto. As ações que são adicionadas posteriormente iteram sobre cada linha dos resultados da consulta.

O exemplo a seguir aciona uma consulta a cada minuto e envia um e-mail com base nos resultados da consulta. A consulta verifica o número de linhas no banco de dados e, em seguida, envia um e-mail somente se o número de linhas for maior que 0. 

![Executar resultados da lista de consultas](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Se a coluna tiver várias linhas, o conector será executado para cada linha na coluna.

### <a name="run-query-and-visualize-results"></a>Executar consulta e visualizar resultados
        
> [!Note]
> Se sua consulta começar com um dot (o que significa que é um comando de [controle),](https://docs.microsoft.com/azure/kusto/management/index)use [o comando Executar controle e visualizar resultados](#run-control-command-and-visualize-results)
        
Use a consulta Executar e visualize a ação de resultados para visualizar o resultado da consulta kusto como uma tabela ou gráfico. Por exemplo, use esse fluxo para receber relatórios diários do ICM por e-mail. 
    
Neste exemplo, os resultados da consulta são retornados como uma tabela HTML.
            
![Executar consulta e visualizar resultados](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> No campo Nome do *cluster,* digite a URL do cluster.

## <a name="email-kusto-query-results"></a>Resultados da consulta de e-mail kusto

Você pode incluir um passo em qualquer fluxo para enviar relatórios por e-mail para qualquer endereço de e-mail. 

1. Selecione **+ Novo passo** para adicionar um novo passo ao seu fluxo.
1. No campo de pesquisa, digite o Office 365 e selecione **Office 365 Outlook**.
1. Selecione **Enviar um email**.
1. Digite o endereço de e-mail para onde deseja que o relatório de e-mail seja enviado.
1. Digite o assunto do e-mail.
1. No campo *Corpo,* a partir do campo de conteúdo Dinâmico, selecione **Corpo**.
1. Selecione **Mostrar opções avançadas**.
1. No *campo Anexos Nome -1,* **selecione Nome de anexo**.
1. No campo *Conteúdo anexos,* selecione **Conteúdo de anexos**.
1. Se necessário, defina o nível de importância.
1. Selecione **Salvar**.

![Enviar email](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>Verifique se seu fluxo foi bem sucedido

Para verificar se o fluxo foi bem sucedido, consulte o histórico de execução do fluxo:
1. Vá para a página inicial do [Microsoft Flow](https://flow.microsoft.com/).
1. No menu principal, selecione [Meus fluxos](https://flow.microsoft.com/manage/flows).
    ![Minha página Fluxos](./media/flow/flow-myflows.png)
1. Na linha do fluxo que deseja investigar, selecione o ícone mais comandos e, em seguida, **Execute o histórico**.

    ![Executar menu histórico](./media/flow//flow-runhistory.png)

    Todas as corridas de fluxo são listadas com tempo de início, duração e status.
    ![Página de resultados do histórico de execução](./media/flow/flow-runhistoryresults.png)

    Para obter detalhes completos sobre o fluxo, na página [Meus fluxos,](https://flow.microsoft.com/manage/flows) selecione o fluxo que deseja investigar.

    ![Página de resultados completos do histórico](./media/flow/flow-fulldetails.png) 

Para ver por que uma execução falhou, selecione o tempo de início da execução. O fluxo aparece e o passo do fluxo que falhou é indicado por um ponto de exclamação vermelho. Expanda o passo falho para visualizar seus detalhes. O painel direito contém informações sobre a falha para que você possa soluciona-lo.

![Erro de fluxo](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Exceções de tempo para dotempo

Seu fluxo pode falhar e retornar uma exceção "RequestTimeout" se for executado por mais de sete minutos.

Saiba mais sobre [as limitações do Microsoft Flow](#limitations).
    
A mesma consulta pode ser executada com sucesso no Azure Data Explorer, onde o tempo não é limitado e pode ser alterado.
            
A exceção "RequestTimeout" é mostrada na imagem abaixo:
    
![Erro de exceção de tempo de tempo de solicitação de fluxo](./media/flow/flow-requesttimeout.png)
    
Para corrigir um problema de tempo, tente tornar sua consulta mais eficiente para que ela seja executada mais rápido ou separe-a em pedaços. Cada pedaço pode ser executado em uma parte diferente da consulta.

Para obter mais informações, leia sobre [as melhores práticas da Consulta.](https://docs.microsoft.com/azure/kusto/query/best-practices)

## <a name="limitations"></a>Limitações

* Os resultados devolvidos ao cliente são limitados a 500.000 registros. A memória geral desses registros não pode exceder 64 MB e sete minutos de tempo de execução.
* O conector não suporta os operadores [de bifurcação](https://docs.microsoft.com/azure/kusto/query/forkoperator) e [faceta.](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* O fluxo funciona melhor no Microsoft Edge e no Chrome.

## <a name="next-steps"></a>Próximas etapas

Conheça o [conector do aplicativo Microsoft Azure Explorer Logic](https://docs.microsoft.com/azure/kusto/tools/logicapps) App, que é outra maneira de executar consultas e comandos kusto automaticamente como parte de uma tarefa programada ou acionada.
