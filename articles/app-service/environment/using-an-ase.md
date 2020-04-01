---
title: Use e gerencie um ambiente de serviço de aplicativos
description: Saiba como criar, publicar e escalar aplicativos em um ambiente de serviço de aplicativos. Encontre todas as tarefas comuns neste artigo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8a73c1998203a8696b67a5e7eb3af23898239265
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477626"
---
# <a name="use-an-app-service-environment"></a>Usar um Ambiente do Serviço de Aplicativo

Um App Service Environment (ASE) é uma implantação do Azure App Service em uma sub-rede na instância da Rede Virtual Azure do cliente. Um ASE consiste em:

- **Extremidades frontais**: Quando HTTP ou HTTPS termina em um ambiente de serviço de aplicativo.
- **Trabalhadores**: Os recursos que hospedam seus aplicativos.
- **Banco de Dados**: Possui informações que definem o ambiente.
- **Armazenamento**: Usado para hospedar os aplicativos publicados pelo cliente.

Você pode implantar um ASE com um IP virtual externo ou interno (VIP) para acesso ao aplicativo. Uma implantação com um VIP externo é comumente chamada *de ASE externa*. Uma implantação com um VIP interno é chamada de *ILB ASE* porque usa um balanceador de carga interno (ILB). Para saber mais sobre o ASE ILB, veja [Criar e usar um ASE ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar um aplicativo em um ASE

Para criar um aplicativo em um ASE, você usa o mesmo processo de quando você normalmente cria um aplicativo, mas com algumas pequenas diferenças. Quando você cria um novo plano do Serviço de Aplicativos:

- Em vez de escolher uma localização geográfica na qual implantar o aplicativo, escolha um ASE como a localização.
- Todos os planos de Serviço de Aplicativo criados em um ASE só podem estar em um nível de preços isolado.

Se você não tiver um ASE, você pode criar um seguindo as instruções em [Criar um ambiente de serviço de aplicativo][MakeExternalASE].

Para criar um aplicativo em um ASE:

1. Selecione **Criar um aplicativo** > Web + Web**móvel** > **de recursos**.

1. Insira um nome do aplicativo. Se você já selecionou um plano de Serviço de Aplicativo em um ASE, o nome de domínio do aplicativo reflete o nome de domínio da ASE:

    ![Seleção de nome de aplicativo][1]

1. Selecione uma assinatura.

1. Insira um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.

1. Selecione seu SO.

1. Selecione um plano do Serviço de Aplicativo existente no ASE ou crie um novo com as seguintes etapas:

    a. No menu do lado esquerdo do portal Azure, **selecione Criar um recurso > Web App**.

    b. Selecione a assinatura.

    c. Selecione ou crie o grupo de recursos.

    d. Digite o nome do seu aplicativo web.

    e. Selecione **Código** ou **DockerContainer**.

    f. Selecione uma pilha de tempo de execução.

    g. Selecione **Linux** ou **Windows**. 

    h. Selecione seu ASE na lista de parada da **Região.** 

    i. Selecione ou crie um novo plano de serviço de aplicativo. Se criar um novo plano de serviço de aplicativo, selecione o tamanho **Isolado** SKU apropriado.

    ![Tipos de preço Isolados][2]

    > [!NOTE]
    > Aplicativos Linux e aplicativos Windows não podem estar no mesmo plano de Serviço de Aplicativo, mas eles podem estar no mesmo Ambiente de Serviço de Aplicativo.
    >

1. Selecione **'Revisar + criar',** certifique-se de que as informações estão corretas e, em seguida, selecione **Criar**.

## <a name="how-scale-works"></a>Como funciona a escala

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. Os Ambientes do Serviço de Aplicativo contêm os planos do Serviço de Aplicativo; que por sua vez contêm os aplicativos. Quando você escala um aplicativo, você também dimensiona o plano App Service e todos os aplicativos nesse mesmo plano.

Quando você dimensiona um plano de Serviço de Aplicativo, a infra-estrutura necessária é adicionada automaticamente. Há um atraso de tempo para dimensionar as operações enquanto a infra-estrutura está sendo adicionada. Se você fizer várias operações de escala em seqüência, a primeira solicitação de escala de infra-estrutura será acionada e as outras estão na fila. Quando a primeira operação de escala termina, as outras solicitações de infra-estrutura operam todas juntas. E quando a infra-estrutura é adicionada, os planos do App Service são atribuídos conforme apropriado. Criar um novo plano de Serviço de Aplicativo é em si uma operação de escala porque solicita hardware adicional.

No Serviço de Aplicativos multilocatários, o dimensionamento é imediato porque um pool de recursos está prontamente disponível para apoiá-lo. Em uma ASE, não há tal buffer, e os recursos são alocados com base na necessidade.

Em um ASE, você pode escalar um plano de Serviço de Aplicativo até 100 instâncias. Um ASE pode ter até 201 instâncias totais em todos os planos do App Service nesse ASE.

## <a name="ip-addresses"></a>Endereços IP

O App Service pode alocar um endereço IP dedicado a um aplicativo. Esse recurso está disponível depois de configurar o SSL baseado em IP, conforme descrito no [Vincular um certificado TLS/SSL personalizado existente ao Azure App Service][ConfigureSSL]. Em um ASE ILB, você não pode adicionar mais endereços IP para serem usados para SSL baseado em IP.

Com um ASE externo, você pode configurar o SSL baseado em IP para o seu aplicativo da mesma forma que no Serviço de Aplicativos multilocatário. Há sempre um endereço sobressalente no ASE, até 30 endereços IP. Cada vez que você usa um, outro é adicionado para que um endereço esteja sempre prontamente disponível. Um atraso de tempo é necessário para alocar outro endereço IP. Esse atraso impede a adição de endereços IP em rápida sucessão.

## <a name="front-end-scaling"></a>Dimensionamento de front-end

Quando você dimensiona seus planos de Serviço de Aplicativo, os trabalhadores são automaticamente adicionados para apoiá-los. Cada ASE é criado com dois front-ends. As extremidades frontais são automaticamente dimensionadas a uma taxa de um front-end para cada conjunto de 15 instâncias do plano de serviço de aplicativo. Por exemplo, se você tiver três planos de Serviço de Aplicativo com cinco instâncias cada, você teria um total de 15 instâncias e três front ends. Se você escalar para um total de 30 instâncias, você tem quatro extremidades frontais. Este padrão continua à medida que você escala.

O número de extremidades frontais que são alocadas por padrão é bom para uma carga moderada. Você pode reduzir a proporção para apenas uma frente para cada cinco instâncias. Você também pode alterar o tamanho das extremidades dianteiras. Por padrão, eles são núcleo único. No portal Azure, você pode mudar seu tamanho para dois ou quatro núcleos em vez disso.

Há uma taxa para mudar a proporção ou os tamanhos front-end. Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing]. Se você quiser melhorar a capacidade de carga do seu ASE, você terá mais melhorias ao primeiro dimensionar para extremidades frontais de dois núcleos antes de ajustar a relação de escala. Alterar o tamanho do núcleo de suas extremidades frontais causará um upgrade do seu ASE e deve ser feito fora do horário comercial regular.

Os recursos do front-end são o ponto de extremidade HTTP/HTTPS do ASE. Com a configuração de front-end padrão, o uso de memória por front-end é consistentemente cerca de 60%. A principal razão para escalar suas extremidades frontais é o uso da CPU, que é impulsionado principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso ao aplicativo

Em um ASE externo, o sufixo de domínio usado para criação de aplicativos é *.&lt; asename&gt;.p.azurewebsites.net*. Se o seu ASE for chamado _de externo-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o alcançará nessas URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter informações sobre como criar um ASE externo, consulte [Criar um ambiente de serviço de aplicativo][MakeExternalASE].

Em um ILB ASE, o sufixo de domínio usado para criação de aplicativos é *.&lt; asename&gt;.appserviceenvironment.net*. Se o seu ASE for chamado _ilb-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o alcançará nessas URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter informações sobre como criar um ILB ASE, consulte [Criar e usar um ILB ASE][MakeILBASE].

A URL SCM é usada para acessar o console Kudu ou para publicar seu aplicativo usando o Web Deploy. Para obter informações sobre o console do Kudu, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

## <a name="publishing"></a>Publicação

Em um ASE, como acontece com o Serviço de Aplicativos multilocatários, você pode publicar por esses métodos:

- Implantação da Web
- FTP
- Integração contínua (IC)
- Operação de arrastar e soltar no console do Kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com um ASE externo, essas opções de publicação funcionam da mesma forma. Para saber mais, veja [Implantação no Serviço de Aplicativo do Azure][AppDeploy].

A publicação é significativamente diferente com um ILB ASE, para o qual os pontos finais de publicação estão todos disponíveis apenas através do ILB. O ILB está em um IP privado na sub-rede do ASE, na rede virtual. Se você não tiver acesso à rede ao ILB, você não poderá publicar nenhum aplicativo nesse ASE. Como observado em [Criar e usar um ILB ASE,][MakeILBASE]você deve configurar o DNS para os aplicativos do sistema. Esse requisito inclui o ponto final do SCM. Se os pontos finais não forem definidos corretamente, você não poderá publicar. Seus IDEs também devem ter acesso à rede ao ILB para publicá-lo diretamente.

Sem alterações adicionais, sistemas de CI baseados na Internet como o GitHub e o Azure DevOps não funcionam com um ILB ASE porque o ponto final da publicação não é acessível à Internet. Você pode habilitar a publicação para um ILB ASE do Azure DevOps instalando um agente de versão auto-hospedado na rede virtual que contém o ILB ASE. Alternativamente, você também pode usar um sistema CI que usa um modelo de tração, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Você pode vê-lo no perfil de publicação do aplicativo e no painel do portal do aplicativo (em **Overview** > **Essentials** e também em **Propriedades**).

## <a name="storage"></a>Armazenamento

Um ASE tem 1 TB de armazenamento para todos os aplicativos da ASE. Um plano de Serviço de Aplicativo no SKU de preços isolados tem um limite de 250 GB por padrão. Se você tiver cinco ou mais planos de Serviço de Aplicativo, tenha cuidado para não exceder o limite de 1 TB da ASE. Se você precisar de mais do que o limite de 250 GB em um plano de Serviço de Aplicativo, entre em contato com o suporte para ajustar o limite do plano de serviço do aplicativo para um máximo de 1 TB. Quando o limite do plano é ajustado, ainda há um limite de 1 TB em todos os planos de Serviço de Aplicativo na ASE.

## <a name="logging"></a>Registro em log

Você pode integrar seu ASE com o Azure Monitor para enviar logs sobre o ASE para o Azure Storage, Azure Event Hubs ou Log Analytics. Esses itens estão registrados hoje:

| Situação | Mensagem |
|---------|----------|
| AASE não é saudável | O ASE especificado não é saudável devido a uma configuração de rede virtual inválida. A ASE será suspensa se o estado insalubre continuar. Certifique-se de que https://docs.microsoft.com/azure/app-service/environment/network-infoas diretrizes aqui definidas são seguidas: . |
| A sub-rede ASE está quase fora do espaço | O ASE especificado está em uma sub-rede que está quase fora do espaço. Há {0} endereços restantes. Uma vez esgotados esses endereços, a ASE não será capaz de dimensionar.  |
| A ASE está se aproximando do limite total de instâncias | A ASE especificada está se aproximando do limite total de instância da ASE. Atualmente, {0} ele contém instâncias do Plano de Serviço de Aplicativo de um máximo de 201 instâncias. |
| A ASE é incapaz de alcançar uma dependência | O ASE especificado não {0}é capaz de alcançar .  Certifique-se de que https://docs.microsoft.com/azure/app-service/environment/network-infoas diretrizes aqui definidas são seguidas: . |
| ASE está suspensa | A ASE especificada está suspensa. A suspensão da ASE pode ser devido a um déficit de conta ou a uma configuração de rede virtual inválida. Resolva a causa básica e retome a ASE para continuar servindo o tráfego. |
| A atualização do ASE foi iniciada | Uma atualização da plataforma para o ASE especificado foi iniciada. Espere atrasos nas operações de dimensionamento. |
| Atualização ASE foi concluída | Uma atualização da plataforma para o ASE especificado foi concluída. |
| As operações de escala começaram | Um plano de{0}Serviço de Aplicativo () começou a ser dimensionado. Estado desejado: {1} {2} Eu trabalhadores.
| As operações de escala foram concluídas | Um plano de{0}Serviço de Aplicativo () terminou de escalar. Estado {1} atual:{2} Eu trabalhadores. |
| As operações de escala falharam | Um plano de{0}Serviço de Aplicativo () falhou em dimensionar. Estado {1} atual:{2} Eu trabalhadores. |

Para habilitar o login em seu ASE:

1. No portal, vá para **configurações de Diagnóstico**.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a integração de log.
1. Selecione e configure os destinos de log que você deseja.
1. Selecione **AppServiceEnvironmentLoglogs**.

![Configurações do registro de diagnóstico ASE][4]

Se você se integrar ao Log Analytics, poderá ver os logs selecionando **Logs** do portal ASE e criando uma consulta contra **appServiceEnvironmentPlatformLogs**.

## <a name="upgrade-preference"></a>Preferência de upgrade

Se você tiver vários ASEs, você pode querer que alguns ASEs sejam atualizados antes de outros. No objeto ASE **HostingEnvironment Resource Manager,** você pode definir um valor para **upgradePreference**. A configuração **upgradePreference** pode ser configurada usando https://resources.azure.comum modelo, ARMClient ou . Os três valores possíveis são:

- **Nenhum**: O Azure atualizará seu ASE em nenhum lote específico. Esse valor é o padrão.
- **Início**: Seu ASE será atualizado na primeira metade das atualizações do App Service.
- **Atrasado**: Seu ASE será atualizado na segunda metade das atualizações do App Service.

Se estiver usando, https://resources.azure.comsiga estas etapas para definir o valor de **upgradePreferências:**

1. Vá até resources.azure.com e faça login com sua conta no Azure.
1. Passe pelos recursos para\/\[assinaturas\]\/de\/\[nome\]\/de\/assinatura\/nome de\/\[recursosGrupos\]de nomes de grupos de recursos Microsoft.Web hostingEnvironments nome ASE .
1. Selecione **Ler/Escrever** na parte superior.
1. Selecione **Editar**.
1. Definir **upgradePreferência** para qualquer um dos três valores que você deseja.
1. Selecione **Patch**.

![recursos azure com exibição][5]

O recurso **upgradePreferences** faz mais sentido quando você tem várias ASEs porque seus ASEs "Early" serão atualizados antes de suas ASEs "Atrasadas". Quando você tem vários ASEs, você deve definir seu desenvolvimento e testar ASEs como "Early" e seus ASEs de produção como "Atrasados".

## <a name="pricing"></a>Preços

O sku de preços chamado *Isolado* é para uso apenas com ASEs. Todos os planos de Serviço de Aplicativo hospedados no ASE estão no SKU de preços isolados. Taxas isoladas para planos de Serviço de Aplicativo podem variar de acordo com a região.

Além do preço dos seus planos de Serviço de Aplicativo, há uma taxa fixa para a própria ASE. A taxa fixa não muda com o tamanho do seu ASE. Ele paga pela infra-estrutura ASE a uma taxa de escala padrão de um front end adicional para cada 15 instâncias do plano de serviço de aplicativo.

Se a taxa de escala padrão de um front-end para cada 15 instâncias do plano de serviço de aplicativo não for rápida o suficiente, você poderá ajustar a razão em que as extremidades frontais são adicionadas ou o tamanho das extremidades frontais. Quando você ajustar a taxa ou o tamanho, pagará pelos núcleos de front-end que não seriam adicionados por padrão.

Por exemplo, se você ajustar a taxa de escala para 10, um front-end será adicionado para cada 10 instâncias nos planos do Serviço de Aplicativo. O valor fixo abrange uma taxa de escala de um front-end para cada 15 instâncias. Com uma taxa escala de 10, você pode pagar uma taxa para o terceiro front-end adicionado para as 10 instâncias de plano de Serviço de Aplicativo. Você não precisará pagar por ele quando você chegar a 15 instâncias porque ele terá sido adicionado automaticamente.

Se você ajustar o tamanho das extremidades dianteiras para dois núcleos, mas não ajustar a proporção, você paga pelos núcleos extras. Um ASE é criado com duas extremidades frontais, então mesmo abaixo do limite automático de dimensionamento você pagaria por dois núcleos extras se você aumentasse o tamanho para extremidades frontais de dois núcleos.

Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing].

## <a name="delete-an-ase"></a>Excluir um ASE

Para excluir um ASE:

1. Selecione **Excluir** na parte superior do painel Ambiente de Serviço do **Aplicativo.**

1. Insira o nome do seu ASE para confirmar que deseja excluí-lo. Quando você exclui um ASE, você também exclui todo o conteúdo dentro dele.

    ![Exclusão de ASE][3]

1. Selecione **OK**.

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
