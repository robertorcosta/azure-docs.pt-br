---
title: Conecte-se a 3270 aplicativos nos mainframes da IBM
description: Integre e automatize 3270 aplicativos orientados por tela com o Azure usando o Azure Logic Apps e o conector IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371114"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integre 3270 aplicativos orientados por tela nos mainframes da IBM com o Azure usando o Azure Logic Apps e o conector IBM 3270

> [!NOTE]
> Este conector está em [*visualização pública*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Com o Azure Logic Apps e o conector IBM 3270, você pode acessar e executar aplicativos de mainframe da IBM que você normalmente dirige navegando através de telas de emuladores 3270. Dessa forma, você pode integrar seus aplicativos de mainframe da IBM com o Azure, a Microsoft e outros aplicativos, serviços e sistemas, criando fluxos de trabalho automatizados com o Azure Logic Apps. O conector se comunica com os mainframes da IBM usando o protocolo TN3270 e está disponível em todas as regiões do Azure Logic Apps, exceto no Azure Government e no Azure China 21Vianet. Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Este artigo descreve esses aspectos para usar o conector 3270: 

* Por que usar o conector IBM 3270 no Azure Logic Apps e como o conector executa 3270 aplicativos orientados por tela

* Os pré-requisitos e configuração para usar o conector 3270

* As etapas para adicionar 3270 ações de conector ao seu aplicativo lógico

## <a name="why-use-this-connector"></a>Por que usar esse conector?

Para acessar aplicativos nos mainframes da IBM, você normalmente usa um emulador de terminal 3270, muitas vezes chamado de "tela verde". Este método é uma maneira testada pelo tempo, mas tem limitações. Embora o Host Integration Server (HIS) ajude você a trabalhar diretamente com esses aplicativos, às vezes, separar a tela e a lógica de negócios pode não ser possível. Ou talvez você não tenha mais informações sobre como os aplicativos host funcionam.

Para estender esses cenários, o conector IBM 3270 no Azure Logic Apps funciona com o 3270 Design Tool, que você usa para gravar, ou "capturar", as telas host usadas para uma tarefa específica, definir o fluxo de navegação para essa tarefa através do seu aplicativo de mainframe e definir o métodos com parâmetros de entrada e saída para essa tarefa. A ferramenta de design converte essas informações em metadados que o conector 3270 usa ao chamar uma ação que representa essa tarefa a partir do seu aplicativo lógico.

Depois de gerar o arquivo de metadados da ferramenta de design, você adiciona esse arquivo a uma conta de integração no Azure. Dessa forma, seu aplicativo lógico pode acessar os metadados do seu aplicativo quando você adiciona uma ação de conector 3270. O conector lê o arquivo de metadados da sua conta de integração, lida com a navegação através das telas 3270 e apresenta dinamicamente os parâmetros para a ação do conector 3270. Em seguida, você pode fornecer dados para o aplicativo host, e o conector retorna os resultados para o seu aplicativo lógico. Dessa forma, você pode integrar seus aplicativos legados com o Azure, a Microsoft e outros aplicativos, serviços e sistemas que o Azure Logic Apps suporta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recomendado: Um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Você pode selecionar este ambiente como o local para criar e executar seu aplicativo lógico. Um ISE fornece acesso do seu aplicativo lógico a recursos protegidos dentro das redes virtuais do Azure.

* O aplicativo lógico para usar para automatizar e executar seu aplicativo 3270 orientado por tela

  O conector IBM 3270 não tem gatilhos, então use outro gatilho para iniciar seu aplicativo lógico, como o gatilho **Recorrência.** Em seguida, você pode adicionar 3270 ações de conector. Para começar, [crie um aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Se você usar um ISE, selecione esse ISE como a localização do seu aplicativo lógico.

* [Baixe e instale a Ferramenta de Design 3270](https://aka.ms/3270-design-tool-download).
O único pré-requisito é [o Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Esta ferramenta ajuda você a gravar as telas, caminhos de navegação, métodos e parâmetros para as tarefas em seu aplicativo que você adiciona e executa como ações de conector 3270. A ferramenta gera um arquivo Host Integration Designer XML (HIDX) que fornece os metadados necessários para o conector usar para dirigir seu aplicativo de mainframe.
  
  Depois de baixar e instalar esta ferramenta, siga estas etapas para se conectar ao seu host:

  1. Abra a ferramenta de design 3270. No menu **Sessão,** selecione **Sessões de Host**.
  
  1. Forneça as informações do servidor host TN3270.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), que é o lugar onde você armazena seu arquivo HIDX como um mapa para que seu aplicativo lógico possa acessar as definições de metadados e método nesse arquivo. 

  Certifique-se de que sua conta de integração está vinculada ao aplicativo lógico que você está usando. Além disso, se você usar um ISE, certifique-se de que a localização da sua conta de integração seja a mesma ISE que seu aplicativo lógico usa.

* Acesso ao servidor TN3270 que hospeda seu aplicativo de mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Criar visão geral de metadados

Em um aplicativo 3270 baseado em tela, as telas e campos de dados são exclusivos de seus cenários, então o conector 3270 precisa dessas informações sobre o seu aplicativo, que você pode fornecer como metadados. Esses metadados descrevem informações que ajudam seu aplicativo lógico a identificar e reconhecer telas, descreve como navegar entre telas, onde inserir dados e onde esperar resultados. Para especificar e gerar esses metadados, você usa a Ferramenta de Design 3270, que o acompanha nesses modos específicos, ou *estágios,* conforme descrito posteriormente em mais detalhes:

* **Captura**: Neste modo, você grava as telas necessárias para completar uma tarefa específica com seu aplicativo de mainframe, por exemplo, obtendo um saldo bancário.

* **Navegação**: Neste modo, você especifica o plano ou caminho de como navegar pelas telas do seu aplicativo de mainframe para a tarefa específica.

* **Métodos**: Neste modo, você define o `GetBalance`método, por exemplo, que descreve o caminho de navegação da tela. Você também escolhe os campos em cada tela que se tornam os parâmetros de entrada e saída do método.

### <a name="unsupported-elements"></a>Elementos não suportados

A ferramenta de design não suporta esses elementos:

* Mapas parciais de BMS (Basic Mapping Support, suporte de mapeamento básico) da IBM: Se você importar um mapa BMS, a ferramenta de design ignora definições parciais de tela.
* Parâmetros de in/out: Você não pode definir parâmetros de in/out.
* Processamento do menu: Não suportado durante a visualização
* Processamento de array: Não suportado durante a visualização

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Telas de captura

Neste modo, você marca um item em cada tela 3270 que identifica com exclusividade essa tela. Por exemplo, você pode especificar uma linha de texto ou um conjunto mais complexo de condições, como texto específico e um campo não vazio. Você pode gravar essas telas através de uma conexão ao vivo com o servidor host ou importar essas informações de um mapa BMS (Basic Mapping Support, suporte básico de mapeamento básico) da IBM. A conexão ao vivo usa um emulador TN3270 para se conectar ao host. Cada ação do conector deve mapear para uma única tarefa que começa com a conexão à sua sessão e termina com a desconexão da sessão.

1. Se você ainda não o fez, abra a Ferramenta de Design 3270. Na barra de ferramentas, escolha **Capturar** para que você insira o modo Captura.

1. Para iniciar a gravação, pressione a tecla F5 ou no menu **Gravação,** **selecione Iniciar gravação**. 

1. No menu **Sessão,** selecione **Conectar**.

1. No painel **Capturar,** a partir da primeira tela do seu aplicativo, entre no aplicativo para a tarefa específica que você está gravando.

1. Depois de terminar a tarefa, saia do seu aplicativo como costuma fazer.

1. No menu **Sessão,** selecione **Desconectar**.

1. Para interromper a gravação, pressione as teclas Shift + F5 ou no menu **Gravação,** **selecione Stop Recording**.

   Depois de capturar as telas para uma tarefa, a ferramenta de designer mostra miniaturas que representam essas telas. Algumas notas sobre essas miniaturas:

   * Incluído com suas telas capturadas, você tem uma tela chamada "Empty".

     Quando você se conectar pela primeira vez ao [CICS,](https://www.ibm.com/it-infrastructure/z/cics)você deve enviar a tecla "Limpar" antes de inserir o nome da transação que deseja executar. A tela para onde você envia a tecla "Clear" não tem nenhum *atributo de reconhecimento,* como um título de tela, que você pode adicionar usando o editor de reconhecimento de tela. Para representar esta tela, as miniaturas incluem uma tela chamada "Vazia". Mais tarde, você pode usar esta tela para representar a tela onde você digita o nome da transação.

   * Por padrão, o nome de uma tela capturada usa a primeira palavra na tela. Se esse nome já existir, a ferramenta de design anexa o nome com um sublinhado e um número, por exemplo, "WBGB" e "WBGB_1".

1. Para dar um nome mais significativo a uma tela capturada, siga estas etapas:

   1. No painel **Tela de Host,** selecione a tela que deseja renomear.

   1. No mesmo painel, perto da parte inferior no mesmo painel, encontre a propriedade **Screen Name.**

   1. Altere o nome de tela atual para um nome mais descritivo.

1. Agora especifique os campos para identificar cada tela.

   Com o fluxo de dados 3270, as telas não têm identificadores padrão, então você precisa selecionar texto único em cada tela. Para cenários complexos, você pode especificar várias condições, por exemplo, texto único e um campo com uma condição específica.

Depois de terminar de selecionar os campos de reconhecimento, mude para o próximo modo.

### <a name="conditions-for-identifying-repeated-screens"></a>Condições para identificar telas repetidas

Para que o conector navegue e diferencie entre as telas, você geralmente encontra texto único em uma tela que você pode usar como um identificador entre as telas capturadas. Para telas repetidas, você pode precisar de mais métodos de identificação. Por exemplo, suponha que você tenha duas telas que parecem iguais, exceto que uma tela retorna um valor válido, enquanto a outra tela retorna uma mensagem de erro.

Na ferramenta de design, você pode adicionar *atributos de reconhecimento,* por exemplo, um título de tela como "Obter saldo da conta", usando o editor de reconhecimento de tela. Se você tem um caminho bifurcado e ambos os ramos retornam a mesma tela, mas com resultados diferentes, você precisa de outros atributos de reconhecimento. No tempo de execução, o conector usa esses atributos para determinar o ramo e o garfo atuais. Aqui estão as condições que você pode usar:

* Valor específico: Este valor corresponde à seqüência de string especificada no local especificado.
* NÃO um valor específico: Este valor não corresponde à seqüência especificada no local especificado.
* Este campo está vazio.
* Não está vazio: este campo não está vazio.

Para saber mais, consulte o [plano de navegação Exemplo](#example-plan) mais tarde neste tópico.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definir planos de navegação

Neste modo, você define o fluxo ou os passos para navegar através das telas do seu aplicativo de mainframe para sua tarefa específica. Por exemplo, às vezes, você pode ter mais de um caminho que seu aplicativo pode tomar onde um caminho produz o resultado correto, enquanto o outro caminho produz um erro. Para cada tela, especifique as teclas necessárias `CICSPROD <enter>`para mover-se para a próxima tela, tais como .

> [!TIP]
> Se você estiver automatizando várias tarefas que usam as mesmas telas de conexão e desconexão, a ferramenta de design fornece tipos especiais de plano Connect e Disconnect. Quando você define esses planos, você pode adicioná-los ao início e ao fim do seu plano de navegação.

### <a name="guidelines-for-plan-definitions"></a>Diretrizes para definições de planos

* Inclua todas as telas, começando com a conexão e terminando com a desconexão.

* Você pode criar um plano autônomo ou usar os planos Conectar e Desconectar, que permitem reutilizar uma série de telas comuns a todas as suas transações.

  * A última tela do seu plano Connect deve ser a mesma tela da primeira tela do seu plano de navegação.

  * A primeira tela do seu plano Desconectar deve ser a mesma tela da última tela do seu plano de navegação.

* Suas telas capturadas podem conter muitas telas repetidas, então selecione e use apenas uma instância de qualquer tela repetida em seu plano. Aqui estão alguns exemplos de telas repetidas:

  * A tela de login, por exemplo, a tela **MSG-10**
  * A tela de boas-vindas para cics
  * A tela "Clara" ou **Vazia**

<a name="create-plans"></a>

### <a name="create-plans"></a>Criar planos

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **Navegação** para que você entre no modo de navegação.

1. Para iniciar seu plano, no painel **Navegação,** escolha **Novo Plano**.

1. Em **Escolha Nome do Novo Plano,** digite um nome para o seu plano. Na lista **Tipo,** selecione o tipo de plano:

   | Tipo de plano | Descrição |
   |-----------|-------------|
   | **Processo** | Para planos autônomos ou combinados |
   | **Conectar** | Para planos Connect |
   | **Desconectar** | Para planos de desconexão |
   |||

1. Do painel **Tela de Host,** arraste as miniaturas capturadas para a superfície do plano de navegação no painel **navegação.**

   Para representar a tela em branco onde você digita o nome da transação, use a tela "Vazio".

1. Organize as telas na ordem que descreve a tarefa que você está definindo.

1. Para definir o caminho de fluxo entre telas, incluindo garfos e junções, na barra de ferramentas da ferramenta de design, escolha **Flow**.

1. Escolha a primeira tela no fluxo. Arraste e desenhe uma conexão com a próxima tela no fluxo.

1. Para cada tela, forneça os valores para a propriedade **AID Key** (Identificador de Atenção) e para a propriedade **Texto Fixo,** que move o fluxo para a próxima tela.

   Você pode ter apenas a tecla AID, ou a tecla AID e o texto fixo.

Depois de terminar seu plano de navegação, você pode [definir métodos no próximo modo](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemplo

Neste exemplo, suponha que você execute uma transação CICS chamada "WBGB" que tenha essas etapas: 

* Na primeira tela, você digita um nome e uma conta.
* Na segunda tela, você recebe o saldo da conta.
* Você sai para a tela "Vazio".
* Você sai do CICS para a tela "MSG-10".

Suponha também que você repita essas etapas, mas digite dados incorretos para capturar a tela que mostra o erro. Aqui estão as telas que você captura:

* MSG-10
* CICS Bem-vindo
* Vazio
* WBGB_1 (entrada)
* WBGB_2 (erro)
* Empty_1
* MSG-10_1

Embora muitas telas aqui obtenham nomes únicos, algumas telas são a mesma tela, por exemplo, "MSG-10" e "Empty". Para uma tela repetida, use apenas uma instância para essa tela em seu plano. Aqui estão exemplos que mostram como um plano autônomo, plano de conexão, plano de desconexão e um plano combinado podem parecer:

* Plano autônomo

  ![Plano de navegação autônomo](./media/connectors-create-api-3270/standalone-plan.png)

* Plano de conexão

  ![Plano de conexão](./media/connectors-create-api-3270/connect-plan.png)

* Plano de desconexão

  ![Plano de desconexão](./media/connectors-create-api-3270/disconnect-plan.png)

* Plano combinado

  ![Plano combinado](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemplo: Identifique telas repetidas

Para que o conector navegue e diferencie as telas, você geralmente encontra texto único em uma tela que você pode usar como um identificador através das telas capturadas. Para telas repetidas, você pode precisar de mais métodos de identificação. O plano de exemplo tem um garfo onde você pode obter telas que se parecem. Uma tela retorna um saldo da conta, enquanto a outra tela retorna uma mensagem de erro.

A ferramenta de design permite adicionar atributos de reconhecimento, por exemplo, um título de tela chamado "Obter saldo da conta", usando o editor de reconhecimento de tela. No caso com telas semelhantes, você precisa de outros atributos. No tempo de execução, o conector usa esses atributos para determinar o ramo e o garfo.

* No ramo que retorna entrada válida, que é a tela com o saldo da conta, você pode adicionar um campo que tem uma condição "não vazia".

* No ramo que retorna com um erro, você pode adicionar um campo que tenha uma condição "vazia".

<a name="define-method"></a>

## <a name="define-methods"></a>Definir métodos

Neste modo, você define um método associado ao seu plano de navegação. Para cada parâmetro de método, você especifica o tipo de dados, como uma seqüência, inteiro, data ou hora, e assim por diante. Quando terminar, você pode testar seu método no host ao vivo e confirmar que o método funciona como esperado. Em seguida, você gera o arquivo de metadados ou o arquivo Host Integration Designer XML (HIDX), que agora tem as definições de método para usar para criar e executar uma ação para o conector IBM 3270.

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **Métodos** para que você entre no modo Métodos. 

1. No painel **Navegação,** selecione a tela que tem os campos de entrada desejados.

1. Para adicionar o primeiro parâmetro de entrada para o seu método, siga estas etapas:

   1. No painel **Capturar,** na tela do emulador 3270, escolha todo o campo, não apenas texto dentro do campo, que você deseja como a primeira entrada.

      > [!TIP]
      > Para exibir todos os campos e certifique-se de selecionar o campo completo, no menu **Exibir,** selecione **Todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **Campo de entrada**. 

   Para adicionar mais parâmetros de entrada, repita as etapas anteriores para cada parâmetro.

1. Para adicionar o primeiro parâmetro de saída para o seu método, siga estas etapas:

   1. No painel **Capturar,** na tela do emulador 3270, escolha todo o campo, não apenas texto dentro do campo, que você deseja como a primeira saída.

      > [!TIP]
      > Para exibir todos os campos e certifique-se de selecionar o campo completo, no menu **Exibir,** selecione **Todos os campos**.

   1. Na barra de ferramentas da ferramenta de design, escolha **'Campo de saída '''Campo de saída'.**

   Para adicionar mais parâmetros de saída, repita as etapas anteriores para cada parâmetro.

1. Depois de adicionar todos os parâmetros do seu método, defina essas propriedades para cada parâmetro:

   | Nome da propriedade | Valores possíveis | 
   |---------------|-----------------|
   | **Tipo de dados** | Byte, Hora da Data, Decimal, Int, Long, Short, String |
   | **Técnica de preenchimento de campo** | Os parâmetros suportam esses tipos de preenchimento, preenchendo com espaços em branco, se necessário: <p><p>- **Tipo**: Insira caracteres sequencialmente no campo. <p>- **Preenchimento**: Substitua o conteúdo do campo por caracteres, preenchendo-se com espaços em branco, se necessário. <p>- **ApagadaEofType**: Limpe o campo e, em seguida, insira caracteres sequencialmente no campo. |
   | **String de formato** | Alguns tipos de dados de parâmetros usam uma string de formato, que informa o conector 3270 como converter texto da tela em um tipo de dados .NET: <p><p>- **Data:** A seqüência de formato DateTime segue as [seqüências de formato de data e hora personalizadas .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Por exemplo, `06/30/2019` a data `MM/dd/yyyy`usa a seqüência de formato . <p>- **Decimal**: A seqüência de formato decimal usa a [cláusula COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Por exemplo, `100.35` o número `999V99`usa a seqüência de formato . |
   |||

## <a name="save-and-view-metadata"></a>Salvar e visualizar metadados

Depois de definir seu método, mas antes de testar seu método, salve todas as informações que você definiu até agora para um arquivo RAP (.rap).
Você pode salvar neste arquivo RAP a qualquer momento durante qualquer modo. A ferramenta de design também inclui um arquivo RAP de exemplo que você pode abrir e revisar navegando para a pasta de instalação da ferramenta de design neste local e abrindo o arquivo "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

No entanto, se você tentar salvar alterações no arquivo RAP amostra ou gerar um arquivo HIDX a partir do arquivo RAP de amostra enquanto o arquivo permanecer na pasta de instalação da ferramenta de design, você pode obter um erro de "acesso negado". Por padrão, a ferramenta de design é instalada na pasta Arquivos do programa sem permissões elevadas. Se você tiver um erro, tente uma dessas soluções:

* Copie o arquivo de amostra para um local diferente.
* Execute a ferramenta de design como administrador.
* Torne-se o dono da pasta SDK.

## <a name="test-your-method"></a>Teste seu método

1. Para executar o seu método contra o host ao vivo, enquanto ainda estiver no modo Métodos, pressione a tecla F5 ou na barra de ferramentas da ferramenta de design, escolha **Executar**.

   > [!TIP]
   > Você pode mudar de modo a qualquer momento. No **menu Arquivo,** selecione **Modo**e selecione o modo desejado.

1. Digite os valores de seus parâmetros e escolha **OK**.

1. Para continuar até a próxima tela, escolha **Next**.

1. Quando terminar, escolha **Concluído,** que mostra os valores do parâmetro de saída.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Gerar e carregar o arquivo HIDX

Quando estiver pronto, gere o arquivo HIDX para que você possa carregar para sua conta de integração. A Ferramenta de Design 3270 cria o arquivo HIDX em uma nova subpasta onde você salvou seu arquivo RAP.

1. Na barra de ferramentas da Ferramenta de Design 3270, escolha **Gerar código**.

1. Vá para a pasta que contém seu arquivo RAP e abra a subpasta que a ferramenta criou após gerar seu arquivo HIDX. Confirme se a ferramenta criou o arquivo HIDX.

1. Faça login no [portal Azure](https://portal.azure.com)e encontre sua conta de integração.

1. Adicione seu arquivo HIDX como um mapa à sua conta de [integração, siga estas etapas semelhantes para adicionar mapas,](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)mas quando você selecionar o tipo de mapa, selecione **HIDX**.

Mais tarde, neste tópico, quando você adiciona uma ação IBM 3270 ao seu aplicativo lógico pela primeira vez, você é solicitado a criar uma conexão entre seu aplicativo lógico e o servidor host, fornecendo informações de conexão, como os nomes para sua conta de integração e servidor host . Depois de criar a conexão, você pode selecionar seu arquivo HIDX adicionado anteriormente, o método a ser executado e os parâmetros a serem usados.

Ao terminar todas essas etapas, você pode usar a ação que você cria em seu aplicativo lógico para se conectar ao seu mainframe ibm, dirigir telas para o seu aplicativo, inserir dados, retornar resultados e assim por diante. Você também pode continuar adicionando outras ações ao seu aplicativo lógico para se integrar com outros aplicativos, serviços e sistemas.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Execute as ações do IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na última etapa, você deseja adicionar uma ação, escolha **Nova etapa**e **selecione Adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **Enterprise**. Na caixa de pesquisa, digite "3270" como seu filtro. Na lista de ações, selecione esta ação: **Executa um programa de mainframe sobre uma conexão TN3270**

   ![Selecione 3270 ação](./media/connectors-create-api-3270/select-3270-action.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal**+** de adição () que aparece e, em seguida, **selecione Adicionar uma ação**.

1. Se ainda não houver conexão, forneça as informações necessárias para sua conexão e escolha **Criar**.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | <*nome de conexão*> | O nome para a sua conexão |
   | **ID da conta de integração** | Sim | <*nome da conta de integração*> | Nome da sua conta de integração |
   | **URL SAS da Conta de Integração** | Sim | <*integração-conta-SAS-URL*> | A URL sas (SAS) da sua conta de integração, que você pode gerar a partir das configurações da sua conta de integração no portal Azure. <p>1. No menu da conta de integração, em **Configurações,** selecione **URL de retorno de chamada**. <br>2. No painel à direita, copie o valor **da URL de retorno de chamada gerada.** |
   | **Servidor** | Sim | <*Nome do servidor TN3270*> | O nome do servidor para o seu serviço TN3270 |
   | **Porta** | Não | <*TN3270-servidor-porta*> | A porta usada pelo seu servidor TN3270. Se deixado em branco, `23` o conector usará como valor padrão. |
   | **Tipo de Dispositivo** | Não | <*Modelo de terminal IBM*> | O nome ou número do modelo para o terminal IBM emular. Se deixado em branco, o conector usa valores padrão. |
   | **Página de Código** | Não | <*número de página de código*> | O número da página de código para o host. Se deixado em branco, `37` o conector usará como valor padrão. |
   | **Nome da unidade lógica** | Não | <*nome de unidade lógica*> | O nome da unidade lógica específica para solicitar do host |
   | **Habilitar SSL?** | Não | Ligado ou desligado | Ligue ou desligue a criptografia TLS. |
   | **Validar o certificado host ssl?** | Não | Ligado ou desligado | Ligue ou desligue a validação do certificado do servidor. |
   ||||

   Por exemplo: 

   ![Propriedades da conexão](./media/connectors-create-api-3270/connection-properties.png)

1. Fornecer as informações necessárias para a ação:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome Hidx** | Sim | <*Nome do arquivo HIDX*> | Selecione o arquivo HIDX 3270 que você deseja usar. |
   | **Nome do método** | Sim | <*nome do método*> | Selecione o método no arquivo HIDX que você deseja usar. Depois de selecionar um método, a lista **Adicionar novos parâmetros** é exibida para que você possa selecionar parâmetros para usar com esse método. |
   ||||

   Por exemplo: 

   **Selecione o arquivo HIDX**

   ![Selecione o arquivo HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Selecione o método**

   ![Seleleta método](./media/connectors-create-api-3270/select-method.png)

   **Selecione os parâmetros**

   ![Selecionar parâmetros](./media/connectors-create-api-3270/add-parameters.png)

1. Quando terminar, salve e execute seu aplicativo lógico.

   Depois que seu aplicativo lógico termina de ser executado, os passos da execução aparecem. 
   Passos bem-sucedidos mostram marcas de verificação, enquanto etapas mal sucedidas mostram a letra "X".

1. Para revisar as entradas e saídas de cada etapa, expanda essa etapa.

1. Para rever as saídas, escolha **Ver saídas brutas**.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/si3270/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
