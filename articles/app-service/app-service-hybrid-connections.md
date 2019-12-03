---
title: Conexões Híbridas
description: Saiba como criar e usar conexões híbridas no serviço de Azure App para acessar recursos em redes diferentes.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: ffc5ee32541cfbbda2ae54fd229c1436f133d730
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671521"
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões Híbridas do Serviço de Aplicativo do Azure #

Conexões Híbridas é um serviço no Azure e uma funcionalidade no Serviço de Aplicativo do Azure. Como um serviço, ele tem uso e recursos além daqueles usados no Serviço de Aplicativo do Azure. Para saber mais sobre Conexões Híbridas e seu uso fora do serviço de aplicativo, confira [conexões híbridas de retransmissão do Azure][HCService].

Com o Serviço de Aplicativo, as Conexões Híbridas podem ser usadas para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Ele não permite que uma funcionalidade alternativa acesse seu aplicativo. Conforme usado no Serviço de Aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e de porta TCP. Isso significa que o ponto de extremidade de Conexões Híbridas pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não sabe e nem se importa com o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.  


## <a name="how-it-works"></a>Como funciona ##
O recurso Conexões Híbridas consiste em duas chamadas de saída para a Retransmissão do Barramento de Serviço do Azure. Há uma conexão de uma biblioteca no host em que seu aplicativo está em execução no Serviço de Aplicativo. Também há uma conexão do HCM (Gerenciador de Conexões Híbridas) para a Retransmissão do Barramento de Serviço. O HCM e um serviço de retransmissão que você implanta dentro da rede que hospeda o recurso que você está tentando acessar. 

Por meio de duas conexões ingressadas, seu aplicativo tem um túnel TCP para uma combinação fixa de host:porta no outro lado do HCM. A conexão usa o protocolo TLS 1.2 para segurança e as chaves de SAS (Assinatura de Acesso Compartilhado) para autenticação e autorização.    

![Diagrama de fluxo de alto nível de Conexão híbrida][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de Conexão Híbrida configurado, o tráfego TCP de saída será redirecionado por meio da Conexão Híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua Conexão Híbrida. Se em vez disso o ponto de extremidade usar um endereço IP, alguns softwares cliente não farão uma pesquisa de DNS. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da Conexão Híbrida do Serviço de Aplicativo ###

Há inúmeros benefícios para a funcionalidade de Conexões Híbridas, incluindo:

- Os aplicativos podem acessar sistemas e serviços locais com segurança.
- O recurso não requer um ponto de extremidade acessível pela Internet.
- É rápido e fácil de configurar. 
- Cada Conexão Híbrida corresponde a uma única combinação de host:porta, que é útil para segurança.
- Ele geralmente não exigem aberturas do firewall. As conexões são todas de saídas por meio de portas padrão da Web.
- Como o recurso está no nível de rede, ele independente da linguagem usada pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade.
- Ele pode ser usado para fornecer acesso em várias redes de um único aplicativo. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com as Conexões Híbridas ###

Coisas que você não pode fazer com conexões híbridas incluem:

- Monte uma unidade.
- Use UDP.
- Acesse serviços baseados em TCP que usam portas dinâmicas, como o Modo Passivo FTP ou o Modo Passivo Estendido.
- Suporte LDAP, porque pode exigir UDP.
- Suporte ao Active Directory, porque você não pode ingressar no domínio de um funcionário do Serviço de Aplicativo.

### <a name="prerequisites"></a>Pré-requisitos ###
 - O serviço de aplicativo do Windows é necessário. Ele só está disponível no Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar Conexões Híbridas em seu aplicativo ##

Para criar uma conexão híbrida, vá para a [portal do Azure][portal] e selecione seu aplicativo. Selecione **Rede** > **Configurar seus pontos de extremidade de Conexão Híbrida**. Aqui você pode ver as conexões híbridas que são configuradas para seu aplicativo.  

![Captura de tela da lista de Conexões Híbridas][2]

Para adicionar uma nova conexão híbrida, selecione **[+] Adicionar conexão híbrida**.  Você verá uma lista das conexões híbridas que você já criou. Para adicionar uma ou mais delas ao seu aplicativo, selecione as que você deseja e selecione **Adicionar Conexão Híbrida selecionada**.  

![Captura de tela do portal de Conexões Híbridas][3]

Se desejar criar uma nova Conexão Híbrida, clique em **Criar nova conexão híbrida**. Especifique: 

- Nome da Conexão híbrida.
- Nome do host do ponto de extremidade.
- Porta do ponto de extremidade.
- O namespace do Barramento de Serviço que você deseja usar.

![Captura de tela da caixa de diálogo Criar nova conexão híbrida][4]

Cada Conexão Híbrida está associada a um namespace do Barramento de Serviço e cada namespace do Barramento de Serviço está em uma região do Azure. É importante tentar usar um namespace do Barramento de Serviço na mesma região do seu aplicativo para evitar a latência de rede induzida.

Se você desejar remover sua Conexão Híbrida do seu aplicativo, clique com o botão direito do mouse nele e selecione **Desconectar**.  

Depois que uma Conexão Híbrida é adicionada ao seu aplicativo, basta clicar nela para ver seus detalhes. 

![Captura de tela dos detalhes das Conexões Híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma Conexão Híbrida no portal da Retransmissão do Azure ###

Além da experiência do portal de dentro do seu aplicativo, você também pode criar Conexões Híbridas no portal da Retransmissão do Azure. Para uma Conexão Híbrida ser usada pelo Serviço de Aplicativo, ele deverá:

* Exigir autorização do cliente.
* Ter um item de metadados denominado ponto de extremidade que contém a combinação de host:porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e Planos do Serviço de Aplicativo ##

As conexões híbridas do serviço de aplicativos estão disponíveis apenas nos SKUs de preços Básico, Padrão, Premium e Isolado. Há limites vinculados ao plano de preços.  

| Plano de preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

A UI do plano de serviço do aplicativo mostra quantas conexões híbridas estão sendo usadas e por quais aplicativos.  

![Captura de tela das propriedades do Plano do Serviço de Aplicativo][6]

Selecione a Conexão Híbrida para ver os detalhes. Você pode ver todas as informações exibidas no modo de exibição do aplicativo. Você também pode ver quantos outros aplicativos no mesmo plano estão usando essa Conexão Híbrida.

Há um limite no número de pontos de extremidade de Conexão Híbrida que pode ser usado em um Plano do Serviço de Aplicativo. Cada Conexão Híbrida usada, no entanto, pode ser usado em qualquer quantidade de aplicativos no plano. Por exemplo, uma única Conexão Híbrida usada em cinco aplicativos separados em um Plano do Serviço de Aplicativo conta como uma Conexão Híbrida.

### <a name="pricing"></a>Preços ###

Além de haver um requisito de SKU do plano de serviço de aplicativo, há um custo adicional para o uso de conexões híbridas. Há uma taxa para cada ouvinte usado por uma conexão híbrida. O ouvinte é o Gerenciador de Conexão híbrida. Se você tivesse cinco conexões híbridas suportadas por dois gerentes de conexão híbridos, seriam 10 ouvintes. Para obter mais informações, consulte [Preços de Barramento de Serviço][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexão Híbrida ##

O recurso Conexões Híbridas exige um agente de retransmissão na rede que hospeda o seu ponto de extremidade de Conexão Híbrida. Esse agente de retransmissão é chamado de HCM (Gerenciador de Conexão Híbrida). Para baixar a HCM, em seu aplicativo na [portal do Azure][portal], selecione **rede** > **configurar seus pontos de extremidade de conexão híbrida**.  

Essa ferramenta é executada no Windows Server 2012 e versões posteriores. O HCM é executado como um serviço e conecta-se de saída para retransmissão do Azure na porta 443.  

Depois de instalar o HCM, você pode executar HybridConnectionManagerUi.exe para usar a interface do usuário da ferramenta. Esse arquivo está no diretório de instalação do Gerenciador de Conexões Híbridas. No Windows 10, você também pode procurar por *Interface do usuário do Gerenciador de Conexões Híbridas* na sua caixa de pesquisa.  

![Captura de tela do Gerenciador de Conexões Híbridas][7]

Ao iniciar a interface do usuário do HCM, a primeira coisa que você vê é uma tabela que lista todas as Conexões Híbridas configuradas com esta instância do HCM. Se você quiser fazer alterações, faça autenticação no Azure primeiro. 

Para adicionar uma ou mais Conexões Híbridas a seu HCM:

1. Inicie a interface do usuário do HCM.
2. Selecione **Configurar outra Conexão Híbrida**.
![Captura de tela de Configurar novas Conexões Híbridas][8]

1. Entre com sua conta do Azure para colocar seu Conexões Híbridas disponível com suas assinaturas. A HCM não continua a usar sua conta do Azure além disso. 
1. Escolha uma assinatura.
1. Selecione as Conexões Híbridas que você deseja que o HCM retransmita.
![Captura de tela de Conexões Híbridas][9]

1. Clique em **Salvar**.

Agora você pode ver as Conexões Híbridas adicionadas. Você também pode selecionar a Conexão híbrida configurada para ver os detalhes.

![Captura de tela de detalhes de Conexão Híbrida][10]

Para dar suporte às Conexões Híbridas com que ele está configurado, o HCM requer:

- Acesso TCP ao Azure pela porta 443.
- Acesso TCP ao ponto de extremidade da Conexão Híbrida.
- A capacidade de fazer pesquisas de DNS no host do ponto de extremidade e no namespace do Barramento de Serviço.

> [!NOTE]
> A Retransmissão do Azure depende de soquetes da Web para ter conectividade. Essa funcionalidade só está disponível no Windows Server 2012 ou versão posterior. Por isso, o HCM não tem suporte em nada anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode ser compatível com várias Conexões Híbridas. Além disso, qualquer Conexão Híbrida determinada pode ser compatível com vários HCMs. O comportamento padrão é direcionar o tráfego pelos HCMs configurados para qualquer ponto de extremidade. Se desejar alta disponibilidade nas Conexões Híbridas da sua rede, execute vários HCMs em computadores separados. O algoritmo de distribuição de carga usado pelo serviço de retransmissão para distribuir o tráfego para os HCMs é uma atribuição aleatória. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar uma Conexão Híbrida manualmente ###

Para permitir que alguém fora da sua assinatura hospede uma instância HCM para uma determinada Conexão Híbrida, compartilhe a cadeia de conexão de gateway para a Conexão Híbrida. Você pode ver a cadeia de conexão do gateway nas propriedades de conexão híbrida no [portal do Azure][portal]. Para usar essa cadeia de caracteres, selecione **Inserir Manualmente** no HCM e cole-o na cadeia de conexão de gateway.

![Adicionar uma Conexão Híbrida manualmente][11]

### <a name="upgrade"></a>Atualize ###

Há atualizações periódicas no Gerenciador de Conexão Híbrida para corrigir problemas ou fornecer melhorias. Quando as atualizações são lançadas, um pop-up aparecerá na interface do usuário do HCM. A aplicação da atualização aplicará as mudanças e reiniciará o HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Adicionando uma conexão híbrida ao seu aplicativo programaticamente ##

As APIs mencionadas abaixo podem ser usadas diretamente para gerenciar as Conexões Híbridas conectadas aos seus aplicativos. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

O objeto JSON associado a uma conexão híbrida é semelhante a:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Uma maneira de usar essas informações é com o armclient, que pode ser obtido do projeto GitHub do [armclient][armclient] . Aqui está um exemplo sobre como anexar uma conexão híbrida pré-existente ao seu aplicativo. Crie um arquivo JSON de acordo com o esquema acima, como:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Para usar essa API, você precisa da chave de envio e do ID do recurso de retransmissão. Se você salvou suas informações com o nome de arquivo hctest.json, emita este comando para anexar sua conexão híbrida ao seu aplicativo: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Proteger seu Conexões Híbridas ##

Uma conexão híbrida existente pode ser adicionada a outros aplicativos Web do serviço de aplicativo por qualquer usuário que tenha permissões suficientes na retransmissão do barramento de serviço do Azure subjacente. Isso significa que, se você precisar impedir que outras pessoas reutilizem essa mesma conexão híbrida (por exemplo, quando o recurso de destino é um serviço que não tem nenhuma medida de segurança adicional em vigor para impedir o acesso não autorizado), você deve bloquear o acesso ao Azure Retransmissão do barramento de serviço.

Qualquer pessoa com acesso `Reader` à retransmissão poderá _Ver_ a conexão híbrida ao tentar adicioná-la ao aplicativo Web no portal do Azure, mas não poderá _adicioná_ -la, pois ela não tem as permissões para recuperar a cadeia de conexão que é usada para estabelecer a conexão de retransmissão. Para adicionar a conexão híbrida com êxito, elas devem ter a permissão `listKeys` (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). A função `Contributor` ou qualquer outra função que inclua essa permissão na retransmissão permitirá que os usuários usem a conexão híbrida e o adicionem aos seus próprios aplicativos Web.

## <a name="troubleshooting"></a>Solução de Problemas ##

O status “Conectado” significa que pelo menos um HCM está configurado com essa Conexão Híbrida e é capaz de alcançar o Azure. Se o status da sua Conexão Híbrida não indica **Conectado**, sua Conexão Híbrida não está configurada em nenhum HCM com acesso ao Azure.

O principal motivo pelo qual os clientes não podem se conectar ao ponto de extremidade é porque o ponto de extremidade foi especificado usando um endereço IP em vez de um nome DNS. Se seu aplicativo não puder acessar o ponto de extremidade desejado e você tiver usado um endereço IP, mude e passe a usar um nome DNS válido no host em que o HCM está em execução. Verifique também se o nome DNS resolve corretamente no host onde o HCM está em execução. Confirme se há conectividade entre o host onde a HCM está em execução no ponto de extremidade de Conexão Híbrida.  

No serviço de aplicativo, a ferramenta de linha de comando **tcpping** pode ser invocada no console de ferramentas avançadas (kudu). Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas ela não diz se você tem acesso a um ponto de extremidade de Conexão Híbrida. Quando você usar a ferramenta no console em relação a um ponto de extremidade de Conexão Híbrida, apenas confirmará que ele usa uma combinação host:porta.  

Se você tiver um cliente de linha de comando para seu ponto de extremidade, poderá testar a conectividade no console do aplicativo. Por exemplo, você pode testar o acesso a pontos de extremidade do servidor Web usando a ondulação.

## <a name="biztalk-hybrid-connections"></a>Conexões híbridas do BizTalk ##

A forma inicial desse recurso era chamada de conexões híbridas do BizTalk. Esta capacidade entrou em Final de Vida em 31 de maio de 2018 e encerrou as operações. As conexões híbridas do BizTalk foram removidas de todos os aplicativos e não estão acessíveis por meio do portal ou da API. Se você ainda tiver essas conexões antigas configuradas no Gerenciador de conexões híbridas, verá o status Descontinuado e exibirá uma declaração de fim de vida útil na parte inferior.

![Conexões híbridas do BizTalk no HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
