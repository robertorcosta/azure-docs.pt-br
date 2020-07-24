---
title: Conectar-se a sistemas SAP
description: Acesse e gerencie recursos do SAP automatizando fluxos de trabalho com aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: a8985f951b8ff37beb7a1f63e8200321fc706ce6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086601"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure

> [!IMPORTANT]
> Os conectores anteriores do servidor de aplicativos SAP e do SAP Message Server foram preteridos em 29 de fevereiro de 2020. O conector SAP atual consolida esses conectores do SAP anteriores para que você não precise alterar o tipo de conexão, é totalmente compatível com conectores anteriores, fornece muitos recursos adicionais e continua a usar a SAP .net Connector library (SAP NCo).
>
> Para aplicativos lógicos que usam os conectores mais antigos, [migre para o conector mais recente](#migrate) antes da data de reprovação. Caso contrário, esses aplicativos lógicos sofrerão falhas de execução e não poderão enviar mensagens para o sistema SAP.

Este artigo mostra como você pode acessar seus recursos SAP locais de dentro de um aplicativo lógico usando o conector SAP. O conector funciona com as versões clássicas do SAP, como sistemas de R/3 e ECC locais. O conector também permite a integração com os mais recentes sistemas SAP baseados em HANA, como o HANA S/4, onde quer que eles estejam hospedados, no local ou na nuvem. O conector do SAP é compatível com a integração de dados ou de mensagens entre os sistemas baseados no SAP NetWeaver por meio do IDoc (Documento Intermediário) ou BAPI (Interface de programação de aplicativo de negócios) ou RFC (Chamada de função remota).

O conector SAP usa a [biblioteca do NCo (conector do SAP .net)](https://support.sap.com/en/product/connectors/msnet.html) e fornece estas ações:

* **Enviar mensagem para SAP**: enviar IDOC sobre tRFC, chamar funções BAPI sobre a RFC ou chamar RFC/tRFC em sistemas SAP.

* **Quando uma mensagem é recebida do SAP**: receber IDOC sobre tRFC, chamar funções BAPI em tRFC ou chamar RFC/tRFC em sistemas SAP.

* **Gerar esquemas**: gere esquemas para os artefatos do SAP para IDOC, BAPI ou RFC.

Para essas operações, o conector SAP dá suporte à autenticação básica por meio de nomes de acessações e senhas. O conector também dá suporte a [SNC (comunicações de rede segura)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). O SNC pode ser usado para SSO (logon único) do SAP NetWeaver ou para recursos de segurança adicionais fornecidos por um produto de segurança externo.

Este artigo mostra como criar aplicativos lógicos de exemplo que se integram ao SAP ao abordar os cenários de integração descritos anteriormente. Para aplicativos lógicos que usam os conectores do SAP mais antigos, este artigo mostra como migrar seus aplicativos lógicos para o conector SAP mais recente.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir com este artigo, são necessário esses itens:

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja acessar seu sistema SAP e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. Se você for novo em aplicativos lógicos, consulte [o que é o aplicativo lógico do Azure? e o](../logic-apps/logic-apps-overview.md) [início rápido: criar seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Servidor de aplicativos SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [servidor de mensagens SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* O conteúdo da mensagem que você envia para o servidor SAP, como um arquivo IDoc de exemplo, deve estar no formato XML e incluir o namespace para a ação SAP que você deseja usar.

* Para usar o gatilho de **quando uma mensagem é recebida do SAP** , você também precisa executar estas etapas de instalação:
  
  > [!NOTE]
  > Esse gatilho usa o mesmo local de URI para renovar e cancelar a assinatura de uma inscrição de webhook. A operação de renovação usa o `PATCH` método http, enquanto a operação de cancelamento de assinatura usa o `DELETE` método http. Esse comportamento pode fazer com que uma operação de renovação seja exibida como uma operação de cancelamento de assinatura no histórico do gatilho, mas a operação ainda é uma renovação porque o gatilho usa `PATCH` como o método http, não `DELETE` .

  * Configure as permissões de segurança do gateway SAP com esta configuração:

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * Configure o log de segurança do gateway SAP, que ajuda a encontrar erros de ACL (lista de controle de acesso) e não está habilitado por padrão. Caso contrário, você obterá o seguinte erro:

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    Para obter mais informações, consulte o tópico da ajuda do SAP, [Configurando o log do gateway](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>Pré-requisitos do Azure para vários locatários

Esses pré-requisitos se aplicam quando seus aplicativos lógicos são executados no Azure multilocatário e você deseja usar o conector SAP gerenciado, que não é executado nativamente em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Caso contrário, se você estiver usando um ISE de nível Premium e quiser usar o conector SAP que é executado nativamente no ISE, consulte [pré-requisitos do ISE (ambiente do serviço de integração)](#sap-ise).

O conector do SAP gerenciado (não ISE) integra-se com sistemas SAP locais por meio do [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Por exemplo, em cenários de envio de mensagem, quando uma mensagem é enviada de um aplicativo lógico para um sistema SAP, o gateway de dados atua como um cliente RFC e encaminha as solicitações recebidas do aplicativo lógico para o SAP. Da mesma forma, em cenários de mensagem de recebimento, o gateway de dados atua como um servidor RFC que recebe solicitações do SAP e as encaminha para o aplicativo lógico.

* [Baixar e instalar o gateway de dados](../logic-apps/logic-apps-gateway-install.md) local em seu computador local. Em seguida, [crie um recurso de gateway do Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) para esse gateway no portal do Azure. O gateway ajuda você a acessar com segurança os dados e recursos locais.

  Como prática recomendada, certifique-se de usar uma versão com suporte do gateway de dados local. A Microsoft lança uma nova versão todos os meses. Atualmente, a Microsoft dá suporte às últimas seis versões. Se você tiver um problema com seu gateway, tente [atualizar para a versão mais recente](https://aka.ms/on-premises-data-gateway-installer), que pode incluir atualizações para resolver o problema.

* [Baixe e instale a biblioteca de cliente SAP mais recente](#sap-client-library-prerequisites) no mesmo computador que o gateway de dados local.

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>Pré-requisitos do ISE (ambiente do serviço de integração)

Esses pré-requisitos se aplicam quando os seus aplicativos lógicos são executados em um [ambiente de serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)do nível Premium (não no nível do desenvolvedor) e você deseja usar o conector SAP que é executado nativamente em um ISE. Um ISE fornece acesso a recursos que são protegidos por uma rede virtual do Azure e oferece outros conectores do ISE-nativo que permitem que os aplicativos lógicos acessem diretamente os recursos locais sem usar o gateway de dados local.

> [!NOTE]
> Embora o conector do SAP ISE esteja visível dentro de um ISE de nível de desenvolvedor, a tentativa de instalar o conector não terá sucesso.

1. Se você ainda não tiver uma conta de armazenamento do Azure e um contêiner de BLOB, crie esse contêiner usando o [portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) ou [Gerenciador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Baixe e instale a biblioteca de cliente SAP mais recente](#sap-client-library-prerequisites) em seu computador local. Você deve ter os seguintes arquivos de assembly:

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. Crie um arquivo. zip que inclua esses assemblies e carregue esse pacote em seu contêiner de blob no armazenamento do Azure.

1. No portal do Azure ou Gerenciador de Armazenamento do Azure, navegue até o local do contêiner em que você carregou o arquivo. zip.

1. Copie a URL para esse local, certificando-se de incluir o token SAS (assinatura de acesso compartilhado).

   Caso contrário, o token SAS não será autorizado e a implantação para o conector do SAP ISE falhará.

1. Antes de poder usar o conector do ISE do SAP, você precisa instalar e implantar o conector no ISE.

   1. Na [portal do Azure](https://portal.azure.com), localize e abra o ISE.
   
   1. No menu ISE, selecione **conectores gerenciados**  >  **Adicionar**. Na lista de conectores, localize e selecione **SAP**.
   
   1. No painel **Adicionar um novo conector gerenciado** , na caixa **pacote SAP** , Cole a URL do arquivo. zip que tem os assemblies do SAP. *Certifique-se de incluir o token SAS.*

   1. Quando terminar, selecione **Criar**.

   Para obter mais informações, consulte [Adicionar conectores do ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

1. Se sua instância do SAP e o ISE estiverem em redes virtuais diferentes, você também precisará [emparelhar essas redes](../virtual-network/tutorial-connect-virtual-networks-portal.md) para que a rede virtual do ISE esteja conectada à rede virtual da instância do SAP.

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>Pré-requisitos da biblioteca de cliente SAP

* Certifique-se de instalar a versão mais recente, [conector SAP (NCo 3,0) para Microsoft .net 3.0.22.0 compilado com .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019). As versões anteriores podem resultar em problemas de compatibilidade. Para obter mais informações, consulte [versões da biblioteca do cliente SAP](#sap-library-versions).

* Por padrão, o instalador do SAP coloca os arquivos de assembly na pasta de instalação padrão. Você precisa copiar esses arquivos de assembly para outro local, com base em seu cenário, da seguinte maneira:

  Para aplicativos lógicos executados em um ISE, siga as etapas descritas nos [pré-requisitos do ambiente do serviço de integração](#sap-ise). Para aplicativos lógicos que são executados em vários locatários do Azure e usam o gateway de dados local, copie os arquivos de assembly da pasta de instalação padrão para a pasta de instalação do gateway de dados. Se você encontrar problemas com o gateway de dados, examine os seguintes problemas:

  * Você deve instalar a versão de 64 bits para a biblioteca de cliente SAP porque o gateway de dados é executado somente em sistemas de 64 bits. Caso contrário, você obtém um erro de “imagem incorreta” porque o serviço de host do gateway de dados não oferece suporte a assemblies de 32 bits.

  * Se a conexão SAP falhar com a mensagem de erro "Verifique suas informações de conta e/ou permissões e tente novamente", os arquivos de assembly podem estar no local errado. Certifique-se de que você copiou os arquivos de assembly para a pasta de instalação do gateway de dados.

    Para ajudá-lo a solucionar problemas, [use o Visualizador de log de associação de assembly .net](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), que permite verificar se os arquivos do assembly estão no local correto. Opcionalmente, você pode selecionar a opção de **registro de cache de assembly global** ao instalar a biblioteca de cliente SAP.

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>Versões da biblioteca do cliente SAP

Versões anteriores do SAP NCo podem ser bloqueadas quando mais de uma mensagem IDoc é enviada ao mesmo tempo. Essa condição bloqueia todas as mensagens posteriores enviadas ao destino SAP, o que faz com que as mensagens expirem o tempo limite.

Aqui estão as relações entre a biblioteca de cliente SAP, o .NET Framework, o tempo de execução do .NET e o gateway:

* O adaptador do Microsoft SAP e o serviço de host do gateway usam o .NET Framework 4,5.

* O NCo SAP para o .NET Framework 4.0 funciona com processos que usam o runtime do .NET 4.0 para 4.7.1.

* O SAP NCo para .NET Framework 2,0 funciona com processos que usam o tempo de execução do .NET 2,0 para 3,5, mas não funciona mais com o gateway mais recente.

### <a name="secure-network-communications-prerequisites"></a>Pré-requisitos de comunicações de rede segura

Se você usar o gateway de dados local com o SNC (comunicações de rede segura) opcionais, que tem suporte apenas no Azure de vários locatários, também será necessário definir essas configurações:

* Se você usar o SNC com SSO (logon único), verifique se o gateway de dados está sendo executado como um usuário mapeado no usuário SAP. Para alterar a conta padrão, selecione **alterar conta**e insira as credenciais do usuário.

  ![Alterar conta do gateway de dados](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se você habilitar o SNC com um produto de segurança externo, copie a biblioteca de SNC ou os arquivos no mesmo computador em que o gateway de dados está instalado. Alguns exemplos de produtos do SNC incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

Para obter mais informações sobre como habilitar a SNC para o gateway de dados, consulte [habilitar comunicações de rede seguras](#secure-network-communications).

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrar para o conector atual

Para migrar de um conector SAP gerenciado anteriormente (não ISE) para o conector SAP gerenciado atual, siga estas etapas:

1. Se você ainda não fez isso, atualize seu [Gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127) para que você tenha a versão mais recente. Para obter mais informações, consulte [instalar um gateway de dados local para aplicativos lógicos do Azure](../logic-apps/logic-apps-gateway-install.md).

1. No aplicativo lógico que usa o conector do SAP mais antigo, exclua a ação **Enviar para SAP** .

1. A partir do conector SAP mais recente, adicione a ação **Enviar mensagem para SAP** . Para poder usar essa ação, recrie a conexão com o sistema SAP.

1. Quando terminar, salve o aplicativo lógico.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Enviar mensagem ao SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. O aplicativo lógico envia um IDoc para um servidor SAP e retorna uma resposta para o solicitante que chamou o aplicativo lógico.

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

> [!NOTE]
> Quando um aplicativo lógico recebe pacotes IDoc do SAP, o [gatilho de solicitação](../connectors/connectors-native-reqres.md) não dá suporte ao esquema XML "simples" gerado pela documentação do WE60 IDoc da SAP. No entanto, o esquema XML "Plain" tem suporte para cenários que enviam mensagens de aplicativos lógicos *para* o SAP. Você pode usar o gatilho de solicitação com XML IDoc da SAP, mas não com IDoc sobre RFC. Ou, você pode transformar o XML no formato necessário. 

Neste exemplo, você criará um aplicativo lógico com um ponto de extremidade no Azure para seja possível enviar *solicitações HTTP POST* para o aplicativo lógico. Quando seu aplicativo lógico recebe essas solicitações HTTP, o gatilho é acionado e executa a próxima etapa no fluxo de trabalho.

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico.

1. Na caixa de pesquisa, insira `http request` como o filtro. Na lista de **gatilhos** , selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora, salve seu aplicativo lógico para que você possa gerar uma URL de ponto de extremidade para seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Adicionar uma ação SAP

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Caso ainda não tenha adicionado um gatilho ao seu aplicativo lógico e deseje acompanhar esse exemplo [adicione o gatilho descrito nesta seção](#add-trigger).

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Adicionar nova etapa ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista **ações** , selecione **Enviar mensagem para SAP**.
  
   ![Selecione a ação "Enviar mensagem para o SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Ou, você pode selecionar a guia **Enterprise** e selecionar a ação SAP.

   ![Selecione a ação "Enviar mensagem para o SAP" na guia empresa](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se você for solicitado a fornecer detalhes de conexão, forneça as informações para poder criar uma conexão com o servidor SAP local.

   1. Forneça um nome para a conexão.

   1. Se você estiver usando o gateway de dados, siga estas etapas:
   
      1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway de dados que você criou no portal do Azure para a instalação do gateway de dados.
   
      1. Em **Gateway de conexão**, selecione o recurso de gateway de dados no Azure.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :
   
      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Ao terminar, selecione **Criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Agora localize e selecione uma ação do servidor SAP.

   1. Na caixa **ação SAP** , selecione o ícone de pasta. Na lista de arquivos, localize e selecione a mensagem do SAP que você deseja usar. Para navegar pela lista, use as setas.

      Este exemplo seleciona um IDoc com o tipo **Orders** .

      ![Localizar e selecionar a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se não encontrar a ação desejada, é possível inserir um caminho manualmente, por exemplo:

      ![Fornecer manualmente o caminho para a ação de IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Forneça o valor para a **ação do SAP** por meio do editor de expressão. Dessa forma, você pode usar a mesma ação para tipos de mensagens diferentes.

      Para obter mais informações sobre operações de IDoc, consulte [esquemas de mensagem para operações de IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Clique dentro da caixa **Mensagem de entrada** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Quando uma solicitação HTTP é recebida**, selecione o campo **Corpo**.

      Esta etapa inclui o conteúdo do corpo do gatilho de solicitação HTTP e envia essa saída para o servidor SAP.

      ![Selecione a propriedade "corpo" do gatilho](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Quando terminar, sua ação SAP será parecida com este exemplo:

      ![Concluir a ação SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Adicionar uma ação de resposta HTTP

Agora, adicione uma ação de resposta ao fluxo de trabalho do aplicativo lógico e inclua a saída de uma ação SAP. Dessa forma, seu aplicativo lógico retorna os resultados do seu servidor SAP para o solicitante original.

1. No designer de aplicativo lógico, na ação SAP, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `response` como o filtro. Na lista **ações** , selecione **resposta**.

1. Clique dentro da caixa **Corpo** para que seja exibida a lista de conteúdo dinâmica. Nessa lista, em **Enviar mensagem para SAP**, selecione o campo **corpo** .

   ![Concluir ação SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salve seu aplicativo lógico.

#### <a name="add-rfc-request-response"></a>Adicionar solicitação RFC-resposta

> [!NOTE]
> O gatilho SAP recebe IDocs sobre tRFC, que não tem um parâmetro de resposta por design. 

Você deve criar um padrão de solicitação e resposta se precisar receber respostas usando uma RFC (chamada de função remota) para aplicativos lógicos do SAP ABAP. Para receber IDocs em seu aplicativo lógico, você deve fazer sua primeira ação uma [solicitação HTTP](../connectors/connectors-native-reqres.md#add-a-response-action) com um código de status `200 OK` e nenhum conteúdo. Essa etapa recomendada conclui a transferência assíncrona do SAP LUW sobre tRFC imediatamente, o que deixa a conversa do SAP comunicação CPIC disponível novamente. Você pode adicionar mais ações em seu aplicativo lógico para processar o IDoc recebido sem bloquear outras transferências.

Para implementar um padrão de solicitação e resposta, primeiro você deve descobrir o esquema RFC usando o [ `generate schema` comando](#generate-schemas-for-artifacts-in-sap). O esquema gerado tem dois nós raiz possíveis: 

1. O nó de solicitação, que é a chamada que você recebe do SAP.
1. O nó de resposta, que é a resposta de volta para o SAP.

No exemplo a seguir, um padrão de solicitação e resposta é gerado a partir do `STFC_CONNECTION` módulo RFC. O XML de solicitação é analisado para extrair um valor de nó no qual as solicitações SAP `<ECHOTEXT>` . A resposta insere o carimbo de data/hora atual como um valor dinâmico. Você recebe uma resposta semelhante ao enviar uma `STFC_CONNECTION` RFC de um aplicativo lógico para o SAP.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico ainda não estiver habilitado, no menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **habilitar**.

1. Na barra de ferramentas do designer, selecione **executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Dispare seu aplicativo lógico enviando uma solicitação HTTP POST para a URL em seu gatilho de solicitação HTTP.
Inclua o conteúdo da mensagem com sua solicitação. Para enviar a solicitação, você pode usar uma ferramenta como [Postman](https://www.getpostman.com/apps).

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

Agora você criou um aplicativo lógico que pode se comunicar com o servidor SAP. Agora que você configurou uma conexão SAP para seu aplicativo lógico, é possível explorar outras ações SAP disponíveis, como BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Receber mensagem do SAP

Este exemplo usa um aplicativo lógico que é disparado quando o aplicativo recebe uma mensagem de um sistema SAP.

### <a name="add-an-sap-trigger"></a>Adicionar um gatilho SAP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista de **gatilhos** , selecione **quando uma mensagem é recebida do SAP**.

   ![Adicionar gatilho do SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Ou, você pode selecionar a guia **Enterprise** e, em seguida, selecionar o gatilho:

   ![Adicionar gatilho SAP da guia Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se os detalhes de conexão forem solicitados, forneça as informações para que você possa criar uma conexão com o servidor SAP local agora.

   1. Forneça um nome para a conexão.

   1. Se você estiver usando o gateway de dados, siga estas etapas:

      1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway de dados que você criou no portal do Azure para a instalação do gateway de dados.

      1. Em **Gateway de conexão**, selecione o recurso de gateway de dados no Azure.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :

      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Ao terminar, selecione **Criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Forneça os [parâmetros necessários](#parameters) com base na configuração do sistema SAP. 

   Você pode [filtrar as mensagens que recebe do servidor SAP, especificando uma lista de ações SAP](#filter-with-sap-actions).

   É possível selecionar uma ação do SAP no seletor de arquivos:

   ![Adicionar ação SAP ao aplicativo lógico](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Ou, você pode especificar uma ação manualmente:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Veja um exemplo que mostra como a ação é exibida quando você configura o gatilho para receber mais de uma mensagem.

   ![Exemplo de gatilho que recebe várias mensagens](media/logic-apps-using-sap-connector/example-trigger.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações de IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Agora salve seu aplicativo lógico para poder começar a receber mensagens do seu sistema SAP. Selecione **Salvar** na barra de ferramentas do designer.

Agora seu aplicativo lógico está pronto para receber mensagens do seu sistema SAP.

> [!NOTE]
> O gatilho SAP não é um gatilho de sondagem, mas é um gatilho baseado em webhook. Se você estiver usando o gateway de dados, o gatilho será chamado do gateway de dados somente quando uma mensagem existir, portanto, nenhuma sondagem será necessária.

<a name="parameters"></a>

#### <a name="parameters"></a>Parâmetros

Juntamente com as entradas de cadeia de caracteres e números simples, o conector SAP aceita os seguintes parâmetros de tabela ( `Type=ITAB` entradas):

* Parâmetros de direção de tabela, entrada e saída, para versões mais antigas do SAP.
* Alterando parâmetros, que substituem os parâmetros de direção da tabela para versões mais recentes do SAP.
* Parâmetros de tabela hierárquica

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>Filtrar com ações do SAP

Opcionalmente, você pode filtrar as mensagens que seu aplicativo lógico recebe do servidor SAP, fornecendo uma lista, ou matriz, com uma única ou várias ações SAP. Por padrão, essa matriz está vazia, o que significa que seu aplicativo lógico recebe todas as mensagens do servidor SAP sem filtragem. 

Quando você configura o filtro de matriz, o gatilho recebe apenas mensagens dos tipos de ação SAP especificados e rejeita todas as outras mensagens do servidor SAP. No entanto, esse filtro não afeta se a digitação da carga recebida é fraca ou forte.

Qualquer filtragem de ação SAP ocorre no nível do adaptador SAP para seu gateway de dados local. Para obter mais informações, consulte [como enviar IDocs de teste para aplicativos lógicos do SAP](#send-idocs-from-sap).

Se você não puder enviar pacotes IDoc do SAP para o gatilho do seu aplicativo lógico, consulte a mensagem de rejeição da chamada RFC (tRFC) transacional na caixa de diálogo SAP tRFC (T-Code SM58). Na interface SAP, você pode receber as seguintes mensagens de erro, que são recortadas devido aos limites de subcadeias de caracteres no campo de **texto status** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Falhas inesperadas ocorrem quando o manipulador catch-all do canal termina o canal devido a um erro e recria o canal para processar outras mensagens.

  * Para confirmar que seu aplicativo lógico recebeu o IDoc, [adicione uma ação de resposta](../connectors/connectors-native-reqres.md#add-a-response-action) que retorna um `200 OK` código de status. O IDoc é transportado por meio de tRFC, que não permite uma carga de resposta.

  * Se você precisar rejeitar o IDoc em vez disso, responda com qualquer código de status HTTP diferente de `200 OK` para que o adaptador SAP retorne uma exceção ao SAP em seu nome. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Falhas esperadas ocorrem com outros erros, como a falha ao gerar uma carga XML IDoc porque seus segmentos não são liberados pelo SAP, portanto, os metadados de tipo de segmento necessários para a conversão estão ausentes. 

  * Para que esses segmentos sejam liberados pelo SAP, entre em contato com o engenheiro do ABAP para seu sistema SAP.

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>Localizar logs de erros estendidos

Para obter mensagens de erro completas, verifique os logs estendidos do adaptador SAP. 

Para versões do gateway de dados locais de junho de 2020 e posteriores, você pode [habilitar os logs de gateway nas configurações do aplicativo](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

Para versões do gateway de dados locais de abril de 2020 e anteriores, os logs são desabilitados por padrão. Para recuperar logs estendidos, siga estas etapas:

1. Na pasta de instalação do gateway de dados local, abra o `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` arquivo. 

1. Para a configuração **SapExtendedTracing** , altere o valor de **false** para **true**.

1. Opcionalmente, para menos eventos, altere o valor de **SapTracingLevel** de **informativo** (padrão) para **erro** ou **aviso**. Ou, para obter mais eventos, altere **informativo** para **detalhado**.

1. Salve o arquivo de configuração.

1. Reinicie o gateway de dados. Abra seu aplicativo de instalador de gateway de dados local e vá para o menu **configurações de serviço** . Em **reiniciar o gateway**, selecione **reiniciar agora**.

1. Reproduza o problema.

1. Exporte seus logs de gateway. No aplicativo instalador do gateway de dados, vá para o menu **diagnóstico** . Em **logs de Gateway**, selecione **Exportar logs**. Esses arquivos incluem logs do SAP organizados por data. Dependendo do tamanho do log, vários arquivos de log podem existir para uma única data.

1. No arquivo de configuração, reverta a configuração **SapExtendedTracing** para **false**.

1. Reinicie o serviço do gateway.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Para disparar seu aplicativo lógico, envie uma mensagem do sistema SAP.

1. No menu do aplicativo lógico, selecione **visão geral**. Examine o **histórico de execuções** para qualquer nova execução para seu aplicativo lógico.

1. Abra a execução mais recente, que mostra a mensagem enviada do seu sistema SAP na seção de saídas de gatilho.

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>Testar o envio de IDocs do SAP

Para enviar IDocs do SAP para seu aplicativo lógico, você precisará da seguinte configuração mínima:

> [!IMPORTANT]
> Use estas etapas somente quando você testar a configuração do SAP com seu aplicativo lógico. Os ambientes de produção exigem configuração adicional.

1. [Configurar um destino RFC no SAP](#create-rfc-destination)

1. [Criar uma conexão ABAP para seu destino RFC](#create-abap-connection)

1. [Criar uma porta do destinatário](#create-receiver-port)

1. [Criar uma porta de remetente](#create-sender-port)

1. [Criar um parceiro de sistema lógico](#create-logical-system-partner)

1. [Criar um perfil de parceiro](#create-partner-profiles)

1. [Testar mensagens de envio](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Criar destino RFC

1. Para abrir a **configuração das configurações de conexões do RFC** , na interface do SAP, use o código de transação **sm59** (T-Code) com o prefixo **/n** .

1. Selecione **conexões TCP/IP**  >  **criar**.

1. Crie um novo destino RFC com as seguintes configurações:
    
    * Para o **destino RFC**, insira um nome.
    
    * Na guia **configurações técnicas** , para **tipo de ativação**, selecione **programa de servidor registrado**. Para a **ID do programa**, insira um valor. No SAP, o gatilho do aplicativo lógico será registrado usando esse identificador.
    
    * Na guia **Unicode** , para **tipo de comunicação com sistema de destino**, selecione **Unicode**.

1. Salve as alterações.

1. Registre sua nova **ID de programa** com os aplicativos lógicos do Azure.

1. Para testar sua conexão, na interface SAP, em seu novo **destino RFC**, selecione **teste de conexão**.

#### <a name="create-abap-connection"></a>Criar conexão ABAP

1. Para abrir a **configuração das configurações de conexões RFC** , na sua interface SAP, use o código de transação **Sm59*** (T-Code) com o prefixo **/n** .

1. Selecione **conexões ABAP**  >  **criar**.

1. Para **destino RFC**, insira o identificador para [o sistema SAP de teste](#create-rfc-destination).

1. Salve as alterações.

1. Para testar sua conexão, selecione **teste de conexão** .

#### <a name="create-receiver-port"></a>Criar porta do destinatário

1. Para abrir as **portas nas** configurações de processamento do IDOC, na sua interface SAP, use o código de transação **we21** (T-Code) com o prefixo **/n** .

1. Selecione **porta**de  >  **criação RFC transacional**  >  **Create**.

1. Na caixa configurações que é aberta, selecione o **próprio nome de porta**. Para sua porta de teste, insira um **nome**. Salve as alterações.

1. Nas configurações de sua nova porta do destinatário, para **destino RFC**, insira o identificador para [o destino RFC de teste](#create-rfc-destination).

1. Salve as alterações.

#### <a name="create-sender-port"></a>Criar porta do remetente

1.  Para abrir as **portas nas** configurações de processamento do IDOC, na sua interface SAP, use o código de transação **we21** (T-Code) com o prefixo **/n** .

1. Selecione **porta**de  >  **criação RFC transacional**  >  **Create**.

1. Na caixa configurações que é aberta, selecione o **próprio nome de porta**. Para sua porta de teste, insira um **nome** que comece com **SAP**. Todos os nomes de porta de remetente devem começar com as letras **SAP**, por exemplo, **SAPTEST**. Salve as alterações.

1. Nas configurações de sua nova porta de remetente, para **destino RFC**, insira o identificador para [sua conexão ABAP](#create-abap-connection).

1. Salve as alterações.

#### <a name="create-logical-system-partner"></a>Criar parceiro de sistema lógico

1. Para abrir a **exibição de alteração "sistemas lógicos": configurações de visão geral** , na sua interface SAP, use o código de transação **bd54** (T-Code).

1. Aceite a mensagem de aviso que aparece: **cuidado: a tabela é cliente cruzado**

1. Acima da lista que mostra os sistemas lógicos existentes, selecione **novas entradas**.

1. Para seu novo sistema lógico, insira um **Log.Sys** identificador de tem e uma descrição de **nome** curta. Salve as alterações.

1. Quando o **prompt do Workbench** for exibido, crie uma nova solicitação fornecendo uma descrição ou, se você já tiver criado uma solicitação, ignore esta etapa.

1. Depois de criar a solicitação do Workbench, vincule essa solicitação à solicitação de atualização de tabela. Para confirmar que a tabela foi atualizada, salve suas alterações.

#### <a name="create-partner-profiles"></a>Criar perfis de parceiro

Para ambientes de produção, você deve criar dois perfis de parceiro. O primeiro perfil é para o remetente, que é a sua organização e o sistema SAP. O segundo perfil é para o receptor, que é seu aplicativo lógico.

1. Para abrir as configurações de **perfis de parceiro** , na sua interface SAP, use o código de transação **we20** (T-Code) com o prefixo **/n** .

1. Em **perfis de parceiro**, selecione **tipo de parceiro ls**  >  **criar**.

1. Crie um novo perfil de parceiro com as seguintes configurações:

    * Para o **parceiro não**, insira [o identificador do parceiro do sistema lógico](#create-logical-system-partner).

    * Para **partn. Digite**, digite **ls**.

    * Para o **Agent**, insira o identificador da conta de usuário SAP a ser usado ao registrar identificadores de programa para aplicativos lógicos do Azure ou outros sistemas não SAP.

1. Salve as alterações. Se você não [criou o parceiro do sistema lógico](#create-logical-system-partner), você obtém o erro, **Insira um número de parceiro válido**.

1. Nas configurações de seu perfil de parceiro, em **parmtrs de saída.**, selecione **criar parâmetro de saída**.

1. Crie um novo parâmetro de saída com as seguintes configurações:

    * Insira seu **tipo de mensagem**, por exemplo, **CREMAS**.

    * Insira o [identificador da porta do destinatário](#create-receiver-port).

    * Insira um tamanho de IDoc para o **pacote. Tamanho**. Ou, para [Enviar IDocs um de cada vez do SAP](#receive-idoc-packets-from-sap), selecione **Pass IDOC imediatamente**.

1. Salve as alterações.

#### <a name="test-sending-messages"></a>Testar mensagens de envio

1. Para abrir a **ferramenta de teste para configurações de processamento IDOC** , na sua interface SAP, use o código de transação **WE19** (T-Code) com o prefixo **/n** .

1. Em **modelo para teste**, selecione **por meio do tipo de mensagem**e insira seu tipo de mensagem, por exemplo, **CREMAS**. Selecione **Criar**.

1. Confirme a mensagem **que tipo de IDOC?** , selecionando **continuar**.

1. Selecione o nó **EDIDC** . Insira os valores apropriados para o receptor e as portas do remetente. Selecione **Continuar**.

1. Selecione **processamento de saída padrão**.

1. Para iniciar o processamento de IDoc de saída, selecione **continuar**. Quando o processamento for concluído, a mensagem **IDOC enviada para o sistema SAP ou o programa externo** será exibida.

1.  Para verificar se há erros de processamento, use o código de transação **SM58** (T-Code) com o prefixo **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Receber pacotes IDoc do SAP

Você pode configurar o SAP para [Enviar IDocs em pacotes](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), que são lotes ou grupos de IDocs. Para receber pacotes IDoc, o conector SAP e, especificamente, o gatilho, não precisa de configuração adicional. No entanto, para processar cada item em um pacote IDoc depois que o gatilho recebe o pacote, algumas etapas adicionais são necessárias para dividir o pacote em IDocs individuais.

Aqui está um exemplo que mostra como extrair IDocs individuais de um pacote usando a [ `xpath()` função](./workflow-definition-language-functions-reference.md#xpath):

1. Antes de começar, você precisa de um aplicativo lógico com um gatilho SAP. Se você ainda não tiver esse aplicativo lógico, siga as etapas anteriores neste tópico para [configurar um aplicativo lógico com um gatilho do SAP](#receive-from-sap).

   Por exemplo:

   ![Adicionar gatilho SAP ao aplicativo lógico](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Obtenha o namespace raiz do IDoc XML que seu aplicativo lógico recebe do SAP. Para extrair esse namespace do documento XML, adicione uma etapa que cria uma variável de cadeia de caracteres local e armazena esse namespace usando uma `xpath()` expressão:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Obter namespace de raiz de IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extrair um IDoc individual, adicione uma etapa que cria uma variável de matriz e armazena a coleção IDoc usando outra `xpath()` expressão:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Obter matriz de itens](./media/logic-apps-using-sap-connector/get-array.png)

   A variável de matriz torna cada IDoc disponível para o aplicativo lógico processar individualmente, enumerando sobre a coleção. Neste exemplo, o aplicativo lógico transfere cada IDoc para um servidor SFTP usando um loop:

   ![Enviar IDoc para o servidor SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDoc deve incluir o namespace raiz, que é o motivo pelo qual o conteúdo do arquivo é encapsulado dentro de um `<Receive></Receive` elemento junto com o namespace raiz antes de enviar o IDOC para o aplicativo downstream, ou para o servidor SFTP nesse caso.

Você pode usar o modelo de início rápido para esse padrão selecionando este modelo no designer de aplicativo lógico ao criar um novo aplicativo lógico.

![Selecionar modelo de aplicativo lógico do lote](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Gerar esquemas para artefatos no SAP

Este exemplo usa um aplicativo lógico que pode ser disparado com uma solicitação HTTP. Para gerar os esquemas para o IDoc e o BAPI especificados, o esquema de **geração** de ação SAP envia uma solicitação para um sistema SAP.

Essa ação SAP retorna um [esquema XML](#sample-xml-schemas), não o conteúdo ou os dados do documento XML em si. Os esquemas retornados na resposta são carregados em uma conta de integração usando o conector de Azure Resource Manager. Os esquemas contêm as seguintes partes:

* A estrutura da mensagem de solicitação. Use essas informações para formar sua lista de BAPI `get` .
* A estrutura da mensagem de resposta. Use essas informações para analisar a resposta. 

Para enviar a mensagem de solicitação, use a ação genérica do SAP **Enviar mensagem para SAP**ou as ações **BAPI de chamada** de destino.

### <a name="sample-xml-schemas"></a>Esquemas XML de exemplo

Se você estiver aprendendo a gerar um esquema XML para uso na criação de um documento de exemplo, consulte os exemplos a seguir. Esses exemplos mostram como você pode trabalhar com muitos tipos de conteúdo, incluindo:

* [Solicitações RFC](#xml-samples-for-rfc-requests)
* [Solicitações BAPI](#xml-samples-for-bapi-requests)
* [Solicitações IDoc](#xml-samples-for-idoc-requests)
* Tipos de dados de esquema XML simples ou complexos
* Parâmetros de tabela
* Comportamentos XML opcionais

Você pode iniciar o esquema XML com um prólogo XML opcional. O conector SAP funciona com ou sem o prólogo XML.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>Exemplos de XML para solicitações RFC

O exemplo a seguir é uma chamada RFC básica. O nome da RFC é `STFC_CONNECTION` . Essa solicitação usa o namespace padrão `xmlns=` , no entanto, você pode atribuir e usar aliases de namespace, como `xmmlns:exampleAlias=` . O valor do namespace é o namespace para todos os RFCs no SAP para serviços Microsoft. Há um parâmetro de entrada simples na solicitação, `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

O exemplo a seguir é uma chamada RFC com um parâmetro Table. Este exemplo de chamada e seu grupo de RFCs de teste estão disponíveis como parte de todos os sistemas SAP. O nome do parâmetro de tabela é `TCPICDAT` . O tipo de linha de tabela é `ABAPTEXT` , e esse elemento se repete para cada linha na tabela. Este exemplo contém uma única linha, chamada `LINE` . As solicitações com um parâmetro de tabela podem conter qualquer número de campos, em que o número é um inteiro positivo (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

O exemplo a seguir é uma chamada RFC com um parâmetro de tabela que tem um campo anônimo. Um campo anônimo é quando o campo não tem nenhum nome atribuído. Tipos complexos são declarados em um namespace separado, no qual a declaração define um novo padrão para o nó atual e todos os seus elementos filho. O exemplo usa o código hex `x002F` como um caractere de escape para o símbolo */* , pois esse símbolo é reservado no nome do campo SAP.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

O exemplo a seguir inclui prefixos para os namespaces. Você pode declarar todos os prefixos de uma vez ou pode declarar qualquer quantidade de prefixos como atributos de um nó. O alias do namespace RFC `ns0` é usado como a raiz e os parâmetros para o tipo básico. Observe que os tipos complexos são declarados em um namespace diferente para tipos RFC com o alias `ns3` em vez do namespace RFC regular com o alias `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>Exemplos de XML para solicitações BAPI

> [!TIP]
> Se você estiver usando o designer de aplicativos lógicos para editar sua solicitação de BAPI, poderá usar as seguintes funções de pesquisa: 
> 
> * Selecione um objeto no designer para ver um menu suspenso dos métodos disponíveis.
> * Filtre tipos de objeto de negócios por palavra-chave usando a lista pesquisável fornecida pela chamada à API BAPI.

> [!NOTE]
> O SAP disponibiliza os objetos comerciais para sistemas externos, descrevendo-os em resposta à RFC `RPY_BOR_TREE_INIT` , quais aplicativos lógicos apresentam problemas sem nenhum filtro de entrada. Os aplicativos lógicos inspecionam a tabela de saída `BOR_TREE` . O `SHORT_TEXT` campo é usado para nomes de objetos comerciais. Os objetos comerciais não retornados pelo SAP na tabela de saída não estão acessíveis a aplicativos lógicos.
> Se você usar objetos comerciais personalizados, deverá se certificar de publicar e liberar esses objetos comerciais no SAP. Caso contrário, o SAP não lista os objetos comerciais personalizados na tabela de saída `BOR_TREE` . Você não pode acessar seus objetos comerciais personalizados em aplicativos lógicos até expor os objetos comerciais do SAP. 

O exemplo a seguir obtém uma lista de bancos usando o método BAPI `GETLIST` . Este exemplo contém o objeto comercial para um banco, `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

O exemplo a seguir cria um objeto de banco usando o `CREATE` método. Este exemplo usa o mesmo objeto comercial do exemplo anterior, `BUS1011` . Ao usar o `CREATE` método para criar um banco, certifique-se de confirmar suas alterações porque esse método não é confirmado por padrão.

> [!TIP]
> Certifique-se de que o documento XML segue as regras de validação configuradas no sistema SAP. Por exemplo, neste documento de exemplo, a chave do banco ( `<BANK_KEY>` ) precisa ser um número de roteamento bancário, também conhecido como um número aba, nos EUA.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

O exemplo a seguir obtém detalhes de um banco usando o número de roteamento bancário, o valor de `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>Exemplos de XML para solicitações IDoc

Para gerar um esquema XML IDoc SAP simples, use o aplicativo de **logon do SAP** e o código T `WE-60` . Acesse a documentação do SAP por meio da GUI e gere esquemas XML no formato XSD para seus tipos e extensões de IDoc. Para obter uma explicação sobre as cargas e os formatos genéricos do SAP e suas caixas de diálogo internas, consulte a [documentação do SAP](https://help.sap.com/viewer/index).

Este exemplo declara o nó raiz e os namespaces. O URI no código de exemplo, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` , declara a seguinte configuração:

* `/IDoc`é a observação raiz para todos os IDocs
* `/3`é a versão de tipos de registro para definições de segmento comuns
* `/ORDERS05`é o tipo IDoc
* `//`é um segmento vazio, porque não há extensão IDoc
* `/700`é a versão do SAP
* `/Send`é a ação para enviar as informações para o SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Você pode repetir o `idocData` nó para enviar um lote de IDocs em uma única chamada. No exemplo a seguir, há um registro de controle, `EDI_DC40` e vários registros de dados.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

O exemplo a seguir é um registro de controle IDoc de exemplo, que usa o prefixo `EDI_DC` . Você deve atualizar os valores para corresponder à instalação do SAP e ao tipo IDoc. Por exemplo, seu código de cliente IDoc pode não ser `800` . Entre em contato com sua equipe SAP para verificar se você está usando os valores corretos para a instalação do SAP.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

O exemplo a seguir é um registro de dados de exemplo com segmentos simples. Este exemplo usa o formato de data do SAP. Documentos de tipo forte podem usar formatos de data XML nativos, como `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

O exemplo a seguir é um registro de dados com segmentos agrupados. Isso inclui um nó pai do grupo, `E2EDKT1002GRP` e vários nós filho, incluindo `E2EDKT1002` e `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

O método recomendado é criar um identificador de IDoc para uso com tRFC. Você pode definir esse identificador de transação, `tid` usando a [operação enviar IDOC](https://docs.microsoft.com/connectors/sap/#send-idoc) na API do conector SAP.

O exemplo a seguir é um método alternativo para definir o identificador da transação ou `tid` . Neste exemplo, o nó de segmento do último registro de dados e o nó de dados IDoc são fechados. Em seguida, o GUID, `guid` , é usado como o identificador tRFC para detectar duplicatas. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>Adicionar um gatilho de solicitação HTTP

1. No portal do Azure, crie um aplicativo lógico em branco, o que abrirá o Designer do Aplicativo Lógico.

1. Na caixa de pesquisa, insira `http request` como o filtro. Na lista de **gatilhos** , selecione **quando uma solicitação HTTP é recebida**.

   ![Adicionar gatilho de solicitação HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Agora salve seu aplicativo lógico para poder gerar uma URL de ponto de extremidade para seu aplicativo lógico.
Selecione **Salvar** na barra de ferramentas do designer.

   O ponto de extremidade de URL agora aparece em seu gatilho, por exemplo:

   ![Gerar a URL do ponto de extremidade](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adicionar uma ação SAP para gerar esquemas

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

   ![Adicionar nova etapa ao aplicativo lógico](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Na caixa de pesquisa, insira `sap` como o filtro. Na lista **ações** , selecione **gerar esquemas**.
  
   ![Adicionar a ação "gerar esquemas" ao aplicativo lógico](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Ou, você pode selecionar a guia **Enterprise** e selecionar a ação SAP.

   ![Selecionar ação de envio do SAP na guia Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se sua conexão já existir, continue com a próxima etapa para que você possa configurar sua ação SAP. No entanto, se os detalhes de conexão forem solicitados, forneça as informações para que você possa criar uma conexão com o servidor SAP local agora.

   1. Forneça um nome para a conexão.

   1. Na seção **Gateway de dados** , em **assinatura**, primeiro selecione a assinatura do Azure para o recurso de gateway de dados que você criou no portal do Azure para a instalação do gateway de dados. 
   
   1. Em **Gateway de conexão**, selecione o recurso de gateway de dados no Azure.

   1. Continue fornecendo informações sobre a conexão. Para a propriedade **tipo de logon** , siga a etapa com base em se a propriedade está definida como servidor ou **grupo**de **aplicativos** :
   
      * Para o **servidor de aplicativos**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de aplicativos SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para o **grupo**, essas propriedades, que geralmente aparecem opcionais, são necessárias:

        ![Criar conexão do servidor de mensagens SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Por padrão, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Saiba mais sobre a [opção de digitação segura](#safe-typing).

   1. Ao terminar, selecione **Criar**.

      Os aplicativos lógicos configuram e testam sua conexão para garantir que a conexão funcione corretamente.

1. Forneça o caminho para o artefato para o qual você deseja gerar o esquema.

   É possível selecionar a ação do SAP no seletor de arquivos:

   ![Adicionar ação do SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Ou é possível inserir manualmente a ação:

   ![Inserir manualmente a ação do SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para gerar esquemas para mais de um artefato, forneça os detalhes da ação do SAP para cada artefato, por exemplo:

   ![Selecionar Adicionar novo item](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrar dois itens](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para obter mais informações sobre a ação SAP, consulte [esquemas de mensagem para operações de IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Na barra de ferramentas do designer, selecione **executar** para disparar uma execução para seu aplicativo lógico.

1. Abra a execução e verifique as saídas para a ação **gerar esquemas** .

   As saídas mostram os esquemas gerados para a lista de mensagens especificada.

### <a name="upload-schemas-to-an-integration-account"></a>Carregar esquemas para uma conta de integração

Opcionalmente, é possível baixar ou armazenar os esquemas gerados em repositórios, como um blob, armazenamento ou conta de integração. As contas de integração oferecem uma experiência de primeira classe com outras ações de XML; portanto, este exemplo mostra como carregar esquemas para uma conta de integração para o mesmo aplicativo lógico usando o conector do Azure Resource Manager.

1. No designer do aplicativo lógico, no gatilho, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `Resource Manager` como o filtro. Selecione **criar ou atualizar um recurso**.

   ![Selecionar ação do Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Insira os detalhes da ação, incluindo sua assinatura do Azure, grupo de recursos do Azure e conta de integração. Para adicionar os tokens do SAP aos campos, clique dentro das caixas para esses campos e selecione na lista de conteúdo dinâmico que aparece.

   1. Abra a lista **Adicionar novo parâmetro** e selecione os campos **local** e **Propriedades** .

   1. Forneça detalhes para esses novos campos, conforme mostrado neste exemplo.

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

1. Na barra de ferramentas do designer, selecione **executar** para disparar manualmente seu aplicativo lógico.

1. Após uma execução bem-sucedida, vá para a conta de integração e verifique se os esquemas gerados existem.

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>Habilitar comunicações de rede seguras

Antes de começar, certifique-se de que você atende aos [pré-requisitos](#pre-reqs)listados anteriormente, que se aplicam somente quando você usa o gateway de dados e seus aplicativos lógicos são executados no Azure de vários locatários:

* Verifique se o gateway de dados local está instalado em um computador que está na mesma rede que o seu sistema SAP.

* Para SSO (logon único), o gateway de dados é executado como um usuário que é mapeado para um usuário SAP.

* A biblioteca SNC que fornece as funções de segurança adicionais é instalada no mesmo computador que o gateway de dados. Alguns exemplos incluem [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Para habilitar o SNC para suas solicitações de ou para o sistema SAP, marque a caixa de seleção **usar SNC** na conexão SAP e forneça estas propriedades:

   ![Configurar o SAP SNC na conexão](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propriedade | Descrição |
   |----------| ------------|
   | **Caminho da biblioteca SNC** | O nome ou caminho da biblioteca SNC relativo ao local de instalação do NCo ou ao caminho absoluto. Os exemplos são `sapsnc.dll` ou `.\security\sapsnc.dll` ou `c:\security\sapsnc.dll` . |
   | **SSO DO SNC** | Quando você se conecta por meio do SNC, a identidade da SNC normalmente é usada para autenticar o chamador. Outra opção é substituir para que as informações de usuário e senha possam ser usadas para autenticar o chamador, mas a linha ainda está criptografada. |
   | **Meu nome do SNC** | Na maioria dos casos, essa propriedade pode ser omitida. A solução SNC instalada geralmente sabe seu próprio nome de SNC. Somente para soluções que dão suporte a várias identidades, talvez seja necessário especificar a identidade a ser usada para esse destino ou servidor específico. |
   | **Nome do parceiro SNC** | O nome da SNC de back-end. |
   | **Qualidade de proteção da SNC** | A qualidade do serviço a ser usada para comunicação de SNC desse servidor ou destino específico. O valor padrão é definido pelo sistema back-end. O valor máximo é definido pelo produto de segurança usado para a SNC. |
   |||

   > [!NOTE]
   > Não defina as variáveis de ambiente SNC_LIB e SNC_LIB_64 no computador em que você tem o gateway de dados e a biblioteca do SNC. Se definido, eles terão precedência sobre o valor da biblioteca SNC passado pelo conector.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Digitação segura

Por padrão, quando você cria sua conexão SAP, a tipagem forte é usada para verificar valores inválidos executando a validação de XML no esquema. Esse comportamento pode ajudá-lo a detectar problemas anteriormente. A opção de **tipagem segura** está disponível para compatibilidade com versões anteriores e verifica apenas o comprimento da cadeia de caracteres. Se você escolher a **digitação segura**, o tipo de DATs e o tipo TIMS no SAP serão tratados como cadeias de caracteres em vez de como seus equivalentes XML `xs:date` e `xs:time` , em que `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . A digitação segura afeta o comportamento de toda a geração de esquema, a mensagem de envio para a carga "enviada" e a resposta "foi recebida" e o gatilho. 

Quando a digitação forte é usada (a**digitação segura** não está habilitada), o esquema MAPEIA os DATs e os tipos TIMS para tipos XML mais diretos:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando você envia mensagens usando tipagem forte, os DATS e a resposta TIMS estão em conformidade com o formato de tipo XML correspondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando a **digitação segura** está habilitada, o esquema MAPEIA os DATs e os tipos TIMS para campos de cadeia de caracteres XML com restrições de comprimento apenas, por exemplo:

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

Quando as mensagens são enviadas com a **digitação segura** habilitada, a resposta de DATs e TIMS é semelhante a este exemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="change-language-headers"></a>Alterar cabeçalhos de idioma

Quando você se conecta ao SAP de aplicativos lógicos, o idioma padrão da conexão é o inglês. Você pode definir o idioma para sua conexão usando [o cabeçalho `Accept-Language` http padrão](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) com suas solicitações de entrada.

> [!TIP]
> A maioria dos navegadores da Web adiciona um `Accept-Language` cabeçalho com base nas configurações do usuário. O navegador da Web aplica esse cabeçalho quando você cria uma nova conexão SAP no designer de aplicativos lógicos. Se você não quiser criar conexões SAP no idioma preferencial do seu navegador da Web, atualize as configurações do navegador da Web para usar seu idioma preferencial ou crie sua conexão SAP usando Azure Resource Manager em vez do designer de aplicativos lógicos. 

Por exemplo, você pode enviar uma solicitação com o `Accept-Language` cabeçalho para seu aplicativo lógico usando o gatilho de **solicitação HTTP** . Todas as ações em seu aplicativo lógico recebem o cabeçalho. Em seguida, o SAP usa os idiomas especificados em suas mensagens do sistema, como mensagens de erro do BAPI.

Os parâmetros de conexão SAP para um aplicativo lógico não têm uma propriedade Language. Portanto, se você usar o `Accept-Language` cabeçalho, poderá obter o seguinte erro: **Verifique as informações de sua conta e/ou as permissões e tente novamente.** Nesse caso, verifique os logs de erros do componente SAP em vez disso. O erro realmente acontece no componente SAP que usa o cabeçalho, portanto, você pode obter uma dessas mensagens de erro:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Confirmar transação explicitamente

Quando você envia transações para o SAP de aplicativos lógicos, essa troca ocorre em duas etapas, conforme descrito no documento SAP, [programas de servidor RFC transacionais](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Por padrão, a ação **Enviar para SAP lida com** as etapas para a transferência de função e para a confirmação de transação em uma única chamada. O conector SAP oferece a opção de desacoplar essas etapas. Você pode enviar um IDoc e, em vez de confirmar automaticamente a transação, pode usar a ação de **confirmar ID da transação** explícita.

Esse recurso para desacoplar a confirmação de ID de transação é útil quando você não deseja duplicar transações no SAP, por exemplo, em cenários em que as falhas podem ocorrer devido a causas, como problemas de rede. Confirmando a ID da transação separadamente, a transação é concluída apenas uma vez no sistema SAP.

Aqui está um exemplo que mostra esse padrão:

1. Crie um aplicativo lógico em branco e adicione um gatilho HTTP.

1. No conector SAP, adicione a ação **Enviar IDOC** . Forneça os detalhes para o IDoc que você envia para o sistema SAP.

1. Para confirmar explicitamente a ID da transação em uma etapa separada, no campo **confirmar tid** , selecione **não**. Para o campo **GUID da ID de transação** opcional, você pode especificar manualmente o valor ou fazer com que o conector gere e retorne automaticamente esse GUID na resposta da ação enviar IDOC.

   ![Enviar Propriedades da ação IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explicitamente a ID da transação, adicione a ação **confirmar ID da transação** , garantindo [evitar o envio de IDocs duplicados para o SAP](#avoid-sending-duplicate-idocs). Clique dentro da caixa **ID da transação** para que a lista de conteúdo dinâmico seja exibida. Nessa lista, selecione o valor da **ID da transação** que é retornado da ação **Enviar IDOC** .

   ![Confirmar ação da ID da transação](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Depois que essa etapa é executada, a transação atual é marcada como concluída em ambas as extremidades, no lado do conector SAP e no lado do sistema SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Evite enviar IDocs duplicados

Se você tiver um problema com IDocs duplicados sendo enviado ao SAP de seu aplicativo lógico, siga estas etapas para criar uma variável de cadeia de caracteres para servir como seu identificador de transação do IDoc. A criação desse identificador de transação ajuda a evitar transmissões de rede duplicadas quando há problemas como interrupções temporárias, problemas de rede ou confirmações perdidas.

> [!NOTE]
> Os sistemas SAP esquecem um identificador de transação após uma hora especificada ou 24 horas por padrão. Como resultado, o SAP nunca falha ao confirmar um identificador de transação se a ID ou o GUID for desconhecido.
> Se a confirmação de um identificador de transação falhar, essa falha indicará que comunicação com o sistema SAP falhou antes que o SAP pudesse reconhecer a confirmação.

1. No designer de aplicativos lógicos, adicione a **variável de inicialização** de ação ao seu aplicativo lógico. 
1. No editor da **variável de inicialização**da ação, defina as configurações a seguir. Em seguida, salve as alterações.
    1. Para **nome**, insira um nome para a variável. Por exemplo, `IDOCtransferID`.
    2. Para **tipo**, selecione **cadeia de caracteres** como o tipo de variável.
    3. Para **valor**, selecione a caixa de texto **Inserir valor inicial** para abrir o menu de conteúdo dinâmico. Selecione a guia **expressões** . Na lista de funções, insira a função `guid()` . Em seguida, selecione **OK** para salvar as alterações. O campo **valor** agora está definido como a `guid()` função, que gera um GUID.
1. Após a ação **inicializar variável** , adicione a ação **Enviar IDOC**.
1. No editor da ação **Enviar IDOC**, defina as configurações a seguir. Em seguida, salve as alterações.
    1. Para o **tipo IDOC** , selecione seu tipo de mensagem e, para **mensagem IDOC de entrada**, especifique sua mensagem.
    1. Para a **versão de lançamento do SAP**, selecione os valores da configuração do SAP.
    1. Para **versão de tipos de registro**, selecione os valores da configuração do SAP.
    1. Para **confirmar tid**, selecione **não**.
    1. Selecione **Adicionar novo parâmetro lista**de  >  **ID da transação GUID**. Selecione a caixa de texto para abrir o menu de conteúdo dinâmico. Na guia **variáveis** , selecione o nome da variável que você criou. Por exemplo, `IDOCtransferID`.
1. Na barra de título da ação **Enviar IDOC**, selecione **...**  >  **Configurações**. Para **política de repetição**, selecione **nenhum**  >  **concluído**.
1. Depois que a ação **Enviar IDOC**, adicione a ação **confirmar ID da transação**.
1. No editor da ação **confirmar ID da transação**, defina as configurações a seguir. Em seguida, salve as alterações.
    1. Para **ID da transação**, insira o nome da variável novamente. Por exemplo, `IDOCtransferID`.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Aqui estão os problemas e limitações atualmente conhecidos para o conector SAP gerenciado (não ISE):

* O gatilho do SAP não dá suporte a clusters de gateway de dados. Em alguns casos de failover, o nó do gateway de dados que se comunica com o sistema SAP pode ser diferente do nó ativo, o que resulta em um comportamento inesperado. Para cenários de envio, há suporte para clusters de gateway de dados.

* No momento, o conector do SAP não é compatível com cadeia de caracteres do roteador do SAP. O gateway de dados local deve existir na mesma LAN que o sistema SAP que você deseja conectar.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/sap/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se a sistemas locais](../logic-apps/logic-apps-gateway-connection.md) de aplicativos lógicos do Azure.
* Saiba como validar, transformar e usar outras operações de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md).
