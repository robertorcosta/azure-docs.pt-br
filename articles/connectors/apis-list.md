---
title: Conectores de Aplicativos Lógicos do Azure
description: Automatizar fluxos de trabalho com conectores para aplicativos lógicos do Azure, como o interno, gerenciado, local, conta de integração, ISE e conectores empresariais
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247326"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores de Aplicativos Lógicos do Azure

Os conectores fornecem acesso rápido do Aplicativos Lógicos do Azure a eventos, dados e ações entre outros aplicativos, serviços, sistemas, protocolos e plataformas. Ao usar conectores em seus aplicativos lógicos, você expande os recursos para seus aplicativos na nuvem e locais para executar tarefas com os dados que você cria e já tem.

Embora os aplicativos lógicos ofereçam [centenas de conectores](https://docs.microsoft.com/connectors), este artigo descreve os *mais populares e mais usados* conectores que são usados com êxito por milhares de aplicativos e milhões de execuções para processamento de dados e informações. Para localizar a lista completa de conectores e as informações de referência de cada conector, como gatilhos, ações e limites, examine as páginas de referência do conector em [conectores visão geral](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [gatilhos e ações](#triggers-actions), [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md)e detalhes de preços de [aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Para integrar com um serviço ou uma API que não tem conector, você pode chamar diretamente o serviço por meio de um protocolo como HTTP ou criar um [conector personalizado](#custom).

## <a name="connector-types"></a>Tipos de conector

Os conectores estão disponíveis como gatilhos e ações internas ou como conectores gerenciados.

<a name="built-in"></a>

* [**Interno**](#built-ins): gatilhos internos e ações são "nativos" para aplicativos lógicos do Azure e ajudam a executar essas tarefas para seus aplicativos lógicos:

  * Executar em agendas personalizadas e avançadas.

  * Organize e controle o fluxo de trabalho de seu aplicativo lógico, por exemplo, loops e condições, e também para trabalhar com variáveis e operações de dados.

  * Comunique-se com outros pontos de extremidade.

  * Receber e responder a solicitações.

  * Chame o Azure functions, aplicativos de API do Azure (aplicativos Web), suas próprias APIs gerenciadas e publicadas com o gerenciamento de API do Azure e aplicativos lógicos aninhados que podem receber solicitações.

<a name="managed-connectors"></a>

* [**Conectores gerenciados**](#managed-api-connectors): implantados e gerenciados pela Microsoft, esses conectores fornecem gatilhos e ações para acessar serviços de nuvem, sistemas locais ou ambos, incluindo o Office 365, o armazenamento de BLOBs do Azure, o SQL Server, o Dynamics, o Salesforce, o SharePoint e muito mais. Alguns conectores especificamente dão suporte a cenários de comunicação entre empresas (B2B) e exigem uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vinculada ao seu aplicativo lógico. Antes de usar determinados conectores, talvez você precise primeiro criar conexões, que são gerenciadas pelos aplicativos lógicos do Azure.

  Por exemplo, se você estiver usando o Microsoft BizTalk Server, seus aplicativos lógicos poderão se conectar e se comunicar com seus BizTalk Server usando o [BizTalk Server on-premises Connector](#on-premises-connectors). Você pode, então, estender ou executar operações semelhantes ao BizTalk em seus aplicativos lógicos usando os [conectores de conta de integração](#integration-account-connectors).

  Os conectores são classificados como Standard ou Enterprise. [Conectores corporativos](#enterprise-connectors) fornecem acesso a sistemas corporativos como SAP, IBM MQ e IBM 3270 por um custo adicional. Para determinar se um conector é Standard ou Enterprise, consulte a página de referência detalhes técnicos em cada conector em [conectores visão geral](https://docs.microsoft.com/connectors).

  Você também pode identificar conectores usando essas categorias, embora alguns conectores possam cruzar várias categorias. Por exemplo, o SAP é um conector corporativo e um conector local:

  |   |   |
  |---|---|
  | [**Conectores gerenciados**](#managed-api-connectors) | Crie aplicativos lógicos que usam serviços como Armazenamento de Blobs do Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e muito mais. |
  | [**Conectores locais**](#on-premises-connectors) | Depois de instalar e configurar o [Gateway de dados local][gateway-doc], esses conectores ajudam seus aplicativos lógicos a acessar sistemas locais, como SQL Server, SharePoint Server, Oracle DB, compartilhamentos de arquivos e outros. |
  | [**Conectores da conta de integração**](#integration-account-connectors) | Disponível quando você cria e paga por uma conta de integração, esses conectores transformam e validam XML, codificam e decodificam arquivos simples e processam mensagens B2B (business-to-business) com protocolos AS2, EDIFACT e X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Conectar-se de um ambiente do serviço de integração

Para aplicativos lógicos que precisam de acesso direto a recursos em uma rede virtual do Azure, você pode criar um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) isolado no qual você pode criar, implantar e executar seus aplicativos lógicos em recursos dedicados. No designer do aplicativo lógico, quando você procura os conectores que deseja usar para aplicativos lógicos em um ISE, um rótulo **principal** aparece em gatilhos e ações internas, enquanto o rótulo do **ISE** aparece em alguns conectores:

* **Núcleo**: gatilhos e ações internos com esse rótulo são executados no mesmo ISE que seus aplicativos lógicos, por exemplo:

  ![Conector do ISE de exemplo](./media/apis-list/example-core-connector.png)

* **ISE**: conectores gerenciados com esse rótulo são executados no mesmo ISE que seus aplicativos lógicos, por exemplo:

  ![Conector do ISE de exemplo](./media/apis-list/example-ise-connector.png)

  Se você tiver um sistema local conectado a uma rede virtual do Azure, um ISE permitirá que seus aplicativos lógicos acessem diretamente esse sistema sem o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Em vez disso, você pode usar o conector do **ISE** do sistema, se disponível, uma ação http ou um [conector personalizado](#custom). Para sistemas locais que não têm conectores do **ISE** , use o gateway de dados local. Para examinar os conectores do ISE disponíveis, consulte [conectores do ISE](#ise-connectors).

* Todos os outros conectores sem o rótulo **principal** ou **ISE** , que você pode continuar a usar, são executados no serviço de aplicativos lógicos multilocatários globais, por exemplo:

  ![Conector de vários locatários de exemplo](./media/apis-list/example-multi-tenant-connector.png)

Os aplicativos lógicos que são executados em um ISE e seus conectores, independentemente de onde esses conectores são executados, seguem um plano de preços fixo versus o plano de preços baseado em consumo. Para obter mais informações, consulte estas páginas:

* [Modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md)
* [Detalhes de preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Interno

Os aplicativos lógicos fornecem gatilhos e ações internas para que você possa criar fluxos de trabalho baseados em agendamento, ajudar seus aplicativos lógicos a se comunicar com outros aplicativos e serviços, controlar o fluxo de trabalho por meio de seus aplicativos lógicos e gerenciar ou manipular dados.

|   |   |   |   |
|---|---|---|---|
| [ícone de API de ![][schedule-icon]<br>**agendamento**][schedule-doc] | -Executar um aplicativo lógico em uma recorrência especificada, variando de programação básica para avançada com o gatilho de [ **recorrência** ][schedule-recurrence-doc]. <p>-Executar um aplicativo lógico que precisa manipular dados em partes contínuas com o gatilho de [ **janela deslizante** ][schedule-sliding-window-doc]. <p>-Pause seu aplicativo lógico por uma duração especificada com a [ação de **atraso** ][schedule-delay-doc]. <p>-Pause seu aplicativo lógico até a data e a hora especificadas com a ação de [ **atraso até** ][schedule-delay-until-doc]. | [ícone de API de ![][batch-icon]<br>**lote**][batch-doc] | - Processe mensagens em lotes com o gatilho **Mensagens em lote**. <p>- Chame aplicativos lógicos que possuem gatilhos de lote existentes com a ação **Enviar mensagens para lote**. |
| [ícone de API de ![][http-icon]<br>**http**][http-doc] | Chamar pontos de extremidade HTTP ou HTTPS com gatilhos e ações para HTTP. Outros gatilhos internos HTTP e ações incluem [http + Swagger][http-swagger-doc] e [http + webhook][http-webhook-doc]. | [ícone de API de ![][http-request-icon]<br>**solicitação**][http-request-doc] | - Faça com que seu aplicativo lógico possa ser chamado de outros aplicativos ou serviços, gatilho em eventos de recursos da Grade de Eventos ou gatilho em respostas aos alertas da Central de Segurança do Azure com o gatilho **Solicitar**. <p>- Envie respostas para um aplicativo ou serviço com a ação **Resposta**. |
| [ícone de API de ![][azure-api-management-icon]<br>**Gerenciamento de <br>de API do Azure**][azure-api-management-doc] | Chame os gatilhos e ações definidos por suas próprias APIs que você gerencia e publica com o Gerenciamento de API do Azure. | [ícone de API de ![][azure-app-services-icon]<br>**Azure app serviços <br>** ][azure-app-services-doc] | Chame Aplicativos de API do Azure ou Aplicativos Web, hospedado no Serviço de Aplicativo do Azure. Os gatilhos e ações definidos por esses aplicativos são exibidos como quaisquer outros gatilhos e ações de primeira classe quando o Swagger é incluído.|
| [ícone de API ![][azure-logic-apps-icon]<br>**aplicativos de <br>lógica do Azure**][nested-logic-app-doc] | Chame outros aplicativos lógicos que comecem com o gatilho de **solicitação** . |
|||||

### <a name="run-code-from-logic-apps"></a>Executar código de aplicativos lógicos

Os aplicativos lógicos fornecem ações internas para executar seu próprio código no fluxo de trabalho do aplicativo lógico:

|   |   |   |   |
|---|---|---|---|
| [ícone de API de ![][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | Chame funções do Azure que executam snippets de códigos personalizados (C# ou Node.js) de seus aplicativos lógicos. | [ícone de API de ![][inline-code-icon]<br>**código embutido**][azure-functions-doc] | Adicione e execute trechos de código JavaScript de seus aplicativos lógicos. |
|||||

### <a name="control-workflow"></a>Controlar o fluxo de trabalho

Os aplicativos lógicos fornecem ações internas para estruturar e controlar as ações no fluxo de trabalho do aplicativo lógico:

|   |   |   |   |
|---|---|---|---|
| [![o ícone interno][condition-icon]<br>**condição**][condition-doc] | Avalie uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa. | [![][for-each-icon]<br>de ícone interno **para cada**][for-each-doc] | Execute as mesmas ações em cada item em uma matriz. |
| [![**escopo** de][scope-icon]<br>de ícone interno][scope-doc] | Agrupe ações em *Escopos*, que obtém seus próprios status após as ações no escopo concluírem a execução. | [![ícone interno de][switch-icon]<br>**switch**][switch-doc] | Agrupe ações em *Casos*, que recebem valores exclusivos, exceto para o caso padrão. Execute somente esse caso cujo valor atribuído coincide com o resultado de uma expressão, objeto ou token. Se nenhuma correspondência existir, execute o caso padrão. |
| [![ícone interno][terminate-icon]<br>**encerrar**][terminate-doc] | Pare um fluxo de trabalho de aplicativo lógico ativamente em execução. | [![ícone interno][until-icon]<br>**até**][until-doc] | Repita ações até que a condição especificada seja verdadeira ou algum estado seja alterado. |
|||||

### <a name="manage-or-manipulate-data"></a>Gerenciar ou manipular dados

Os aplicativos lógicos fornecem ações internas para trabalhar com saídas de dados e seus formatos:

|   |   |
|---|---|
| [![ícone interno][data-operations-icon]<br>operações de **dados**][data-operations-doc] | Execute operações com dados: <p>- **Compor**: crie uma única saída de várias entradas com vários tipos. <br>- **Criar tabela CSV**: crie uma tabela CSV (separada por valores vírgula) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: crie uma tabela HTML de uma matriz com objetos JSON. <br>- **Matriz de filtro**: crie uma matriz de itens na outra matriz que atendem aos seus critérios. <br>- **Juntar-se**: crie uma cadeia de caracteres de todos os itens em uma matriz e separe esses itens com o delimitador especificado. <br>- **analisar JSON**: Crie tokens amigáveis de propriedades e seus valores no conteúdo JSON para que você possa usar essas propriedades em seu fluxo de trabalho. <br>- **Selecionar**: crie uma matriz com objetos JSON transformando itens ou valores em outra matriz e mapeando esses itens para propriedades especificadas. |
| ![Ícone Interno][date-time-icon]<br>**Data/Hora** | Execute operações com carimbos de data/hora: <p>- **Adicionar horário**: adicionar número especificado de unidades a um carimbo de data/hora. <br>- **Converter fuso horário**: converter um carimbo de data/hora do fuso horário de origem para o fuso horário de destino. <br>- **Horário atual**: retornar o carimbo de data/hora atual como uma cadeia de caracteres. <br>- **Obter horário futuro**: retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. <br>- **Obter horário passado**: retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. <br>- **Subtrair de horário**: subtrair um número de unidades de tempo de um carimbo de data/hora. |
| [![ícone interno][variables-icon]<br>**variáveis**][variables-doc] | Execute operações com variáveis: <p>- **Acrescentar à variável de matriz**: inserir um valor como o último item em uma matriz armazenada por uma variável. <br>- **Acrescentar à variável de matriz**: inserir um valor como o último caractere em uma cadeia de caracteres armazenada por uma variável. <br>- **Diminuir variável**: diminuir uma variável em um valor constante. <br>- **Incrementar variável**: aumentar uma variável em um valor constante. <br>- **Inicializar variável**: criar uma variável e declarar seu tipo de dados e o valor inicial. <br>- **Definir a variável**: atribuir um valor diferente em uma variável existente. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Conectores gerenciados

Os aplicativos lógicos fornecem esses conectores padrão populares para automatizar tarefas, processos e fluxos de trabalho com esses serviços ou sistemas:

|   |   |   |   |
|---|---|---|---|
| [ícone de API de ![][azure-service-bus-icon]<br>**barramento de serviço do Azure**][azure-service-bus-doc] | Gerencie mensagens assíncronas, sessões e assinaturas de tópico com o conector mais comumente usado em Aplicativos Lógicos. | [ícone de API de ![][sql-server-icon]<br>**SQL Server**][sql-server-doc] | Conecte-se ao seu SQL Server local ou a um banco de dados SQL do Azure na nuvem para que você possa gerenciar registros, executar procedimentos armazenados ou executar consultas. |
| [ícone de API de ![][azure-blob-storage-icon]<br>**armazenamento de<br>de blob do Azure**][azure-blob-storage-doc] | Conecte-se à sua conta de armazenamento para que você possa criar e gerenciar conteúdo de BLOB. | [ícone de API de ![][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | Conecte-se à sua conta de email do Office 365 para que você possa criar e gerenciar emails, tarefas, eventos de calendário e reuniões, contatos, solicitações e muito mais. |
| [ícone de API de ![][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | Conecte-se a servidores SFTP que você pode acessar da Internet usando o SSH para que você possa trabalhar com seus arquivos e pastas. | [ícone de API de ![][sharepoint-online-icon]<br>**SharePoint<br>online**][sharepoint-online-doc] | Conecte-se ao SharePoint Online para que você possa gerenciar arquivos, anexos, pastas e muito mais. | 
| [ícone de API de ![][dynamics-365-icon]<br>**Dynamics 365<br>** ][dynamics-365-doc] | Conecte-se à sua conta do Dynamics 365 para que você possa criar e gerenciar registros, itens e muito mais. | [ícone de API de ![][azure-queues-icon]<br>**filas de <br>do Azure**][azure-queues-doc] | Conecte-se à sua conta de armazenamento do Azure para que você possa criar e gerenciar filas e mensagens |
| [ícone de API de ![][ftp-icon]<br>**FTP**][ftp-doc] | Conecte-se a servidores FTP que você pode acessar pela Internet para que você possa trabalhar com seus arquivos e pastas. | [ícone de API de ![][file-system-icon]<br>**sistema de <br>de arquivos**][file-system-doc] | Conecte-se ao seu compartilhamento de arquivos local para que você possa criar e gerenciar arquivos. |
| [ícone de API de ![][azure-event-hubs-icon]<br>**hubs de eventos do Azure**][azure-event-hubs-doc] | Como consumir e publicar eventos por meio de um Hub de Eventos. Por exemplo, obtenha uma saída do seu aplicativo lógico com os Hubs de Eventos e, em seguida, envie essa saída para um provedor de análise em tempo real. | [ícone de API de ![][azure-event-grid-icon]<br>**grade** de<br>de **eventos do Azure**][azure-event-grid-doc] | Monitore eventos publicados por uma grade de eventos, por exemplo, quando recursos do Azure ou recursos de terceiros são alterados. |
| [ícone de API de ![][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Conecte-se à sua conta do Salesforce para que você possa criar e gerenciar itens como registros, trabalhos, objetos e muito mais. | [ícone de API de ![][twitter-icon]<br>**Twitter**][twitter-doc] | Conecte-se à sua conta do Twitter para que você possa gerenciar tweets, seguidores, sua linha do tempo e muito mais. Salve seus tweets em SQL, o Excel ou SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores locais

Aqui estão alguns conectores padrão comumente usados que os aplicativos lógicos fornecem para acessar dados e recursos em sistemas locais. Antes de poder criar uma conexão com um sistema local, você deve primeiro [baixar, instalar e configurar um gateway de dados local][gateway-doc]. Esse gateway fornece um canal de comunicação seguro sem ter que configurar a infraestrutura de rede necessária.

|   |   |   |   |   |
|---|---|---|---|---|
| [ícone de API de ![][biztalk-server-icon]<br>**servidor** **BizTalk** <br>][biztalk-server-doc] | [ícone de API de ![][file-system-icon]<br>**sistema de <br>de arquivos**][file-system-doc] | [ícone de API de ![][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [ícone de API de ![][ibm-informix-icon]<br>**IBM** <br>**Informix**][ibm-informix-doc] | [ícone de API de ![][mysql-icon]<br>**MySQL**][mysql-doc] |
| [ícone de API de ![][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc] | [ícone de API de ![][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc] | [ícone de API de ![][sharepoint-server-icon]<br>**servidor de <br>do SharePoint**][sharepoint-server-doc] | [ícone de API de ![][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [ícone de API de ![][teradata-icon]<br>**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores da conta de integração

Os aplicativos lógicos fornecem conectores padrão para a criação de Soluções B2B (entre empresas) com seus aplicativos lógicos quando você cria e paga por uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que está disponível por meio do Enterprise Integration Pack (EIP) no Azure. Com essa conta, você pode criar e armazenar os artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e assim por diante. Para usar esses artefatos, associe seus aplicativos lógicos com sua conta de integração. Se você usa o BizTalk Server atualmente, esses conectores poderão parecer familiares.

|   |   |   |   |
|---|---|---|---|
| [ícone de API de ![][as2-icon]<br>a **decodificação de <br>AS2**][as2-doc] | [ícone de API de ![][as2-icon]<br>**codificação de <br>AS2**][as2-doc] | [ícone de API de ![][edifact-icon]<br>**EDIFACT <br>decodificação**][edifact-decode-doc] | [ícone de API de ![][edifact-icon]<br>**codificação de <br>EDIFACT**][edifact-encode-doc] |
| [ícone de API de ![][flat-file-decode-icon]<br>**decodificação de <br>de arquivo simples**][flat-file-decode-doc] | [ícone de API de ![][flat-file-encode-icon]<br>**codificação de <br>de arquivo simples**][flat-file-encode-doc] | [ícone de API de ![][integration-account-icon]<br>**conta de <br>de integração**][integration-account-doc] | [ícone de API de ![][liquid-icon]<br>**transformações** de <br>**líquido**][json-liquid-transform-doc] |
| [ícone de API de ![][x12-icon]<br>**X12 <br>decodificação**][x12-decode-doc] | [ícone de API de ![][x12-icon]<br>**codificação de <br>X12**][x12-encode-doc] | [ícone de API de ![][xml-transform-icon]<br>**transformações** de <br>**XML**][xml-transform-doc] | [ícone de API de ![][xml-validate-icon]<br>**validação de <br>XML**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

Os aplicativos lógicos fornecem esses conectores empresariais para acessar sistemas empresariais, como SAP e IBM MQ:

|   |   |   |
|---|---|---|
| [ícone de API de ![][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [ícone de API de ![][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] | [ícone de API de ![][sap-icon]<br>**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Conectores do ISE

Para aplicativos lógicos que você cria e executa um [ambiente de serviço de integração isolado (ISE)](#integration-service-environment), o designer de aplicativo lógico identifica gatilhos e ações internos que são executados no ISE usando o rótulo **principal** . Os conectores gerenciados que são executados em um ISE exibem o rótulo do **ISE** , enquanto os conectores executados no serviço de aplicativos lógicos multilocatários globais não exibem nenhum rótulo. Esta lista mostra os conectores que atualmente têm versões do ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[ícone de API de ![][as2-icon]<br>**AS2**][as2-doc] | [ícone de API de ![][azure-blob-storage-icon]<br>**armazenamento de<br>de blob do Azure**][azure-blob-storage-doc] | [ícone de API de ![][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB**][azure-cosmos-db-doc] | [ícone de API de ![][azure-event-hubs-icon]<br>**hubs de <br>de eventos do Azure**][azure-event-hubs-doc] | [ícone de API de ![][azure-file-storage-icon]<br>**armazenamento de<br>de arquivos do Azure**][azure-file-storage-doc] |
| [ícone de API de ![][azure-service-bus-icon]<br>**barramento de <br>de serviço do Azure**][azure-service-bus-doc] | [ícone da API ![][azure-sql-data-warehouse-icon]<br>**SQL data <br>warehouse do Azure**][azure-sql-data-warehouse-doc] | [ícone de API de ![][azure-table-storage-icon]<br>**armazenamento de <br>de tabela do Azure**][azure-table-storage-doc] | [ícone de API de ![][azure-queues-icon]<br>**filas de <br>do Azure**][azure-queues-doc] | [ícone de API de ![][edifact-icon]<br>**EDIFACT**][edifact-doc] |
| [ícone de API de ![][file-system-icon]<br>**sistema de <br>de arquivos**][file-system-doc] | [ícone de API de ![][ftp-icon]<br>**FTP**][ftp-doc] | [ícone de API de ![][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [ícone de API de ![][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [ícone de API de ![][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] |
| [ícone de API de ![][sap-icon]<br>**SAP**][sap-connector-doc] | [ícone de API de ![][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | [ícone de API de ![][smtp-icon]<br>**SMTP**][smtp-doc] | [ícone de API de ![][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [ícone de API de ![][x12-icon]<br>**X12**][x12-doc] |
||||||

Para saber mais, consulte esses tópicos:

* [Acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md)
* [Conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Gatilhos e tipos de ação

Os conectores podem fornecer *gatilhos*, *ações*ou ambos. Um *gatilho* é a primeira etapa em qualquer aplicativo lógico, geralmente especificando o evento que dispara o gatilho e começa a executar seu aplicativo lógico. Por exemplo, o conector de FTP tem um gatilho que inicia seu aplicativo lógico "quando um arquivo é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento ou os dados especificados e, em seguida, são acionados quando detectam o evento ou os dados especificados. Outros gatilhos esperam, mas são imediatamente acionados quando um evento específico ocorre ou quando novos dados estão disponíveis. Os gatilhos também passam todos os dados necessários para seu aplicativo lógico. Seu aplicativo lógico pode ler e usar esses dados em todo o fluxo de trabalho. Por exemplo, o conector do Twitter tem um gatilho "quando um novo tweet é Postado", que passa o conteúdo do tweet para o fluxo de trabalho do seu aplicativo lógico.

Depois que um gatilho é disparado, o aplicativo lógico do Azure cria uma instância de sua aplicação lógica e começa a executar as *ações* no fluxo de trabalho do seu aplicativo lógico. As ações são as etapas que seguem o gatilho e executam tarefas no fluxo de trabalho do aplicativo lógico. Por exemplo, você pode criar um aplicativo lógico que obtém dados do cliente de um banco de dado SQL e processa esses dados em ações posteriores.

Aqui estão os tipos gerais de gatilhos que o aplicativo lógico do Azure fornece:

* *Gatilho de recorrência*: esse gatilho é executado em um agendamento especificado e não está rigidamente associado a um serviço ou sistema específico.

* *Gatilho de sondagem*: esse gatilho sonda regularmente um serviço ou sistema específico com base no agendamento especificado, verificando se há novos dados ou se um evento específico ocorreu. Se novos dados estiverem disponíveis ou o evento específico tiver ocorrido, o gatilho criará e executará uma nova instância do seu aplicativo lógico, que agora pode usar os dados passados como entrada.

* *Gatilho de push*: esse gatilho espera e escuta novos dados ou para que um evento aconteça. Quando novos dados estão disponíveis ou quando o evento ocorre, o gatilho cria e executa uma nova instância do seu aplicativo lógico, que agora pode usar os dados passados como entrada.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configuração do conector

Os gatilhos e as ações de cada conector fornecem suas próprias propriedades para você configurar. Muitos conectores também exigem que você primeiro crie uma *conexão* com o serviço ou sistema de destino e forneça credenciais de autenticação ou outros detalhes de configuração para poder usar um gatilho ou uma ação em seu aplicativo lógico. Por exemplo, você deve autorizar uma conexão com uma conta do Twitter para acessar dados ou postar em seu nome.

Para conectores que usam Azure Active Directory (Azure AD) OAuth, criar uma conexão significa entrar no serviço, como o Office 365, Salesforce ou GitHub, em que seu token de acesso é [criptografado](../security/fundamentals/encryption-overview.md) e armazenado com segurança em um repositório de segredo do Azure. Outros conectores, como FTP e SQL, exigem uma conexão com detalhes de configuração, como o endereço do servidor, o nome de usuário e a senha. Esses detalhes de configuração de conexão também são criptografados e armazenados com segurança. Saiba mais sobre [criptografia no Azure](../security/fundamentals/encryption-overview.md).

As conexões podem acessar o serviço ou sistema de destino pelo tempo que o serviço ou o sistema permitir. Para serviços que usam conexões OAuth do Azure AD, como o Office 365 e Dynamics, os aplicativos lógicos do Azure atualizam os tokens de acesso indefinidamente. Outros serviços podem ter limites de quanto tempo os aplicativos lógicos do Azure podem usar um token sem Atualizar. Em geral, algumas ações invalidam todos os tokens de acesso, como alterar sua senha.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Conectores e APIs personalizados

Para chamar as APIs que executam o código personalizado ou não estão disponíveis como conectores, você pode estender a plataforma dos Aplicativos Lógicos [criando Aplicativos de API personalizados](../logic-apps/logic-apps-create-api-app.md). Você também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *qualquer* API REST ou baseadas em SOAP, o que torna essas APIs disponíveis para qualquer aplicativo lógico em sua assinatura do Azure. Para tornar Aplicativos de API personalizado ou conectores público disponíveis para qualquer pessoa para uso no Azure, você pode [enviar conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Os aplicativos lógicos implantados e executados em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem acessar diretamente os recursos em uma rede virtual do Azure. Se você tiver conectores personalizados que exigem o gateway de dados local e tiver criado esses conectores fora de um ISE, os aplicativos lógicos em um ISE também poderão usar esses conectores.
>
> Os conectores personalizados criados em um ISE não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas a uma rede virtual do Azure que hospeda o ISE. Portanto, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.
>
> Para obter mais informações sobre como criar ISEs, consulte [conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Próximas etapas

* Exibir a [lista completa de conectores](https://docs.microsoft.com/connectors)
* [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicativos lógicos](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Conectar-se a fontes de dados locais de aplicativos lógicos com o gateway de dados local"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Criar uma instância de serviço de Gerenciamento de API do Azure para gerenciar e publicar suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicativos lógicos a Aplicativos de API do Serviço de Aplicativo"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integre aplicativos lógicos com Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avaliar uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Executar as mesmas ações em cada item em uma matriz"
[http-doc]: ./connectors-native-http.md "Chamar pontos de extremidade HTTP ou HTTPS de seus aplicativos lógicos"
[http-request-doc]: ./connectors-native-reqres.md "Receber solicitações HTTP em seus aplicativos lógicos"
[http-response-doc]: ./connectors-native-reqres.md "Responder a solicitações HTTP de seus aplicativos lógicos"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chamar pontos de extremidade REST de seus aplicativos lógicos"
[http-webhook-doc]: ./connectors-native-webhook.md "Aguardar eventos específicos de pontos de extremidade HTTP ou HTTPS"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrar aplicativos lógicos a fluxos de trabalho aninhados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selecionar e filtrar matrizes com a ação Consultar"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Executar aplicativos lógicos com base em uma agenda"
[schedule-delay-doc]: ./connectors-native-delay.md "Atraso ao executar a próxima ação"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Atraso ao executar a próxima ação"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Executar aplicativos lógicos em um agendamento recorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Executar aplicativos lógicos que precisam manipular dados em partes contíguas"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar ações em grupos, que obtêm seu próprio status depois que as ações na conclusão do grupo são executadas"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que recebem valores exclusivos. Execute somente o caso cujo valor corresponde ao resultado de uma expressão, objeto ou token. Se não houver correspondências, execute o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Parar ou cancelar um fluxo de trabalho em execução ativamente para seu aplicativo lógico"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita ações até que a condição especificada seja verdadeira ou algum estado seja alterado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Executar as operações de dados, como filtragem de matrizes ou criação de tabelas HTML e de CSV"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, como inicializar, configurar, aumentar ou diminuir e acrescentar à variável de cadeia de caracteres ou matriz"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerenciar arquivos em seu contêiner de blob com o conector de Armazenamento de Blobs do Azure"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Conectar-se a Azure Cosmos DB para que você possa acessar documentos e procedimentos armazenados"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorar eventos publicados por uma grade de eventos, por exemplo, quando os recursos do Azure ou os recursos de terceiros mudam"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Conecte-se aos hubs de eventos do Azure para que você possa receber e enviar eventos entre os aplicativos lógicos e os hubs de eventos"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Conecte-se à sua conta de armazenamento do Azure para que você possa criar, atualizar, obter e excluir arquivos"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Conecte-se à sua conta de armazenamento do Azure para que você possa criar e gerenciar filas e mensagens"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envia mensagens de tópicos e filas do barramento de serviço e recebe mensagens de assinaturas e filas do barramento de serviço"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Conecte-se ao Azure SQL Data Warehouse para que você possa exibir seus dados"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Conecte-se à sua conta de armazenamento do Azure para que você possa criar, atualizar e consultar tabelas e muito mais"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Conecte-se ao seu BizTalk Server para que você possa executar aplicativos baseados no BizTalk lado a lado com aplicativos lógicos do Azure"
[box-doc]: ./connectors-create-api-box.md "Conecte-se ao box. Carregar, obter, excluir, listar seus arquivos e muito mais"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Conecte-se ao dropbox. Carregar, obter, excluir, listar seus arquivos e muito mais"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Conectar-se ao Dynamics CRM Online para que você possa trabalhar com dados do CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Conecte-se ao Facebook. Postar em uma linha do tempo, obter um feed de página e muito mais"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Conectar-se a um sistema de arquivos local"
[ftp-doc]: ./connectors-create-api-ftp.md "Conectar-se a um servidor FTP / FTPS e para tarefas FTP, incluindo carregar, obter, excluir arquivos e muito mais"
[github-doc]: ./connectors-create-api-github.md "Conectar-se ao GitHub e acompanhar os problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Conecta-se ao Google Agenda e pode gerenciar o calendário"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Conecte-se ao GoogleDrive para que você possa trabalhar com seus dados"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Conectar-se a planilhas do Google para que você possa modificar suas planilhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Conecta-se ao Google Tasks para que você possa gerenciar suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Conecte-se a aplicativos 3270 em mainframes IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Conecte-se ao IBM DB2 na nuvem ou no local. Atualizar uma linha, obter uma tabela e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Conecte-se ao Informix na nuvem ou no local. Ler uma linha, listar as tabelas e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Conectar-se a locais do IBM MQ ou ao Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Conecte-se ao Instagram. Disparar ou agir em eventos"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Conecte-se à sua conta do MailChimp. Gerenciar e automatizar emails"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Conectar-se ao Mandrill para comunicação"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Conecte-se ao seu banco de dados MySQL local para que você possa ler e gravar dados"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Conecte-se à sua conta do Office 365 para que você possa enviar e receber emails, gerenciar seu calendário e contatos e muito mais"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Conecte-se ao seu Microsoft OneDrive pessoal para que você possa carregar, excluir, listar arquivos e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Conecte-se ao Microsoft OneDrive de sua empresa para que você possa carregar, excluir, listar seus arquivos e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Conecte-se a um banco de dados Oracle para que você possa adicionar, inserir, excluir linhas e muito mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Conecte-se à sua caixa de correio do Outlook para que você possa gerenciar seu email, calendários, contatos e muito mais"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Conecte-se ao seu banco de dados PostgreSQL para que você possa ler o dado de tabelas"
[project-online-doc]: ./connectors-create-api-projectonline.md "Conecte-se ao Microsoft Project online para que você possa gerenciar seus projetos, tarefas, recursos e muito mais"
[rss-doc]: ./connectors-create-api-rss.md "Publicar e recuperar itens de feed, disparar operações quando um novo item for publicado em um RSS feed"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Conecte-se à sua conta do Salesforce. Gerenciar contas, clientes potenciais, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Conectar-se a um sistema SAP local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Conecte-se ao SendGrid. Enviar email e gerenciar listas de destinatários"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Conecte-se à sua conta do SFTP usando SSH. Carregar, obter, excluir arquivos e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Conecte-se ao servidor local do SharePoint. Gerenciar documentos, itens de lista e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Conecte-se ao SharePoint Online. Gerenciar documentos, itens de lista e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Conectar-se ao Slack e postar mensagens em canais do Slack."
[smtp-doc]: ./connectors-create-api-smtp.md "Conectar-se a um servidor SMTP e enviar emails com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Conectar-se ao SparkPost para comunicação."
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Conecte-se ao banco de dados SQL do Azure ou SQL Server. Criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Conectar-se ao seu banco de dados Teradata para ler o dado de tabelas"
[trello-doc]: ./connectors-create-api-trello.md "Conecte-se ao Trello. Gerencie seus projetos e organize qualquer coisa com qualquer pessoa"
[twilio-doc]: ./connectors-create-api-twilio.md "Conecte-se ao twilio. Enviar e receber mensagens, obter números disponíveis, gerenciar números de telefone de entrada e muito mais"
[twitter-doc]: ./connectors-create-api-twitter.md "Conecte-se ao Twitter. Obtenha cronogramas, poste tweets e muito mais"
[yammer-doc]: ./connectors-create-api-yammer.md "Conecte-se ao Yammer. Poste mensagens, obtenha novas mensagens e muito mais"
[youtube-doc]: ./connectors-create-api-youtube.md "Conecte-se ao YouTube. Gerenciar seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificar e decodificar mensagens que usam o protocolo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificar e decodificar mensagens que usam o protocolo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodificar mensagens que usam o protocolo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificar mensagens que usam o protocolo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gerenciar metadados para artefatos da conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformar JSON com modelos líquidos"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificar e decodificar mensagens que usam o protocolo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodificar mensagens que usam o protocolo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificar mensagens que usam o protocolo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformar mensagens XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validar mensagens XML"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
