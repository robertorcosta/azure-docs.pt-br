---
title: Conectores de Aplicativos Lógicos do Azure
description: Automatize fluxos de trabalho com conectores para aplicativos azure logic, como conectores integrados, gerenciados, on-premises, conta de integração, ISE e conectores corporativos
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247326"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores de Aplicativos Lógicos do Azure

Os conectores fornecem acesso rápido do Aplicativos Lógicos do Azure a eventos, dados e ações entre outros aplicativos, serviços, sistemas, protocolos e plataformas. Ao usar conectores em seus aplicativos lógicos, você expande os recursos para seus aplicativos na nuvem e locais para executar tarefas com os dados que você cria e já tem.

Embora o Logic Apps ofereça [centenas de conectores,](https://docs.microsoft.com/connectors)este artigo descreve os conectores *populares e mais comumente usados* que são usados com sucesso por milhares de aplicativos e milhões de execuções para processar dados e informações. Para encontrar a lista completa de conectores e as informações de referência de cada conector, como gatilhos, ações e limites, revise as páginas de referência do conector [visão geral do Connectors](https://docs.microsoft.com/connectors). Além disso, saiba mais sobre [gatilhos e ações,](#triggers-actions) [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md)e [detalhes de preços da Logic Apps.](https://azure.microsoft.com/pricing/details/logic-apps/)

> [!TIP]
> Para se integrar a um serviço ou API que não tenha conector, você pode ligar diretamente para o serviço por um protocolo como HTTP ou criar um [conector personalizado](#custom).

## <a name="connector-types"></a>Tipos de conectores

Os conectores estão disponíveis como gatilhos e ações incorporados ou como conectores gerenciados.

<a name="built-in"></a>

* [**Integrado**](#built-ins): Gatilhos e ações incorporados são "nativos" dos aplicativos azure logic e ajudam você a executar essas tarefas para seus aplicativos lógicos:

  * Execute em horários personalizados e avançados.

  * Organize e controle o fluxo de trabalho do seu aplicativo lógico, por exemplo, loops e condições, e também para trabalhar com variáveis e operações de dados.

  * Comunique-se com outros pontos finais.

  * Receba e responda às solicitações.

  * Ligue para as funções do Azure, Azure API Apps (Web Apps), suas próprias APIs gerenciadas e publicadas com o Azure API Management e aplicativos de lógica aninhadas que podem receber solicitações.

<a name="managed-connectors"></a>

* [**Conectores gerenciados**](#managed-api-connectors): Implantados e gerenciados pela Microsoft, esses conectores fornecem gatilhos e ações para acessar serviços de nuvem, sistemas locais ou ambos, incluindo Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint e muito mais. Alguns conectores suportam especificamente cenários de comunicação business-to-business (B2B) e exigem uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vinculada ao seu aplicativo lógico. Antes de usar certos conectores, você pode ter que primeiro criar conexões, que são gerenciadas pelo Azure Logic Apps.

  Por exemplo, se você estiver usando o Microsoft BizTalk Server, seus aplicativos lógicos podem se conectar e se comunicar com o BizTalk Server usando o [conector local biztalk server](#on-premises-connectors). Você pode, então, estender ou executar operações semelhantes ao BizTalk em seus aplicativos lógicos usando os [conectores de conta de integração](#integration-account-connectors).

  Os conectores são classificados como Padrão ou Enterprise. [Os conectores corporativos](#enterprise-connectors) fornecem acesso a sistemas corporativos como SAP, IBM MQ e IBM 3270 por um custo adicional. Para determinar se um conector é Padrão ou Enterprise, consulte os detalhes técnicos na página de referência de cada conector [visão geral do Connectors](https://docs.microsoft.com/connectors).

  Você também pode identificar conectores usando essas categorias, embora alguns conectores possam cruzar várias categorias. Por exemplo, o SAP é um conector Enterprise e um conector no local:

  |   |   |
  |---|---|
  | [**Conectores gerenciados**](#managed-api-connectors) | Crie aplicativos lógicos que usam serviços como Armazenamento de Blobs do Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e muito mais. |
  | [**Conectores locais**](#on-premises-connectors) | Depois de instalar e configurar o [gateway de dados locais][gateway-doc], esses conectores ajudam seus aplicativos lógicos a acessar sistemas locais, como SQL Server, SharePoint Server, Oracle DB, compartilhamentos de arquivos e outros. |
  | [**Conectores da conta de integração**](#integration-account-connectors) | Disponível quando você cria e paga por uma conta de integração, esses conectores transformam e validam XML, codificam e decodificam arquivos simples e processam mensagens B2B (business-to-business) com protocolos AS2, EDIFACT e X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Conecte-se a partir de um ambiente de serviço de integração

Para aplicativos lógicos que precisam de acesso direto aos recursos em uma rede virtual do Azure, você pode criar um ambiente de serviço de integração isolado [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde você pode construir, implantar e executar seus aplicativos lógicos em recursos dedicados. No Logic App Designer, quando você navega pelos conectores que deseja usar para aplicativos lógicos em um ISE, um rótulo **CORE** aparece em gatilhos e ações incorporados, enquanto o rótulo **ISE** aparece em alguns conectores:

* **CORE**: Gatilhos e ações incorporados com este rótulo executados na mesma ISE que seus aplicativos lógicos, por exemplo:

  ![Conector ISE exemplo](./media/apis-list/example-core-connector.png)

* **ISE**: Conectores gerenciados com este rótulo são executados na mesma ISE que seus aplicativos lógicos, por exemplo:

  ![Conector ISE exemplo](./media/apis-list/example-ise-connector.png)

  Se você tem um sistema local conectado a uma rede virtual Do Zure, um ISE permite que seus aplicativos lógicos acessem diretamente esse sistema sem o [gateway de dados no local](../logic-apps/logic-apps-gateway-connection.md). Em vez disso, você pode usar o conector **ISE** desse sistema se disponível, uma ação HTTP ou um [conector personalizado](#custom). Para sistemas locais que não possuem conectores **ISE,** use gateway de dados no local. Para revisar os conectores ISE disponíveis, consulte [os conectores ISE](#ise-connectors).

* Todos os outros conectores sem o rótulo **CORE** ou **ISE,** que você pode continuar a usar, executados no serviço global de aplicativos lógicos multilocatários, por exemplo:

  ![Exemplo conector multi-inquilino](./media/apis-list/example-multi-tenant-connector.png)

Os aplicativos lógicos que são executados em um ISE e seus conectores, independentemente de onde esses conectores são executados, seguem um plano de preços fixos em comparação com o plano de preços baseado no consumo. Para obter mais informações, consulte estas páginas:

* [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md)
* [Detalhes de preços da Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Conectar-se a redes virtuais do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Interno

O Logic Apps fornece gatilhos e ações incorporadas para que você possa criar fluxos de trabalho baseados em horários, ajudar seus aplicativos de lógica a se comunicar com outros aplicativos e serviços, controlar o fluxo de trabalho através de seus aplicativos lógicos e gerenciar ou manipular dados.

|   |   |   |   |
|---|---|---|---|
| [![Programação de][schedule-icon]<br>ícones de**API**][schedule-doc] | - Execute um aplicativo lógico em uma recorrência especificada, que vai desde horários básicos até horários avançados com o gatilho [ **Recorrência** ][schedule-recurrence-doc]. <p>- Execute um aplicativo lógico que precisa lidar com dados em blocos contínuos com o [ **gatilho da janela deslizante** ][schedule-sliding-window-doc]. <p>- Pause seu aplicativo lógico por uma duração especificada com a ação [ **Delay** ][schedule-delay-doc]. <p>- Pause seu aplicativo lógico até a data e hora especificadas com o [ **Atraso até** ][schedule-delay-until-doc]a ação . | [![][batch-icon]<br>**Lote** do ícone de API][batch-doc] | - Processe mensagens em lotes com o gatilho **Mensagens em lote**. <p>- Chame aplicativos lógicos que possuem gatilhos de lote existentes com a ação **Enviar mensagens para lote**. |
| [![Ícone de][http-icon]<br>API**HTTP**][http-doc] | Ligue para pontos finais HTTP ou HTTPS com gatilhos e ações para HTTP. Outros gatilhos e ações incorporados HTTP incluem [HTTP + Swagger][http-swagger-doc] e HTTP + [Webhook][http-webhook-doc]. | [![][http-request-icon]<br>**Solicitação** de ícone de API][http-request-doc] | - Faça com que seu aplicativo lógico possa ser chamado de outros aplicativos ou serviços, gatilho em eventos de recursos da Grade de Eventos ou gatilho em respostas aos alertas da Central de Segurança do Azure com o gatilho **Solicitar**. <p>- Envie respostas para um aplicativo ou serviço com a ação **Resposta**. |
| [![API][azure-api-management-icon]<br>icon**Azure <br>API Management**][azure-api-management-doc] | Chame os gatilhos e ações definidos por suas próprias APIs que você gerencia e publica com o Gerenciamento de API do Azure. | [![Ícone da][azure-app-services-icon]<br>API**Azure <br>App Services**][azure-app-services-doc] | Chame Aplicativos de API do Azure ou Aplicativos Web, hospedado no Serviço de Aplicativo do Azure. Os gatilhos e ações definidos por esses aplicativos são exibidos como quaisquer outros gatilhos e ações de primeira classe quando o Swagger é incluído.|
| [![Ícone da][azure-logic-apps-icon]<br>API**Azure <br>Logic Apps**][nested-logic-app-doc] | Chame outros aplicativos lógicos que começam com o gatilho **Solicitação.** |
|||||

### <a name="run-code-from-logic-apps"></a>Executar código a partir de aplicativos lógicos

O Logic Apps fornece ações incorporadas para executar seu próprio código no fluxo de trabalho do seu aplicativo lógico:

|   |   |   |   |
|---|---|---|---|
| [![Funções][azure-functions-icon]<br>do ícone da API**Azure**][azure-functions-doc] | Chame funções do Azure que executam snippets de códigos personalizados (C# ou Node.js) de seus aplicativos lógicos. | [![Ícone de][inline-code-icon]<br>API**Código inline**][azure-functions-doc] | Adicione e execute trechos de código JavaScript de seus aplicativos lógicos. |
|||||

### <a name="control-workflow"></a>Controlar o fluxo de trabalho

O Logic Apps fornece ações incorporadas para estruturar e controlar as ações no fluxo de trabalho do seu aplicativo lógico:

|   |   |   |   |
|---|---|---|---|
| [![Condição de][condition-icon]<br>ícone**incorporado**][condition-doc] | Avalie uma condição e execute ações diferentes com base em se a condição é verdadeira ou falsa. | [![Ícone][for-each-icon]<br>incorporado**para cada**][for-each-doc] | Execute as mesmas ações em cada item em uma matriz. |
| [![][scope-icon]<br>**Escopo** de ícone incorporado][scope-doc] | Agrupe ações em *Escopos*, que obtém seus próprios status após as ações no escopo concluírem a execução. | [![][switch-icon]<br>**Interruptor** de ícone incorporado][switch-doc] | Agrupe ações em *Casos*, que recebem valores exclusivos, exceto para o caso padrão. Execute somente esse caso cujo valor atribuído coincide com o resultado de uma expressão, objeto ou token. Se nenhuma correspondência existir, execute o caso padrão. |
| [![Ícone incorporado][terminate-icon]<br>**terminar**][terminate-doc] | Pare um fluxo de trabalho de aplicativo lógico ativamente em execução. | [![Ícone incorporado][until-icon]<br>**até**][until-doc] | Repita ações até que a condição especificada seja verdadeira ou algum estado seja alterado. |
|||||

### <a name="manage-or-manipulate-data"></a>Gerenciar ou manipular dados

Logic Apps fornece ações incorporadas para trabalhar com saídas de dados e seus formatos:

|   |   |
|---|---|
| [![Operações de][data-operations-icon]<br>dados de**ícones incorporadas**][data-operations-doc] | Execute operações com dados: <p>- **Compor**: crie uma única saída de várias entradas com vários tipos. <br>- **Criar tabela CSV**: crie uma tabela CSV (separada por valores vírgula) de uma matriz com objetos JSON. <br>- **Criar tabela HTML**: crie uma tabela HTML de uma matriz com objetos JSON. <br>- **Matriz de filtro**: crie uma matriz de itens na outra matriz que atendem aos seus critérios. <br>- **Juntar-se**: crie uma cadeia de caracteres de todos os itens em uma matriz e separe esses itens com o delimitador especificado. <br>- **Parse JSON**: Crie tokens fáceis de usar a partir de propriedades e seus valores no conteúdo JSON para que você possa usar essas propriedades em seu fluxo de trabalho. <br>- **Selecionar**: crie uma matriz com objetos JSON transformando itens ou valores em outra matriz e mapeando esses itens para propriedades especificadas. |
| ![Ícone Interno][date-time-icon]<br>**Data/Hora** | Execute operações com carimbos de data/hora: <p>- **Adicionar horário**: adicionar número especificado de unidades a um carimbo de data/hora. <br>- **Converter fuso horário**: converter um carimbo de data/hora do fuso horário de origem para o fuso horário de destino. <br>- **Horário atual**: retornar o carimbo de data/hora atual como uma cadeia de caracteres. <br>- **Obter horário futuro**: retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. <br>- **Obter horário passado**: retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. <br>- **Subtrair de horário**: subtrair um número de unidades de tempo de um carimbo de data/hora. |
| [![Variáveis de][variables-icon]<br>ícone**incorporadas**][variables-doc] | Execute operações com variáveis: <p>- **Acrescentar à variável de matriz**: inserir um valor como o último item em uma matriz armazenada por uma variável. <br>- **Acrescentar à variável de matriz**: inserir um valor como o último caractere em uma cadeia de caracteres armazenada por uma variável. <br>- **Diminuir variável**: diminuir uma variável em um valor constante. <br>- **Incrementar variável**: aumentar uma variável em um valor constante. <br>- **Inicializar variável**: criar uma variável e declarar seu tipo de dados e o valor inicial. <br>- **Definir a variável**: atribuir um valor diferente em uma variável existente. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Conectores gerenciados

O Logic Apps fornece esses conectores Padrão populares para automatizar tarefas, processos e fluxos de trabalho com esses serviços ou sistemas:

|   |   |   |   |
|---|---|---|---|
| [![][azure-service-bus-icon]<br>**Ônibus de serviço azure** ícone da API][azure-service-bus-doc] | Gerencie mensagens assíncronas, sessões e assinaturas de tópico com o conector mais comumente usado em Aplicativos Lógicos. | [![Ícone de][sql-server-icon]<br>API**SQL Server**][sql-server-doc] | Conecte-se ao seu SQL Server no local ou a um banco de dados SQL do Azure na nuvem para que você possa gerenciar registros, executar procedimentos armazenados ou realizar consultas. |
| [![Ícone da][azure-blob-storage-icon]<br>API**Azure<br>Blob Storage**][azure-blob-storage-doc] | Conecte-se à sua conta de armazenamento para que você possa criar e gerenciar conteúdo blob. | [![Ícone da][office-365-outlook-icon]<br>API**Office 365<br>Outlook**][office-365-outlook-doc] | Conecte-se à sua conta de e-mail do Office 365 para que você possa criar e gerenciar e-mails, tarefas, eventos e reuniões de calendário, contatos, solicitações e muito mais. |
| [![Ícone de][sftp-ssh-icon]<br>API**SFTP-SSH**][sftp-ssh-doc] | Conecte-se aos servidores SFTP que você pode acessar a partir da internet usando SSH para que você possa trabalhar com seus arquivos e pastas. | [![Ícone da][sharepoint-online-icon]<br>API**SharePoint<br>Online**][sharepoint-online-doc] | Conecte-se ao SharePoint Online para que você possa gerenciar arquivos, anexos, pastas e muito mais. | 
| [![Ícone da][dynamics-365-icon]<br>API**Dynamics 365<br> **][dynamics-365-doc] | Conecte-se à sua conta Dynamics 365 para que você possa criar e gerenciar registros, itens e muito mais. | [![API][azure-queues-icon]<br>ícone**Azure <br>Filas**][azure-queues-doc] | Conecte-se à sua conta de armazenamento do Azure para que você possa criar e gerenciar filas e mensagens |
| [![Ícone de][ftp-icon]<br>API**FTP**][ftp-doc] | Conecte-se aos servidores FTP que você pode acessar a partir da internet para que você possa trabalhar com seus arquivos e pastas. | [![][file-system-icon]<br>**Sistema de <br>arquivos** do ícone da API][file-system-doc] | Conecte-se ao seu compartilhamento de arquivos no local para que você possa criar e gerenciar arquivos. |
| [![Ícone da][azure-event-hubs-icon]<br>API,**a Azure Event Hubs**][azure-event-hubs-doc] | Como consumir e publicar eventos por meio de um Hub de Eventos. Por exemplo, obtenha uma saída do seu aplicativo lógico com os Hubs de Eventos e, em seguida, envie essa saída para um provedor de análise em tempo real. | [![Ícone da][azure-event-grid-icon]<br>API**Azure Event**<br>**Grid**][azure-event-grid-doc] | Monitore eventos publicados por uma grade de eventos, por exemplo, quando recursos do Azure ou recursos de terceiros são alterados. |
| [![Ícone de][salesforce-icon]<br>API**Salesforce**][salesforce-doc] | Conecte-se à sua conta salesforce para que você possa criar e gerenciar itens como registros, empregos, objetos e muito mais. | [![Ícone da][twitter-icon]<br>API**Twitter**][twitter-doc] | Conecte-se à sua conta do Twitter para que você possa gerenciar tweets, seguidores, sua linha do tempo e muito mais. Salve seus tweets em SQL, o Excel ou SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Conectores locais

Aqui estão alguns conectores Padrão comumente usados que o Logic Apps fornece para acessar dados e recursos em sistemas locais. Antes de criar uma conexão com um sistema local, você deve primeiro [fazer o download, instalar e configurar um gateway de dados local][gateway-doc]. Esse gateway fornece um canal de comunicação seguro sem ter que configurar a infraestrutura de rede necessária.

|   |   |   |   |   |
|---|---|---|---|---|
| [![Ícone da][biztalk-server-icon]<br>API**BizTalk** <br> **Server**][biztalk-server-doc] | [![][file-system-icon]<br>**Sistema de <br>arquivos** do ícone da API][file-system-doc] | [![Ícone de][ibm-db2-icon]<br>API**IBM DB2**][ibm-db2-doc] | [![Ícone da][ibm-informix-icon]<br>API**IBM** <br> **Informix**][ibm-informix-doc] | [![Ícone][mysql-icon]<br>de API**MySQL**][mysql-doc] |
| [![Ícone de][oracle-db-icon]<br>API**Oracle DB**][oracle-db-doc] | [![Ícone da][postgre-sql-icon]<br>API**PostgreSQL**][postgre-sql-doc] | [![Ícone da][sharepoint-server-icon]<br>API,**servidor sharepoint <br>**][sharepoint-server-doc] | [![Ícone de][sql-server-icon]<br>API**SQL <br>Server**][sql-server-doc] | [![Ícone de][teradata-icon]<br>API**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Conectores da conta de integração

A Logic Apps fornece conectores padrão para criar soluções de negócios para negócios (B2B) com seus aplicativos lógicos quando você cria e paga por uma [conta de integração,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)que está disponível através do Enterprise Integration Pack (EIP) no Azure. Com essa conta, você pode criar e armazenar os artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e assim por diante. Para usar esses artefatos, associe seus aplicativos lógicos com sua conta de integração. Se você usa o BizTalk Server atualmente, esses conectores poderão parecer familiares.

|   |   |   |   |
|---|---|---|---|
| [![Decodificação][as2-icon]<br>do ícone de API**AS2 <br>**][as2-doc] | [![Codificação][as2-icon]<br>do ícone de API**AS2 <br>**][as2-doc] | [![Ícone da][edifact-icon]<br>API,**decodificação <br>EDIFACT**][edifact-decode-doc] | [![Codificação][edifact-icon]<br>do ícone da API**EDIFACT <br>**][edifact-encode-doc] |
| [![Ícone de][flat-file-decode-icon]<br>API**Decodificação <br>de arquivos planos**][flat-file-decode-doc] | [![Ícone de][flat-file-encode-icon]<br>API**Codificação de arquivos <br>planos**][flat-file-encode-doc] | [![Conta][integration-account-icon]<br>**de <br>integração do** ícone da API][integration-account-doc] | [![Ícone da][liquid-icon]<br>API,**líquido** <br> **transforma**][json-liquid-transform-doc] |
| [![Ícone de][x12-icon]<br>API**X12 <br>decodificação**][x12-decode-doc] | [![Codificação][x12-icon]<br>do ícone de API**X12 <br>**][x12-encode-doc] | [![Ícone de][xml-transform-icon]<br>API**XML** <br> **transforma**][xml-transform-doc] | [![Validação do][xml-validate-icon]<br>ícone**de API <br>XML**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Conectores empresariais

O Logic Apps fornece esses conectores Enterprise para acessar sistemas corporativos, como O SAP e o IBM MQ:

|   |   |   |
|---|---|---|
| [![Ícone da][ibm-3270-icon]<br>API**IBM 3270**][ibm-3270-doc] | [![Ícone de][ibm-mq-icon]<br>API**IBM MQ**][ibm-mq-doc] | [![Ícone de][sap-icon]<br>**API SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Conectores ISE

Para aplicativos lógicos que você cria e executa um ambiente de serviço de integração isolado [(ISE),](#integration-service-environment)o Logic App Designer identifica gatilhos e ações incorporados que são executados em seu ISE usando o rótulo **CORE.** Conectores gerenciados que são executados em um ISE exibem o rótulo **ISE,** enquanto os conectores executados no serviço global de aplicativos lógicos de vários locatários não exibem nenhum dos rótulos. Esta lista mostra os conectores que atualmente possuem versões ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[![Ícone de][as2-icon]<br>API**AS2**][as2-doc] | [![Ícone da][azure-blob-storage-icon]<br>API**Azure<br>Blob Storage**][azure-blob-storage-doc] | [![Ícone da][azure-cosmos-db-icon]<br>API**Azure <br> Cosmos DB**][azure-cosmos-db-doc] | [![Ícone da][azure-event-hubs-icon]<br>API,**a Azure <br>Event Hubs**][azure-event-hubs-doc] | [![Ícone da][azure-file-storage-icon]<br>API,**o armazenamento de arquivos<br>Azure**][azure-file-storage-doc] |
| [![][azure-service-bus-icon]<br>**Ônibus de serviço <br>azure** ícone da API][azure-service-bus-doc] | [![Ícone da][azure-sql-data-warehouse-icon]<br>API**Azure <br>SQL Data Warehouse**][azure-sql-data-warehouse-doc] | [![Ícone da][azure-table-storage-icon]<br>API,**o armazenamento de tabelas <br>azure**][azure-table-storage-doc] | [![API][azure-queues-icon]<br>ícone**Azure <br>Filas**][azure-queues-doc] | [![Ícone da][edifact-icon]<br>API**EDIFACT**][edifact-doc] |
| [![][file-system-icon]<br>**Sistema de <br>arquivos** do ícone da API][file-system-doc] | [![Ícone de][ftp-icon]<br>API**FTP**][ftp-doc] | [![Ícone da][ibm-3270-icon]<br>API**IBM 3270**][ibm-3270-doc] | [![Ícone de][ibm-db2-icon]<br>API**IBM DB2**][ibm-db2-doc] | [![Ícone de][ibm-mq-icon]<br>API**IBM MQ**][ibm-mq-doc] |
| [![Ícone de][sap-icon]<br>**API SAP**][sap-connector-doc] | [![Ícone de][sftp-ssh-icon]<br>API**SFTP-SSH**][sftp-ssh-doc] | [![Ícone de][smtp-icon]<br>API**SMTP**][smtp-doc] | [![Ícone de][sql-server-icon]<br>API**SQL <br>Server**][sql-server-doc] | [![Ícone de][x12-icon]<br>API**X12**][x12-doc] |
||||||

Para saber mais, consulte esses tópicos:

* [Acesso aos recursos de rede virtual do Azure a partir de aplicativos de lógica do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md)
* [Conectar-se a redes virtuais do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Gatilhos e tipos de ação

Os conectores podem fornecer *gatilhos,* *ações*ou ambos. Um *gatilho* é o primeiro passo em qualquer aplicativo lógico, geralmente especificando o evento que dispara o gatilho e começa a executar seu aplicativo lógico. Por exemplo, o conector FTP tem um gatilho que inicia seu aplicativo lógico "quando um arquivo é adicionado ou modificado". Alguns gatilhos verificam regularmente o evento ou dados especificados e, em seguida, disparam quando detectam o evento ou dados especificados. Outros gatilhos esperam, mas disparam instantaneamente quando um evento específico acontece ou quando novos dados estão disponíveis. Os gatilhos também transmitem todos os dados necessários para o seu aplicativo lógico. Seu aplicativo lógico pode ler e usar esses dados durante todo o fluxo de trabalho. Por exemplo, o conector do Twitter tem um gatilho, "Quando um novo tweet é postado", que passa o conteúdo do tweet para o fluxo de trabalho do seu aplicativo lógico.

Depois que um gatilho é acionado, o Azure Logic Apps cria uma instância do seu aplicativo lógico e começa a executar as *ações* no fluxo de trabalho do seu aplicativo lógico. Ações são as etapas que seguem o gatilho e executam tarefas no fluxo de trabalho do seu aplicativo lógico. Por exemplo, você pode criar um aplicativo lógico que obtém dados do cliente de um banco de dados SQL e processar esses dados em ações posteriores.

Aqui estão os tipos gerais de gatilhos que o Azure Logic Apps fornece:

* *Gatilho de recorrência*: Este gatilho é executado em um cronograma especificado e não está fortemente associado a um determinado serviço ou sistema.

* *Gatilho de votação*: Este gatilho pesquisa regularmente um serviço ou sistema específico com base no cronograma especificado, verificando novos dados ou se um evento específico aconteceu. Se novos dados estão disponíveis ou o evento específico aconteceu, o gatilho cria e executa uma nova instância do seu aplicativo lógico, que agora pode usar os dados que são passados como entrada.

* *Gatilho :* Este gatilho espera e ouve novos dados ou para que um evento aconteça. Quando novos dados estão disponíveis ou quando o evento acontece, o gatilho cria e executa uma nova instância do seu aplicativo lógico, que agora pode usar os dados que são passados como entrada.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configuração do conector

Os gatilhos e ações de cada conector fornecem suas próprias propriedades para você configurar. Muitos conectores também exigem que você primeiro crie uma *conexão com* o serviço ou sistema de destino e forneça credenciais de autenticação ou outros detalhes de configuração antes de poder usar um gatilho ou ação em seu aplicativo lógico. Por exemplo, você deve autorizar uma conexão a uma conta do Twitter para acessar dados ou postar em seu nome.

Para conectores que usam o Azure Active Directory (Azure AD) OAuth, criar uma conexão significa entrar no serviço, como office 365, Salesforce ou GitHub, onde seu token de acesso é [criptografado](../security/fundamentals/encryption-overview.md) e armazenado com segurança em uma loja secreta do Azure. Outros conectores, como FTP e SQL, exigem uma conexão que tenha detalhes de configuração, como endereço do servidor, nome de usuário e senha. Esses detalhes de configuração de conexão também são criptografados e armazenados com segurança. Saiba mais sobre [criptografia no Azure](../security/fundamentals/encryption-overview.md).

As conexões podem acessar o serviço de destino ou o sistema pelo tempo que esse serviço ou sistema permitir. Para serviços que usam conexões Azure AD OAuth, como Office 365 e Dynamics, o Azure Logic Apps atualiza os tokens de acesso indefinidamente. Outros serviços podem ter limites de quanto tempo os Aplicativos Azure Logic podem usar um token sem ser atualizado. Geralmente, algumas ações invalidam todos os tokens de acesso, como alterar sua senha.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Conectores e APIs personalizados

Para chamar as APIs que executam o código personalizado ou não estão disponíveis como conectores, você pode estender a plataforma dos Aplicativos Lógicos [criando Aplicativos de API personalizados](../logic-apps/logic-apps-create-api-app.md). Você também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para *qualquer* API REST ou baseadas em SOAP, o que torna essas APIs disponíveis para qualquer aplicativo lógico em sua assinatura do Azure. Para tornar Aplicativos de API personalizado ou conectores público disponíveis para qualquer pessoa para uso no Azure, você pode [enviar conectores para certificação da Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Os aplicativos lógicos que você implanta e executa em um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) podem acessar diretamente recursos em uma rede virtual Do Zure. Se você tiver conectores personalizados que requerem o gateway de dados no local e você criou esses conectores fora de um ISE, os aplicativos lógicos em um ISE também podem usar esses conectores.
>
> Os conectores personalizados criados dentro de um ISE não funcionam com o gateway de dados no local. No entanto, esses conectores podem acessar diretamente fontes de dados locais que estão conectadas a uma rede virtual Azure que hospeda o ISE. Assim, os aplicativos lógicos em um ISE provavelmente não precisam do gateway de dados ao se comunicar com esses recursos.
>
> Para obter mais informações sobre a criação de ISEs, consulte [Conectar-se às redes virtuais do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Próximas etapas

* Veja a [lista completa de conectores](https://docs.microsoft.com/connectors)
* [Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Criar conectores personalizados para aplicativos lógicos](https://docs.microsoft.com/connectors/custom-connectors/)
* [Criar APIs personalizadas para aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Conectar-se a fontes de dados locais de aplicativos lógicos com o gateway de dados local"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Criar uma instância de serviço de Gerenciamento de API do Azure para gerenciar e publicar suas APIs"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integre aplicativos lógicos com aplicativos de API de serviço de aplicativos"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integre aplicativos lógicos com funções do Azure"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Processar mensagens em grupos ou como lotes"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Avalie uma condição e execute diferentes ações com base em se a condição é verdadeira ou falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Execute as mesmas ações em cada item em uma matriz"
[http-doc]: ./connectors-native-http.md "Ligue para pontos finais HTTP ou HTTPS de seus aplicativos lógicos"
[http-request-doc]: ./connectors-native-reqres.md "Receba solicitações HTTP em seus aplicativos lógicos"
[http-response-doc]: ./connectors-native-reqres.md "Responda a solicitações HTTP de seus aplicativos lógicos"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chame pontos finais rest de seus aplicativos lógicos"
[http-webhook-doc]: ./connectors-native-webhook.md "Aguarde eventos específicos de pontos finais HTTP ou HTTPS"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrar aplicativos lógicos a fluxos de trabalho aninhados"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selecionar e filtrar matrizes com a ação Consultar"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Executar aplicativos lógicos com base em um cronograma"
[schedule-delay-doc]: ./connectors-native-delay.md "Demore a executar a próxima ação"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Demore a executar a próxima ação"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Execute aplicativos lógicos em um cronograma recorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Execute aplicativos lógicos que precisam lidar com dados em pedaços contíguos"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizar ações em grupos, o que obtém seu próprio status após a conclusão da execução das ações"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organize ações em casos, que são atribuídos valores únicos. Execute apenas o estojo cujo valor corresponda ao resultado de uma expressão, objeto ou token. Se não houver correspondências, execute o caso padrão"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Parar ou cancelar um fluxo de trabalho em execução ativamente para seu aplicativo lógico"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Repita as ações até que a condição especificada seja verdadeira ou algum estado tenha mudado"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Executar as operações de dados, como filtragem de matrizes ou criação de tabelas HTML e de CSV"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Executar operações com variáveis, como inicializar, configurar, aumentar ou diminuir e acrescentar à variável de cadeia de caracteres ou matriz"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gerenciar arquivos em seu contêiner de blob com o conector de Armazenamento de Blobs do Azure"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Conecte-se ao Azure Cosmos DB para que você possa acessar documentos e procedimentos armazenados"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitore eventos publicados por uma Grade de Eventos, por exemplo, quando os recursos do Azure ou recursos de terceiros mudam"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Conecte-se aos Hubs de Eventos do Azure para que você possa receber e enviar eventos entre aplicativos lógicos e Hubs de Eventos"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Conecte-se à sua conta do Azure Storage para que você possa criar, atualizar, obter e excluir arquivos"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Conecte-se à sua conta de armazenamento do Azure para que você possa criar e gerenciar filas e mensagens"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Envia mensagens de tópicos e filas do barramento de serviço e recebe mensagens de assinaturas e filas do barramento de serviço"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Conecte-se ao Azure SQL Data Warehouse para que você possa visualizar seus dados"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Conecte-se à sua conta do Azure Storage para que você possa criar, atualizar e consultar tabelas e muito mais"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Conecte-se ao seu BizTalk Server para que você possa executar aplicativos baseados no BizTalk lado a lado com aplicativos azure logic"
[box-doc]: ./connectors-create-api-box.md "Conecte-se à Caixa. Carregar, obter, excluir, listar seus arquivos e muito mais"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Conecte-se ao Dropbox. Carregar, obter, excluir, listar seus arquivos e muito mais"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Conecte-se ao Dynamics CRM Online para que você possa trabalhar com dados CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Conecte-se ao Facebook. Poste em uma linha do tempo, obtenha um feed de página e muito mais"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Conectar-se a um sistema de arquivos local"
[ftp-doc]: ./connectors-create-api-ftp.md "Conectar-se a um servidor FTP / FTPS e para tarefas FTP, incluindo carregar, obter, excluir arquivos e muito mais"
[github-doc]: ./connectors-create-api-github.md "Conectar-se ao GitHub e acompanhar os problemas"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Conecta-se ao Google Calendar e pode gerenciar o calendário"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Conecte-se ao GoogleDrive para que você possa trabalhar com seus dados"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Conecte-se às planilhas do Google para que você possa modificar suas planilhas"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Conecta-se às tarefas do Google para que você possa gerenciar suas tarefas"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Conecte-se a 3270 aplicativos nos mainframes da IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Conecte-se ao IBM DB2 na nuvem ou no local. Atualize uma linha, obtenha uma tabela e muito mais"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Conecte-se ao Informix na nuvem ou no local. Leia uma linha, liste as tabelas e muito mais"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Conectar-se a locais do IBM MQ ou ao Azure para enviar e receber mensagens"
[instagram-doc]: ./connectors-create-api-instagram.md "Conecte-se ao Instagram. Desencadear ou agir em eventos"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Conecte-se à sua conta do MailChimp. Gerencie e automatize e-mails"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Conectar-se ao Mandrill para comunicação"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Conecte-se ao seu banco de dados MySQL no local para que você possa ler e gravar dados"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Conecte-se à sua conta do Office 365 para que você possa enviar e receber e-mails, gerenciar seu calendário e contatos e muito mais"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Conecte-se ao seu Microsoft OneDrive pessoal para que você possa carregar, excluir, listar arquivos e muito mais"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Conecte-se à sua empresa Microsoft OneDrive para que você possa carregar, excluir, listar seus arquivos e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Conecte-se a um banco de dados Oracle para que você possa adicionar, inserir, excluir linhas e muito mais"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Conecte-se à caixa de correio do Outlook para que você possa gerenciar seu e-mail, calendários, contatos e muito mais"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Conecte-se ao seu banco de dados PostgreSQL para que você possa ler dados de tabelas"
[project-online-doc]: ./connectors-create-api-projectonline.md "Conecte-se ao Microsoft Project Online para que você possa gerenciar seus projetos, tarefas, recursos e muito mais"
[rss-doc]: ./connectors-create-api-rss.md "Publicar e recuperar itens de alimentação, acionar operações quando um novo item é publicado em um feed RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Conecte-se à sua conta salesforce. Gerencie contas, leads, oportunidades e muito mais"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Conectar-se a um sistema SAP local"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Conecte-se ao SendGrid. Enviar e-mails e gerenciar listas de destinatários"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Conecte-se à sua conta SFTP usando SSH. Carregar, obter, excluir arquivos e muito mais"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Conecte-se ao servidor local SharePoint. Gerenciar documentos, listar itens e muito mais"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Conecte-se ao SharePoint Online. Gerenciar documentos, listar itens e muito mais"
[slack-doc]: ./connectors-create-api-slack.md "Conecte-se ao Slack e poste mensagens nos canais do Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Conectar-se a um servidor SMTP e enviar emails com anexos"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Conectar-se ao SparkPost para comunicação."
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Conecte-se ao Banco de Dados SQL do Azure ou ao SQL Server. Criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Conecte-se ao seu banco de dados Teradata para ler dados de tabelas"
[trello-doc]: ./connectors-create-api-trello.md "Conecte-se ao Trello. Gerencie seus projetos e organize qualquer coisa com qualquer pessoa"
[twilio-doc]: ./connectors-create-api-twilio.md "Ligue para Twilio. Enviar e receber mensagens, obter números disponíveis, gerenciar números de telefone de entrada e muito mais"
[twitter-doc]: ./connectors-create-api-twitter.md "Conecte-se ao Twitter. Obtenha cronogramas, poste tweets e muito mais"
[yammer-doc]: ./connectors-create-api-yammer.md "Conecte-se a Yammer. Postar mensagens, receber novas mensagens e muito mais"
[youtube-doc]: ./connectors-create-api-youtube.md "Conecte-se ao YouTube. Gerencie seus vídeos e canais"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificar e decodificar mensagens que usam o protocolo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificar e decodificar mensagens que usam o protocolo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodificar mensagens que usam o protocolo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificar mensagens que usam o protocolo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Conheça o arquivo plano de integração corporativa"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Conheça o arquivo plano de integração corporativa"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gerenciar metadados para artefatos de conta de integração"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transforme JSON com modelos líquidos"
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
