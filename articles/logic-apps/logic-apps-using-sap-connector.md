---
title: Conecte-se aos sistemas SAP
description: Acesse e gerencie recursos SAP automatizando fluxos de trabalho com aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651008"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure

> [!IMPORTANT]
> Os conectores anteriores do SAP Application Server e do SAP Message Server são preteridos em 29 de fevereiro de 2020. O conector SAP atual consolida esses conectores SAP anteriores para que você não precise alterar o tipo de conexão, seja totalmente compatível com conectores anteriores, forneça muitos recursos adicionais e continue a usar a biblioteca de conectores SAP .Net ( SAP NCo).
>
> Para aplicativos lógicos que usam os conectores mais antigos, [migre para o conector mais recente](#migrate) antes da data de depreciação. Caso contrário, esses aplicativos lógicos sofrerão falhas de execução e não poderão enviar mensagens para o seu sistema SAP.

Este artigo mostra como você pode acessar seus recursos SAP no local de dentro de um aplicativo lógico usando o conector SAP. O conector funciona com os lançamentos clássicos da SAP, como sistemas R/3 e ECC no local. O conector também permite a integração com os novos sistemas SAP baseados em HANA da SAP, como o S/4 HANA, sejam eles hospedados no local ou na nuvem. O conector SAP suporta a integração de mensagens ou dados para e a partir de sistemas baseados em SAP NetWeaver através de Documento Intermediário (IDoc), Interface de Programação de Aplicativos de Negócios (BAPI) ou Chamada de Função Remota (RFC).

O conector SAP usa a [biblioteca SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) e fornece essas ações:

* **Enviar mensagem para SAP**: Envie iDoc sobre tRFC, ligue para funções BAPI sobre RFC ou ligue para RFC/tRFC em sistemas SAP.
* **Quando uma mensagem for recebida do SAP**: Receba iDoc sobre tRFC, ligue para funções BAPI sobre tRFC ou ligue para RFC/tRFC em sistemas SAP.
* **Gerar esquemas**: Gerar esquemas para os artefatos SAP para IDoc, BAPI ou RFC.

Para essas operações, o conector SAP suporta autenticação básica por meio de nomes de usuário e senhas. O conector também suporta [SNC (Secure Network Communications, comunicações de rede segura)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser usado para sap NetWeaver single sign-on (SSO) ou para recursos adicionais de segurança fornecidos por um produto de segurança externa.

O conector SAP integra-se a sistemas SAP locais por meio do [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Em cenários de envio, por exemplo, quando uma mensagem é enviada de um aplicativo lógico para um sistema SAP, o gateway de dados atua como um cliente RFC e encaminha as solicitações recebidas do aplicativo lógico para o SAP. Da mesma forma, nos cenários de recebimento, o gateway de dados atua como um servidor RFC que recebe solicitações do SAP e as encaminha para o aplicativo lógico.

Este artigo mostra como criar aplicativos lógicos de exemplo que se integram ao SAP ao abordar os cenários de integração descritos anteriormente. Para aplicativos lógicos que usam os conectores SAP mais antigos, este artigo mostra como migrar seus aplicativos lógicos para o conector SAP mais recente.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir com este artigo, são necessário esses itens:

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Se você é novo em aplicativos lógicos, veja [O que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Seu [servidor de aplicativo SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou servidor de [mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Baixe e instale a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) em qualquer computador local. Antes de continuar, verifique se você configurou seu gateway no portal do Azure. O gateway ajuda você a acessar com segurança dados e recursos no local. Para obter mais informações, consulte [Instalar um gateway de dados no local para aplicativos de lógica do Azure](../logic-apps/logic-apps-gateway-install.md).

* Se você usar SNC com SSO, certifique-se de que o gateway está sendo executado como um usuário mapeado contra o usuário SAP. Para alterar a conta padrão, selecione **Alterar conta**e digite as credenciais do usuário.

  ![Alterar conta de gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se você habilitar o SNC com um produto de segurança externo, copie a biblioteca SNC ou os arquivos na mesma máquina onde o gateway está instalado. Alguns exemplos de produtos SNC incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

* Baixe e instale a mais recente biblioteca de clientes SAP, que atualmente é [sap connector (NCo 3.0) para Microsoft .NET 3.0.22.0 compilado com .NET Framework 4.0 - Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), no mesmo computador que o gateway de dados on-premises. Instale esta versão ou uma posterior por estes motivos:

  * As versões anteriores do SAP NCo podem ficar bloqueadas quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Essa condição bloqueia todas as mensagens posteriores enviadas para o destino SAP, o que faz com que as mensagens se estimem.
  
  * O gateway de dados local é executado apenas em sistemas de 64 bits. Caso contrário, você obtém um erro de “imagem incorreta” porque o serviço de host do gateway de dados não oferece suporte a assemblies de 32 bits.
  
  * O serviço de host do gateway de dados e o adaptador SAP da Microsoft usam o .NET Framework 4.5. O NCo SAP para o .NET Framework 4.0 funciona com processos que usam o runtime do .NET 4.0 para 4.7.1. O SAP NCo for .NET Framework 2.0 funciona com processos que usam o .NET runtime 2.0 a 3.5, mas não funciona mais com o gateway de dados no local mais recente.

* O conteúdo da mensagem que você pode enviar para o servidor SAP, como um arquivo IDoc de amostra, deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrar para o conector atual

1. Se você ainda não fez isso, atualize seu [gateway de dados no local para](https://www.microsoft.com/download/details.aspx?id=53127) que você tenha a versão mais recente. Para obter mais informações, consulte [Instalar um gateway de dados no local para aplicativos de lógica do Azure](../logic-apps/logic-apps-gateway-install.md).

1. No aplicativo lógico que usa o conector SAP mais antigo, exclua a ação **Enviar para SAP.**

1. No conector SAP mais recente, adicione a mensagem Enviar à ação **SAP.** Antes de usar essa ação, recrie a conexão com seu sistema SAP.

1. Quando terminar, salve o aplicativo lógico.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Enviar mensagem ao SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. O aplicativo lógico envia um IDoc para um servidor SAP e retorna uma resposta ao solicitante que chamou o aplicativo lógico.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho http solicitação

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Neste exemplo, você criará um aplicativo lógico com um ponto de extremidade no Azure para seja possível enviar *solicitações HTTP POST* para o aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No [portal Azure,](https://portal.azure.com)crie um aplicativo de lógica em branco, que abre o Logic App Designer.

1. Na caixa de pesquisa, insira `http request` como o filtro. Na lista **'Gatilhos',** **selecione Quando uma solicitação HTTP for recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora salve seu aplicativo lógico para que você possa gerar uma URL de ponto final para o seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicione uma ação SAP

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Caso ainda não tenha adicionado um gatilho ao seu aplicativo lógico e deseje acompanhar esse exemplo [adicione o gatilho descrito nesta seção](#add-trigger).

1. No Logic App Designer, o gatilho, selecione **Novo passo**.

   ![Adicionar novo passo ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista **Ações,** selecione **Enviar mensagem para SAP**.
  
   ![Selecione a ação "Enviar mensagem para SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, você pode selecionar a guia **Empresa** e selecionar a ação SAP.

   ![Selecione a ação "Enviar mensagem para SAP" na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se a sua conexão já existir, continue com o próximo passo para que você possa configurar sua ação SAP. No entanto, se você for solicitado para detalhes da conexão, forneça as informações para que você possa criar uma conexão com seu servidor SAP no local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Data Gateway,** em **Assinatura,** primeiro selecione a assinatura do Azure para o recurso gateway que você criou no portal Azure para sua instalação de gateway. 
   
   1. Em **Connection Gateway,** selecione o recurso gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **Tipo de logon,** siga o passo com base em se a propriedade está definida como **Servidor de Aplicativos** ou **Grupo**:
   
      * Para **o Servidor de Aplicativos,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **grupo,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a digitação forte é usada para verificar valores inválidos realizando validação XML contra o esquema. Esse comportamento pode ajudá-lo a detectar problemas mais cedo. A opção **Digitação Segura** está disponível para compatibilidade retrógrada e apenas verifica o comprimento da corda. Saiba mais sobre a [opção Digitação segura](#safe-typing).

   1. Quando terminar, selecione **Criar**.

      O Logic Apps configura e testa sua conexão para garantir que a conexão funcione corretamente.

1. Agora localize e selecione uma ação do servidor SAP.

   1. Na caixa **SAP Action,** selecione o ícone da pasta. Na lista de arquivos, localize e selecione a mensagem do SAP que você deseja usar. Para navegar pela lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **Orders.**

      ![Localizar e selecionar a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não encontrar a ação desejada, é possível inserir um caminho manualmente, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor para **o SAP Action** através do editor de expressão. Dessa forma, você pode usar a mesma ação para diferentes tipos de mensagens.

      Para obter mais informações sobre as operações do IDoc, consulte [esquemas de mensagem para operações do IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique dentro da caixa **Mensagem de entrada** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Quando uma solicitação HTTP é recebida**, selecione o campo **Corpo**.

      Esta etapa inclui o conteúdo do corpo do gatilho http request e envia essa saída para o servidor SAP.

      ![Selecione a propriedade "Corpo" do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando você termina, sua ação SAP se parece com este exemplo:

      ![Complete a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicione uma ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída de uma ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original.

1. No Logic App Designer, a ação SAP, selecione **Novo passo**.

1. Na caixa de pesquisa, insira `response` como o filtro. Na lista **Ações,** selecione **Resposta**.

1. Clique dentro da caixa **Corpo** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Enviar mensagem para SAP,** selecione o **campo Corpo.**

   ![Concluir ação SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salve seu aplicativo lógico.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se o seu aplicativo lógico ainda não estiver habilitado, no menu do aplicativo lógico, selecione **Visão geral**. Na barra de ferramentas, selecione **Ativar**.

1. Na barra de ferramentas do designer, selecione **Executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Acione seu aplicativo lógico enviando uma solicitação HTTP POST para a URL no seu gatilho http solicitação.
Inclua o conteúdo da sua mensagem com sua solicitação. Para enviar a solicitação, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/apps).

   Neste artigo, a solicitação envia um arquivo IDoc, que deve estar no formato XML e incluir o namespace para a ação SAP que você está usando, por exemplo:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Depois de enviar a solicitação HTTP, aguarde a resposta do seu aplicativo lógico.

   > [!NOTE]
   > O aplicativo lógico pode atingir o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do [tempo limite da solicitação](./logic-apps-limits-and-config.md). Se essa condição ocorrer, as solicitações podem ser bloqueadas. Para auxílio para diagnosticar problemas, saiba como você pode [verificar e monitorar seus aplicativos lógicos](../logic-apps/monitor-logic-apps.md).

Agora você criou um aplicativo lógico que pode se comunicar com seu servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, é possível explorar outras ações SAP disponíveis, como BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Receba mensagem do SAP

Este exemplo usa um aplicativo lógico que é acionado quando o aplicativo recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicione um gatilho SAP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista **'Gatilhos',** **selecione Quando uma mensagem for recebida do SAP**.

   ![Adicionar gatilho do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ou, você pode selecionar a guia **Empresa** e, em seguida, selecionar o gatilho:

   ![Adicionar gatilho SAP da guia Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se a sua conexão já existir, continue com o próximo passo para que você possa configurar sua ação SAP. No entanto, se você for solicitado para detalhes da conexão, forneça as informações para que você possa criar uma conexão com seu servidor SAP no local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Data Gateway,** em **Assinatura,** primeiro selecione a assinatura do Azure para o recurso gateway que você criou no portal Azure para sua instalação de gateway. 

   1. Em **Connection Gateway,** selecione o recurso gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **Tipo de logon,** siga o passo com base em se a propriedade está definida como **Servidor de Aplicativos** ou **Grupo**:

      * Para **o Servidor de Aplicativos,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **grupo,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por padrão, a digitação forte é usada para verificar valores inválidos realizando validação XML contra o esquema. Esse comportamento pode ajudá-lo a detectar problemas mais cedo. A opção **Digitação Segura** está disponível para compatibilidade retrógrada e apenas verifica o comprimento da corda. Saiba mais sobre a [opção Digitação segura](#safe-typing).

   1. Quando terminar, selecione **Criar**.

      O Logic Apps configura e testa sua conexão para garantir que a conexão funcione corretamente.

1. Forneça os parâmetros necessários com base na configuração do sistema SAP.

   Também é possível oferecer uma ou mais ações do SAP. Esta lista de ações especifica as mensagens que o gatilho recebe do servidor SAP por meio do gateway de dados. Uma lista vazia especifica que o gatilho recebe todas as mensagens. Se a lista tiver mais de uma mensagem, o gatilho receberá apenas as mensagens especificadas na lista. Quaisquer outras mensagens enviadas do seu servidor SAP são rejeitadas pelo gateway.

   É possível selecionar uma ação do SAP no seletor de arquivos:

   ![Adicionar ação SAP ao aplicativo lógico](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou é possível especificar manualmente uma ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Veja um exemplo que mostra como a ação é exibida quando você configura o gatilho para receber mais de uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações do IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Agora salve seu aplicativo lógico para poder começar a receber mensagens do seu sistema SAP. Selecione **Salvar** na barra de ferramentas do designer.

Agora seu aplicativo lógico está pronto para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho SAP não é um gatilho de votação, mas é um gatilho baseado em webhook. O gatilho é chamado do gateway somente quando há uma mensagem; portanto, não é necessária nenhuma sondagem.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Para disparar seu aplicativo lógico, envie uma mensagem do sistema SAP.

1. No menu do aplicativo lógico, selecione **Visão geral**. Revise o **histórico de corridas** para quaisquer novas corridas para o seu aplicativo lógico.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na seção de saídas de gatilho.

## <a name="receive-idoc-packets-from-sap"></a>Receba pacotes IDOC da SAP

Você pode configurar o SAP para [enviar IDOCs em pacotes,](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)que são lotes ou grupos de IDOCs. Para receber pacotes IDOC, o conector SAP e, especificamente, o gatilho, não precisa de configuração extra. No entanto, para processar cada item em um pacote IDOC após o gatilho receber o pacote, algumas etapas adicionais são necessárias para dividir o pacote em IDOCs individuais.

Aqui está um exemplo que mostra como extrair IDOCs [ `xpath()` ](./workflow-definition-language-functions-reference.md#xpath)individuais de um pacote usando a função :

1. Antes de começar, você precisa de um aplicativo lógico com um gatilho SAP. Se você ainda não tiver esse aplicativo lógico, siga os passos anteriores neste tópico para [configurar um aplicativo lógico com um gatilho SAP](#receive-from-sap).

   Por exemplo: 

   ![Adicionar gatilho SAP ao aplicativo lógico](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Obtenha o namespace raiz do XML IDOC que seu aplicativo lógico recebe do SAP. Para extrair esse namespace do documento XML, adicione uma etapa que cria uma `xpath()` variável de string local e arma que namespace usando uma expressão:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obtenha namespace raiz do IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extrair um IDOC individual, adicione uma etapa que cria uma variável `xpath()` de matriz e armaça a coleção IDOC usando outra expressão:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Obter matriz de itens](./media/logic-apps-using-sap-connector/get-array.png)

   A variável array disponibiliza cada IDOC para que seu aplicativo lógico processe individualmente, enumerando sobre a coleção. Neste exemplo, o aplicativo lógico transfere cada IDOC para um servidor SFTP usando um loop:

   ![Envie IDOC para o servidor SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDOC deve incluir o namespace raiz, que é a `<Receive></Receive` razão pela qual o conteúdo do arquivo é embrulhado dentro de um elemento, juntamente com o namespace raiz antes de enviar o IDOC para o aplicativo downstream ou servidor SFTP neste caso.

Você pode usar o modelo quickstart para este padrão selecionando este modelo no Logic App Designer ao criar um novo aplicativo lógico.

![Selecione o modelo do aplicativo de lógica de lote](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefatos no SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. A ação SAP envia uma solicitação a um sistema SAP para gerar os esquemas para IDoc e BAPI especificados. Os schemas que retornam na resposta são carregados em uma conta de integração usando o conector Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho http solicitação

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, insira `http request` como o filtro. Na lista **'Gatilhos',** **selecione Quando uma solicitação HTTP for recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Selecione **Salvar** na barra de ferramentas do designer.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicione uma ação SAP para gerar esquemas

1. No Logic App Designer, o gatilho, selecione **Novo passo**.

   ![Adicionar novo passo ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista **Ações,** selecione **Gerar esquemas**.
  
   ![Adicionar ação "Gerar esquemas" ao aplicativo lógico](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, você pode selecionar a guia **Empresa** e selecionar a ação SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se a sua conexão já existir, continue com o próximo passo para que você possa configurar sua ação SAP. No entanto, se você for solicitado para detalhes da conexão, forneça as informações para que você possa criar uma conexão com seu servidor SAP no local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Data Gateway,** em **Assinatura,** primeiro selecione a assinatura do Azure para o recurso gateway que você criou no portal Azure para sua instalação de gateway. 
   
   1. Em **Connection Gateway,** selecione o recurso gateway.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **Tipo de logon,** siga o passo com base em se a propriedade está definida como **Servidor de Aplicativos** ou **Grupo**:
   
      * Para **o Servidor de Aplicativos,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **grupo,** essas propriedades, que geralmente parecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a digitação forte é usada para verificar valores inválidos realizando validação XML contra o esquema. Esse comportamento pode ajudá-lo a detectar problemas mais cedo. A opção **Digitação Segura** está disponível para compatibilidade retrógrada e apenas verifica o comprimento da corda. Saiba mais sobre a [opção Digitação segura](#safe-typing).

   1. Quando terminar, selecione **Criar**.

      O Logic Apps configura e testa sua conexão para garantir que a conexão funcione corretamente.

1. Forneça o caminho para o artefato para o qual você deseja gerar o esquema.

   É possível selecionar a ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou é possível inserir manualmente a ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para mais de um artefato, forneça os detalhes da ação do SAP para cada artefato, por exemplo:

   ![Selecionar Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [Esquemas de mensagem para operações do IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, **selecione Executar** para ativar uma execução para o seu aplicativo lógico.

1. Abra a corrida e verifique as saídas para a ação **Gerar esquemas.**

   As saídas mostram os esquemas gerados para a lista de mensagens especificada.

### <a name="upload-schemas-to-an-integration-account"></a>Faça upload de esquemas para uma conta de integração

Opcionalmente, é possível baixar ou armazenar os esquemas gerados em repositórios, como um blob, armazenamento ou conta de integração. As contas de integração oferecem uma experiência de primeira classe com outras ações de XML; portanto, este exemplo mostra como carregar esquemas para uma conta de integração para o mesmo aplicativo lógico usando o conector do Azure Resource Manager.

1. No Logic App Designer, o gatilho, selecione **Novo passo**.

1. Na caixa de pesquisa, insira `Resource Manager` como o filtro. Selecione **Criar ou atualizar um recurso**.

   ![Selecionar ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes da ação, incluindo sua assinatura do Azure, grupo de recursos do Azure e conta de integração. Para adicionar tokens SAP aos campos, clique em dentro das caixas para esses campos e selecione na lista de conteúdo dinâmico que aparece.

   1. Abra a **lista Adicionar novos parâmetros** e selecione os campos **Localização** e **Propriedades.**

   1. Forneça detalhes para esses novos campos, como mostrado neste exemplo.

      ![Insira os detalhes da ação do Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   A ação **Gerar esquemas** do SAP gera esquemas como uma coleção, portanto o designer adiciona automaticamente um loop **For each** à ação. Veja um exemplo que mostra como essa ação é exibida:

   ![Ação do Azure Resource Manager com o loop "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Os esquemas usam o formato codificado para base64. Para carregar os esquemas para uma conta de integração, eles devem ser decodificados usando a função `base64ToString()`. Veja um exemplo que mostra o código do elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, **selecione Executar** para acionar manualmente seu aplicativo lógico.

1. Depois de uma corrida bem sucedida, vá para a conta de integração e verifique se os esquemas gerados existem.

## <a name="enable-secure-network-communications"></a>Habilite comunicações seguras de rede

Antes de começar, certifique-se de que você encontrou os [pré-requisitos](#pre-reqs)listados anteriormente:

* O gateway de dados no local está instalado em uma máquina que está na mesma rede que seu sistema SAP.
* Para SSO, o gateway está sendo executado como um usuário mapeado para um usuário SAP.
* A biblioteca SNC que fornece as funções de segurança adicionais está instalada na mesma máquina que o gateway de dados. Alguns exemplos incluem [sapseculib,](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)Kerberos e NTLM.

   Para habilitar o SNC para suas solicitações de ou para o sistema SAP, selecione a caixa de seleção **Use SNC** na conexão SAP e forneça essas propriedades:

   ![Configure sap SNC em conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho da biblioteca SNC** | O nome ou caminho da biblioteca SNC em relação ao local de instalação do NCo ou ao caminho absoluto. Exemplos são `sapsnc.dll` `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll`ou . |
   | **SNC SSO** | Quando você se conecta através do SNC, a identidade SNC é normalmente usada para autenticar o chamador. Outra opção é substituir para que as informações de usuário e senha possam ser usadas para autenticar o chamador, mas a linha ainda está criptografada. |
   | **SNC Meu Nome** | Na maioria dos casos, essa propriedade pode ser omitida. A solução SNC instalada geralmente sabe seu próprio nome SNC. Apenas para soluções que suportam múltiplas identidades, talvez seja necessário especificar a identidade a ser usada para este destino ou servidor em particular. |
   | **Nome do parceiro SNC** | O nome do SNC back-end. |
   | **Qualidade de proteção do SNC** | A qualidade do serviço a ser utilizado para a comunicação SNC deste destino ou servidor específico. O valor padrão é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança usado para SNC. |
   |||

   > [!NOTE]
   > Não defina as variáveis de ambiente SNC_LIB e SNC_LIB_64 na máquina onde você tem o gateway de dados e a biblioteca SNC. Se definido, eles têm precedência sobre o valor da biblioteca SNC passado através do conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Digitação segura

Por padrão, quando você cria sua conexão SAP, a digitação forte é usada para verificar valores inválidos realizando validação XML contra o esquema. Esse comportamento pode ajudá-lo a detectar problemas mais cedo. A opção **Digitação Segura** está disponível para compatibilidade retrógrada e apenas verifica o comprimento da corda. Se você escolher **Digitação segura,** o tipo DATS e o tipo TIMS no SAP `xs:time`são `xmlns:xs="http://www.w3.org/2001/XMLSchema"`tratados como strings e não como seus equivalentes XML, `xs:date` e , onde . A digitação segura afeta o comportamento de toda a geração de esquemas, a mensagem de envio tanto para a carga "enviada" quanto para a resposta "recebida" e para o gatilho. 

Quando a digitação forte é usada **(a digitação segura** não está ativada), o esquema mapeia os tipos DATS e TIMS para tipos XML mais simples:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando você envia mensagens usando digitação forte, a resposta DATS e TIMS está em conformidade com o formato xml correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando **a digitação segura** é ativada, o esquema mapeia os tipos DATS e TIMS para campos de string XML apenas com restrições de comprimento, por exemplo:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando as mensagens são enviadas com **digitação segura** ativada, a resposta DATS e TIMS se parece com este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="confirm-transaction-explicitly"></a>Confirmar transação explicitamente

Quando você envia transações para SAP de Logic Apps, essa troca acontece em duas etapas, conforme descrito no documento SAP, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por padrão, a ação **Enviar para SAP** lida com as etapas para a transferência de função e para a confirmação da transação em uma única chamada. O conector SAP lhe dá a opção de desacoplar essas etapas. Você pode enviar um IDOC e, em vez de confirmar automaticamente a transação, você pode usar a ação explícita **confirmar a transação ID.**

Essa capacidade de desacoplar a confirmação de ID de transação é útil quando você não quer duplicar transações no SAP, por exemplo, em cenários onde falhas podem acontecer devido a causas como problemas de rede. Ao confirmar o ID da transação separadamente, a transação só é concluída uma vez em seu sistema SAP.

Aqui está um exemplo que mostra este padrão:

1. Crie um aplicativo de lógica em branco e adicione um gatilho HTTP.

1. No conector SAP, adicione a ação **Enviar IDOC.** Forneça os detalhes do IDOC que você envia para o seu sistema SAP.

1. Para confirmar explicitamente o ID da transação em uma etapa separada, no campo **Confirmar TID,** **selecione Não**. Para o campo **GUID de ID de transação** opcional, você pode especificar manualmente o valor ou ter o conector automaticamente gerar e retornar este GUID na resposta da ação Enviar IDOC.

   ![Enviar propriedades de ação do IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente o ID da transação, adicione a ação **Confirmar iD de transação.** Clique dentro da caixa ID de **transação** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione o valor de ID de **transação** devolvido da ação **Enviar IDOC.**

   ![Confirmar ação de ID de transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Após a conclusão desta etapa, a transação atual é marcada em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Veja os problemas e limitações atualmente conhecidos para o conector do SAP:

* O gatilho do SAP não dá suporte a clusters de gateway de dados. Em alguns casos de failover, o nó de gateway de dados que se comunica com o sistema SAP pode diferir do nó ativo, o que resulta em comportamento inesperado. Para cenários de envio, os clusters de gateway de dados são suportados.

* No momento, o conector do SAP não é compatível com cadeia de caracteres do roteador do SAP. O gateway de dados local deve existir na mesma LAN que o sistema SAP que você deseja conectar.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se aos sistemas locais](../logic-apps/logic-apps-gateway-connection.md) a partir de Aplicativos Lógicos Azure.
* Saiba como validar, transformar e usar outras operações de mensagem com o [Pacote de Integração Corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Conheça outros [conectores de aplicativos lógicos.](../connectors/apis-list.md)
