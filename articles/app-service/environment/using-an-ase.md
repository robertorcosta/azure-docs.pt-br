---
title: Usar e gerenciar um Ambiente do Serviço de Aplicativo
description: Como criar, publicar e dimensionar aplicativos em um ambiente de serviço Azure App. Encontre as tarefas comuns em um documento.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565616"
---
# <a name="use-an-app-service-environment"></a>Usar um ambiente do Serviço de Aplicativo #

O Ambiente do Serviço de Aplicativo (ASE) é uma implantação do serviço de Azure App em uma sub-rede na rede virtual do Azure de um cliente. Um ASE consiste em:

- **Front-ends**: os front-ends são onde o http/https é encerrado em um ambiente do serviço de aplicativo.
- **Funções de Trabalho**: são os recursos que hospedam os aplicativos.
- **Banco de Dados**: contém informações que definem o ambiente.
- **Armazenamento**: o armazenamento é usado para hospedar os aplicativos publicados pelo cliente.

Você pode implantar um ASE com um VIP externo ou interno para acesso ao aplicativo. A implantação com um VIP externo é geralmente chamada de ASE externo. A versão interna é chamada de ILB ASE, porque ele usa um balanceador de carga interno (ILB). Para saber mais sobre o ASE ILB, confira [criar e usar um ase ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar um aplicativo em um ASE ##

Para criar um aplicativo em um ASE, use o mesmo processo usado ao criá-lo normalmente, mas com algumas pequenas diferenças. Quando você cria um novo plano do serviço de aplicativo (ASP):

- Em vez de escolher uma localização geográfica na qual implantar o aplicativo, escolha um ASE como a localização.
- Todos os planos do serviço de aplicativo criados em um ASE só podem estar em um tipo de preço isolado.

Se você não tiver um ASE, poderá criar um seguindo as instruções em [criar um ambiente de serviço de aplicativo][MakeExternalASE].

Para criar um aplicativo em um ASE:

1. Selecione  **Criar um recurso** >  **Web + Mobile** > **Aplicativo da Web**.

2. Insira um nome do aplicativo. Se você já selecionou um plano do Serviço de Aplicativo em um ASE, o nome de domínio do aplicativo reflete o nome de domínio do ASE.

    ![Seleção de nome de aplicativo][1]

1. Selecione uma assinatura.

1. Insira um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.

1. Selecione seu SO. 

1. Selecione um plano do Serviço de Aplicativo existente no ASE ou crie um novo com as seguintes etapas:

    a. No menu do portal do Azure esquerdo, selecione **criar um recurso > aplicativo Web**.

    b. Selecione a assinatura.
    
    c. Selecione ou crie o grupo de recursos.
    
    d. Forneça o nome do seu aplicativo Web.
    
    e. Selecione código ou DockerContainer.
    
    f. Selecione pilha de tempo de execução.
    
    g. Selecione Linux ou Windows. 
    
    h. Selecione seu ASE na lista suspensa **região** . 
    
    i. Selecione ou crie um novo plano do serviço de aplicativo. Se estiver criando um novo plano do serviço de aplicativo, selecione o tamanho de SKU **isolado** apropriado.
    
    ![Tipos de preço Isolados][2]

    > [!NOTE]
    > Os aplicativos Linux e Windows não podem estar no mesmo Plano de Serviço de Aplicativo, mas podem estar no mesmo Ambiente de Serviço de Aplicativo. 
    >

2. Selecione **revisão + criar** e, em seguida, selecione **criar** se as informações estiverem corretas.

## <a name="how-scale-works"></a>Como funciona a escala ##

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. Os Ambientes do Serviço de Aplicativo contêm os planos do Serviço de Aplicativo; que por sua vez contêm os aplicativos. Ao dimensionar um aplicativo, você dimensiona o plano do Serviço de Aplicativo e, portanto, dimensiona todos os aplicativos do mesmo plano.

Quando você dimensiona um plano do serviço de aplicativo, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo nas operações de escala, conforme a infraestrutura é adicionada. Se você fizer várias operações de escala em sequência, a primeira solicitação de escala de infraestrutura será aplicada e as outras se enfileirarão. Quando a primeira operação de escala for concluída, a outra infraestrutura solicitará todas as operações juntas. Quando a infraestrutura é adicionada, os planos do serviço de aplicativo são atribuídos conforme apropriado. A criação de um novo plano do serviço de aplicativo é, em si, uma operação de escala à medida que solicita hardware adicional. 

No serviço de aplicativo multilocatário, o dimensionamento é imediato porque um pool de recursos está prontamente disponível para dar suporte a ele. Em um ASE, não há nenhum buffer desse tipo e os recursos são alocados conforme a necessidade.

Em um ASE, você pode dimensionar um plano do serviço de aplicativo até 100 instâncias. Um ASE pode ter até 201 instâncias totais em todos os planos do serviço de aplicativo que estão no ASE. 

## <a name="ip-addresses"></a>Endereços IP ##

O Serviço de Aplicativo tem a capacidade de alocar um endereço IP dedicado a um aplicativo. A capacidade de alocar um IP dedicado a um aplicativo está disponível depois de configurar um SSL baseado em IP, conforme descrito em [associar um certificado SSL personalizado existente ao serviço de Azure app][ConfigureSSL]. Em um ASE ILB, não é possível adicionar mais endereços IP a serem usados para um SSL baseado em IP.

Com um ASE externo, você pode configurar o SSL baseado em IP para seu aplicativo da mesma maneira que no serviço de aplicativo multilocatário. Sempre há um endereço de reposição no ASE até 30 endereços IP. Sempre que você usar um, outro será adicionado, de forma que sempre haja um endereço imediatamente disponível para uso. Um atraso de tempo é necessário para alocar outro endereço IP, o que impede a adição de endereços IP em sucessão rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

Quando você escala horizontalmente seus planos do serviço de aplicativo, os trabalhadores são adicionados automaticamente para dar suporte a eles. Todo ASE é criado com dois front-ends. Os front-ends são dimensionados automaticamente a uma taxa de um front-ends para cada total de 15 instâncias do plano do serviço de aplicativo. Se você tiver três planos do serviço de aplicativo com cinco instâncias cada, você teria um total de 15 instâncias e três front-ends. Se você dimensionar para um total de 30 instâncias, terá quatro front-ends e assim por diante. 

O número de front-ends alocados por padrão é bom para uma carga moderada. Você pode alterar a taxa para o máximo de um front-ends para cada cinco instâncias. Você também pode alterar o tamanho dos front-ends. Por padrão, eles são um único núcleo. Em vez disso, você pode alterar o tamanho dos front-ends no portal para dois ou quatro tamanhos de núcleo. Há um encargo para alterar a taxa ou os tamanhos de front-ends. Para obter mais informações, consulte [Azure app preço do serviço][Pricing]. Se você estiver procurando melhorar a capacidade de carga de seu ASE, você obterá mais melhorias, primeiro redimensionando para dois front-ends principais antes de ajustar a taxa de escala. Alterar o tamanho do núcleo de seus front-ends causará uma atualização de seu ASE e deve ser feito fora do horário comercial normal.

Os recursos de front-ends são o ponto de extremidade HTTP/HTTPS para o ASE. Com a configuração de front-end padrão, o uso de memória por front-end é consistentemente em cerca de 60%. O principal motivo para dimensionar seus front-ends é a CPU, que é controlada principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso ao aplicativo ##

Em um ASE externo, o sufixo de domínio usado para a criação do aplicativo é *.&lt;asename&gt;. p.azurewebsites.net*. Se seu ASE for denominado _external-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o atingirá nas seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter mais informações sobre como criar um ASE externo, consulte [criar um ambiente do serviço de aplicativo][MakeExternalASE]

Em um ASE ILB, o sufixo de domínio usado para a criação do aplicativo é *.&lt;asename&gt;. appserviceenvironment.net*. Se seu ASE se chamar _ILB-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o atingirá nas seguintes URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter mais informações sobre como criar um ASE ILB, consulte [criar e usar um ase ILB][MakeILBASE]. 

A URL do SCM é usada para acessar o console do kudu ou para publicar seu aplicativo usando a implantação da Web. Para obter informações sobre o console do kudu, consulte [console do kudu para Azure app Service][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

## <a name="publishing"></a>Publicação ##

Assim como ocorre com o Serviço de Aplicativo multilocatário, em um ASE, é possível publicar com:

- Implantação da Web.
- FTP.
- Integração contínua.
- Operação de arrastar e soltar no console do Kudu.
- Um IDE como o Visual Studio, Eclipse ou IntelliJ IDEA.

Com um ASE externo, todas essas opções de publicação comportam-se da mesma maneira. Para obter mais informações, consulte [Deployment in Azure app Service][AppDeploy]. 

A principal diferença na publicação é em relação a um ASE ILB. Com um ASE ILB, os pontos de extremidade de publicação estão disponíveis apenas por meio do ILB. O ILB está em um IP privado na sub-rede do ASE, na rede virtual. Se você não tiver acesso à rede ao ILB, não poderá publicar nenhum aplicativo nesse ASE. Conforme observado em [criar e usar um ase ILB][MakeILBASE], você precisa configurar o DNS para os aplicativos no sistema. Isso inclui o ponto de extremidade SCM. Se eles não estiverem definidos corretamente, você não poderá publicar. Os IDEs também precisam ter acesso à rede ao ILB para publicar diretamente nele.

Os sistemas de CI baseados na Internet, como GitHub e Azure DevOps, não funcionam com um ASE ILB porque o ponto de extremidade de publicação não é acessível pela Internet. Você pode habilitar a publicação em um ASE ILB do Azure DevOps instalando um agente de liberação auto-hospedado na VNet que contém o ASE ILB. Como alternativa, você também pode usar um sistema de CI que usa um modelo de pull, como o dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Você pode ver isso no perfil de publicação do aplicativo e na folha do portal do aplicativo (em **Visão Geral** > **Informações Básicas** e também em **Propriedades**). 

## <a name="storage"></a>Armazenamento

Um ASE tem 1 TB de armazenamento para todos os aplicativos dentro do ASE. Um plano do serviço de aplicativo de SKU isolado tem um limite de 250 GB por padrão. Se você tiver cinco ou mais planos do serviço de aplicativo, precisará ter cuidado para não exceder o limite de 1 TB do ASE. Se você precisar de mais do que o limite de 250 GB em um plano do serviço de aplicativo, entre em contato com o suporte para ajustar o limite do plano do serviço de aplicativo para um máximo de 1 TB. Quando o limite do plano é ajustado, ainda há um limite de 1 TB em todos os planos do serviço de aplicativo no ASE. 

## <a name="logging"></a>Registro em log ##

Você pode integrar seu ASE com Azure Monitor para enviar logs sobre o ASE para armazenamento, Hub de eventos ou Log Analytics. Os itens que são registrados hoje são:

| Ocorrer | Mensagem |
|---------|----------|
| O ASE não está íntegro | O ASE especificado não está íntegro devido a uma configuração de rede virtual inválida. O ASE será suspenso se o estado não íntegro continuar. Verifique se as diretrizes definidas aqui foram seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info |
| A sub-rede do ASE está quase sem espaço | O ASE especificado está em uma sub-rede que está quase sem espaço. Há {0} endereços restantes. Depois que esses endereços forem esgotados, o ASE não será capaz de dimensionar  |
| O ASE está se aproximando do limite de instância total | O ASE especificado está se aproximando do limite de instância total do ASE. Atualmente, ele contém {0} instâncias do plano do serviço de aplicativo de um máximo de 201 instâncias. |
| O ASE não consegue alcançar uma dependência | O ASE especificado não é capaz de alcançar {0}.  Verifique se as diretrizes definidas aqui foram seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE suspenso | O ASE especificado está suspenso. A suspensão do ASE pode ser devido a uma deficiência de conta ou uma configuração de rede virtual inválida. Resolva a causa raiz e retome o ASE para continuar a fornecer tráfego |
| A atualização do ASE foi iniciada | Uma atualização de plataforma para o ASE especificado foi iniciada. Espera de atrasos em operações de dimensionamento |
| A atualização do ASE foi concluída | Uma atualização de plataforma para o ASE especificado foi concluída |
| Operações de escala iniciadas | Um plano do serviço de aplicativo ({0}) começou a ser dimensionado. Estado desejado: {1} eu{2} trabalhadores 
| Operações de escala concluídas | Um plano do serviço de aplicativo ({0}) concluiu o dimensionamento. Estado atual: {1} I{2} trabalhadores |
| Falha nas operações de escala | Um plano do serviço de aplicativo ({0}) falhou ao ser dimensionado. Estado atual: {1} I{2} trabalhadores |

Para habilitar o registro em log em seu ASE: 

1. Vá para configurações de diagnóstico no Portal.  
1. Selecione Adicionar configuração de diagnóstico.
1. Forneça um nome para a integração de log
1. Verifique e configure os destinos de log desejados. 
1. Verificar AppServiceEnvironmentPlatformLogs

![Configurações do log de diagnóstico do ASE][4]

Se você se integrar com o Log Analytics, poderá ver os logs selecionando logs no portal do ASE e criando uma consulta em relação ao AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Preferência de atualização ##

Se você tiver vários ASEs, talvez você queira fazer com que alguns ASEs sejam atualizados antes de outros. No objeto do Gerenciador de recursos de HostingEnvironment do ASE, você pode definir um valor para UpgradePreference. A configuração upgradePreference pode ser configurada por meio de template, ARMClient ou https://resources.azure.com.  As três opções de valor são:

* Nenhum-nenhum é o padrão e significa que o Azure atualizará seu ASE em um lote específico
* Antecipadamente, o seu ASE será atualizado na primeira metade das atualizações do serviço de aplicativo
* Tardiamente significa que seu ASE será atualizado na segunda metade das atualizações do serviço de aplicativo

Se você estiver usando https://resources.azure.com, poderá definir o valor de upgradePreferences por:

1. Acessando resources.azure.com e entrando com sua conta do Azure
1. Navegue pelas assinaturas\/\[nome da assinatura\]\/resourceGroups\/\[nome do grupo de recursos\]\/provedores\/Microsoft. Web\/hostingEnvironments\/\[nome do ASE\]
1. Selecionando leitura/gravação na parte superior
1. Selecione Editar
1. Altere o valor de upgradePreference para o que for desejado das três opções.
1. Selecionar patch

![recursos de exibição do Azure com][5]

O recurso upgradePreferences realmente faz mais sentido quando você tem vários ASEs como seu "início" atualizado ASEs será atualizado antes de seu "final" ASEs. Quando você tiver vários ASEs, deverá ter seu ASEs de desenvolvimento/teste definido como "antecipado" e sua ASEs de produção para ser definida como "tardia".

## <a name="pricing"></a>Preços ##

O SKU de preços chamado **isolado** é apenas para uso com o ase. Todos os planos do serviço de aplicativo hospedados no ASE estão no SKU de preços isolado. As taxas de plano do Serviço de Aplicativo Isolado podem variar por região. 

Além do preço dos planos do Serviço de Aplicativo, há um valor fixo para o ASE em si. A taxa fixa não muda com o tamanho do seu ASE e paga a infraestrutura do ASE em uma taxa de dimensionamento padrão de um front-end adicional para cada 15 instâncias do plano do serviço de aplicativo.  

Se a taxa de escala padrão de um front-ends para cada 15 instâncias do plano do serviço de aplicativo não for rápida o suficiente, você poderá ajustar a taxa na qual os front-ends são adicionados ou o tamanho dos front-ends.  Ao ajustar a taxa ou o tamanho, você paga pelos núcleos de front-ends que não seriam adicionados por padrão.  

Por exemplo, se você ajustar a taxa de escala para 10, um front-end será adicionado para cada 10 instâncias nos planos do serviço de aplicativo. A taxa fixa cobre uma taxa de escala de um front-end para cada 15 instâncias. Com uma taxa de escala de 10, você paga uma taxa para o terceiro front-end que é adicionado para as 10 instâncias do plano do serviço de aplicativo. Você não precisará pagar por ele quando você chegar a 15 instâncias porque ele terá sido adicionado automaticamente.

Se você ajustou o tamanho dos front-ends para dois núcleos, mas não ajustar a taxa, você paga pelos núcleos extras.  Um ASE é criado com dois front-ends, portanto, mesmo abaixo do limite de dimensionamento automático, você pagaria por dois núcleos extras se aumentar o tamanho para dois front-ends principais.

Para obter mais informações, consulte [Azure app preço do serviço][Pricing].

## <a name="delete-an-ase"></a>Excluir um ASE ##

Para excluir um ASE: 

1. Use **Excluir** na parte superior da folha **Ambiente de Serviço de Aplicativo**. 

1. Insira o nome do seu ASE para confirmar que deseja excluí-lo. Quando você exclui um ASE, exclui todo o conteúdo dentro dele também. 

    ![Exclusão de ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
