---
title: Introdução aos microsserviços no Azure
description: Uma visão geral sobre por que criar aplicativos de nuvem com uma abordagem de microsserviços é importante para o desenvolvimento moderno de aplicativos e como o Azure Service Fabric oferece uma plataforma para conseguir isso.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750624"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Por que usar uma abordagem de microsserviços para criar aplicativos

Para desenvolvedores de software, fatorar um aplicativo em partes componentes não é novidade. Normalmente, uma abordagem hierárquica é usada, com uma loja back-end, lógica de negócios de nível intermediário e uma interface de usuário front-end (Interface do Usuário). O que *mudou* nos últimos anos é que os desenvolvedores estão construindo aplicativos distribuídos para a nuvem.

Aqui estão algumas necessidades de negócios em mudança:

* Um serviço que é construído e operado em escala para alcançar clientes em novas regiões geográficas.
* Entrega mais rápida de recursos e recursos para responder às demandas dos clientes de forma ágil.
* Melhor utilização de recursos para reduzir os custos.

Essas necessidades comerciais estão afetando o *modo* como criamos os aplicativos.

Para obter mais informações sobre a abordagem do Azure para microsserviços, consulte [Microservices: Uma revolução de aplicativos alimentada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Abordagem monolítica vs. projeto de microsserviços

Aplicações evoluem ao longo do tempo. Os aplicativos bem-sucedidos evoluem sendo úteis às pessoas. Aplicações mal sucedidas não evoluem e são eventualmente preteridas. Eis a pergunta: o quanto você sabe sobre suas necessidades hoje e quais serão no futuro? Por exemplo, digamos que você está construindo um aplicativo de relatórios para um departamento em sua empresa. Você tem certeza de que o aplicativo se aplica apenas no âmbito da sua empresa e que os relatórios não serão mantidos por muito tempo. Sua abordagem será diferente da de, digamos, construir um serviço que ofereça conteúdo de vídeo para dezenas de milhões de clientes.

Às vezes, tirar algo da porta como prova de conceito é o fator de condução. Você sabe que o aplicativo pode ser redesenhado mais tarde. Há pouco sentido em sobreengenharia algo que nunca é usado. Por outro lado, quando as empresas constroem para a nuvem, a expectativa é de crescimento e uso. Crescimento e escala são imprevisíveis. Queremos protótipos rapidamente, ao mesmo tempo em que sabemos que estamos em um caminho que pode lidar com o sucesso futuro. Essa é a abordagem de inicialização simples: compilar, medir, aprender e iterar.

Durante a era cliente/servidor, tendemos a focar na construção de aplicativos hierárquicos usando tecnologias específicas em cada nível. O termo aplicação *monolítica* surgiu para descrever essas abordagens. As interfaces tendiam a ficar entre as camadas e um design mais rigidamente acoplado era usado entre os componentes em cada camada. Desenvolvedores projetaram e fatoraram classes que foram compiladas em bibliotecas e ligadas em alguns arquivos executáveis e DLLs.

Há benefícios para uma abordagem de design monolítico. Aplicações monolíticas são muitas vezes mais simples de projetar, e as chamadas entre componentes são mais rápidas porque essas chamadas são muitas vezes por comunicação interprocess (IPC). Além disso, todos testam um único produto, que tende a ser um uso mais eficiente dos recursos humanos. A desvantagem é que há um acoplamento apertado entre camadas hierárquicas, e você não pode escalar componentes individuais. Se você precisa fazer correções ou upgrades, você tem que esperar que outros terminem seus testes. É mais difícil ser ágil.

Os microserviços abordam essas desvantagens e estão mais alinhados com os requisitos de negócios anteriores. Mas eles também têm benefícios e passivos. Os benefícios dos micros serviços são que cada um normalmente encapsula uma funcionalidade de negócios mais simples, que você pode ampliar, testar, implantar e gerenciar de forma independente. Um benefício importante de uma abordagem de microsserviços é que as equipes são impulsionadas mais por cenários de negócios do que por tecnologia. Equipes menores desenvolvem um microserviço baseado no cenário do cliente e usam todas as tecnologias que desejam usar.

Em outras palavras, a organização não precisa padronizar a tecnologia para manter aplicativos de microsserviço. As equipes individuais que detêm serviços podem escolher o que fizer sentido para elas com base em suas competências ou no que é mais apropriado para resolver o problema. Na prática, um conjunto de tecnologias recomendadas, como uma determinada loja NoSQL ou uma estrutura de aplicação web, é preferível.

A desvantagem dos microsserviços é que você tem que gerenciar entidades mais separadas e lidar com implantações e versões mais complexas. O tráfego de rede entre os microserviços aumenta, assim como as latências de rede correspondentes. Muitos serviços tagarelas e granulares podem causar um pesadelo de desempenho. Sem ferramentas para ajudá-lo a visualizar essas dependências, é difícil ver todo o sistema.

As normas fazem com que a abordagem de microserviços funcione especificando como se comunicar e tolerando apenas as coisas que você precisa de um serviço, em vez de contratos rígidos. É importante definir esses contratos antecipadamente no projeto porque os serviços se atualizam independentemente uns dos outros. Outra descrição cunhada para desenvolver com uma abordagem de microsserviços é "SOA (arquitetura orientada a serviços) de granulação fina".

***Em sua mais simples, a abordagem de design de microsserviços é sobre uma federação deserviços dissociada, com alterações independentes em cada um dos padrões de comunicação acordados.***

À medida que mais aplicações em nuvem são produzidas, as pessoas descobriram que essa decomposição da aplicação global em serviços independentes e focados em cenários é uma abordagem melhor a longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicativos

![Desenvolvimento de aplicativos da plataforma Service Fabric][Image1]

1) Um aplicativo monolítico contém funcionalidade específica do domínio e normalmente é dividido em camadas funcionais como web, negócios e dados.

2) Você dimensiona um aplicativo monolítico clonando-o em vários servidores/máquinas virtuais/contêineres.

3) Um aplicativo de microsserviço separa a funcionalidade em serviços menores separados.

4) Essa abordagem de microsserviços pode ser dimensionada implantando cada serviço de modo independente, criando instâncias desses serviços entre servidores/máquinas virtuais/contêineres.

Projetar com uma abordagem de microsserviços não é apropriado para todos os projetos, mas se alinha mais de perto com os objetivos de negócios descritos anteriormente. Começar com uma abordagem monolítica pode fazer sentido se você sabe que terá a oportunidade de retrabalhar o código mais tarde em um projeto de microsserviços. Normalmente, você começa com um aplicativo monolítico e o desmembra em etapas, começando com as áreas funcionais que precisam ser mais escalonáveis ou ágeis.

Quando você usa uma abordagem de microserviços, você compõe sua aplicação de muitos pequenos serviços. Esses serviços são executados em contêineres que são implantados em um conjunto de máquinas. Equipes menores desenvolvem um serviço que se concentra em um cenário e testam, versão, implantam e dimensionam cada serviço de forma independente para que todo o aplicativo possa evoluir.

## <a name="what-is-a-microservice"></a>O que é um microsserviço?

Há diferentes definições de microsserviços. Mas a maioria dessas características dos microserviços são amplamente aceitas:

* Abrange um cenário de cliente ou comercial. Que problema está resolvendo?
* Desenvolvidos por uma pequena equipe de engenharia.
* Escrito em qualquer linguagem de programação, usando qualquer estrutura.
* Consistem em código e, opcionalmente, estado, ambos são versão independente, implantado e dimensionado.
* Interagem com outros microsserviços em protocolos e interfaces bem definidos.
* Tenha nomes exclusivos (URLs) que são usados para resolver sua localização.
* Permanecem consistentes e disponíveis na presença de falhas.

Resumindo:

***Os aplicativos de microsserviços são compostos por serviços pequenos e focados no cliente, com controle de versão independente e escalonáveis que se comunicam entre si por meio de protocolos padrão com interfaces bem definidas.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Escrito em qualquer linguagem de programação, usando qualquer estrutura

Como desenvolvedores, queremos ser livres para escolher um idioma ou estrutura, dependendo de nossas habilidades e das necessidades do serviço que estamos criando. Para alguns serviços, você pode valorizar os benefícios de desempenho do C++ acima de qualquer outra coisa. Para outros, a facilidade de desenvolvimento gerenciado que você recebe de C# ou Java pode ser mais importante. Em alguns casos, você pode precisar usar uma biblioteca de parceiros específica, tecnologia de armazenamento de dados ou método para expor o serviço aos clientes.

Depois de escolher uma tecnologia, você precisa considerar o gerenciamento operacional ou do ciclo de vida e o dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e o estado tenham versão, implantação e dimensionamento independentes

Não importa como você escreve seus microserviços, o código e, opcionalmente, o estado, deve implantar, atualizar e dimensionar de forma independente. Este problema é difícil de resolver porque se resume à sua escolha de tecnologias. Para dimensionamento, entender como particionar (ou fragmento) tanto o código quanto o estado é desafiador. Quando o código e o estado usam tecnologias diferentes, o que é comum hoje, os scripts de implantação do seu microserviço precisam ser capazes de dimensionar ambos. Essa separação também é sobre agilidade e flexibilidade, para que você possa atualizar alguns dos microsserviços sem ter que atualizar todos eles de uma só vez.

Vamos voltar à nossa comparação das abordagens monolíticas e microsserviços por um momento. Este diagrama mostra as diferenças nas abordagens para armazenar o estado:

#### <a name="state-storage-for-the-two-approaches"></a>Armazenamento estatal para as duas abordagens

![Armazenamento de estado da plataforma Service Fabric][Image2]

***A abordagem monolítica, à esquerda, tem um único banco de dados e níveis de tecnologias específicas.***

***A abordagem de microserviços, à direita, tem um gráfico de microserviços interconectados onde o estado é tipicamente escopo para o microserviço e várias tecnologias são usadas.***

Uma abordagem monolítica, o aplicativo geralmente usa um único banco de dados. A vantagem de usar um banco de dados é que ele está em um único local, o que facilita a implantação. Cada componente pode ter uma única tabela para armazenar seu estado. As equipes precisam separar estritamente estado, que é um desafio. Inevitavelmente, alguém será tentado a adicionar uma coluna a uma tabela de clientes existente, fazer uma associação entre as tabelas e criar dependências na camada de armazenamento. Quando isso acontecer, não será possível dimensionar os componentes individuais.

Na abordagem dos microsserviços, cada serviço gerencia e armazena seu próprio estado. Cada serviço é responsável por dimensionar o estado e o código juntos para atender às demandas do serviço. Uma desvantagem é que quando você precisa criar visualizações, ou consultas, dos dados do seu aplicativo, você precisa consultar várias lojas estaduais. Esse problema é normalmente resolvido por um microserviço separado que constrói uma visão através de uma coleção de microserviços. Se você precisar executar várias consultas improvisadas nos dados, você deve considerar escrever os dados de cada microserviço para um serviço de armazenamento de dados para análiseoff.

Microserviços são versidos. É possível que diferentes versões de um microserviço funcionem lado a lado. Uma versão mais recente de um microserviço pode falhar durante uma atualização e precisa ser revertida para uma versão anterior. A versão também é útil para testes A/B, onde diferentes usuários experimentam diferentes versões do serviço. Por exemplo, é comum atualizar um microserviço para um conjunto específico de clientes para testar novas funcionalidades antes de implementá-la mais amplamente.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices em protocolos e interfaces bem-definidos

Nos últimos 10 anos, foram publicadas informações extensivas descrevendo padrões de comunicação em arquiteturas orientadas a serviços. Em geral, a comunicação do serviço usa uma abordagem do REST com os protocolos TCP e HTTP, e XML ou JSON como formato de serialização. De uma perspectiva de interface, trata-se de tomar uma abordagem de web design. Mas nada deve impedi-lo de usar protocolos binários ou seus próprios formatos de dados. Apenas esteja ciente de que as pessoas terão mais dificuldade em usar seus microsserviços se esses protocolos e formatos não estiverem disponíveis abertamente.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo (URL) usado para determinar sua localização

O microsserviço precisa ser endereçável independentemente do local em que está sendo executado. Se você está pensando em máquinas e qual delas está executando um microserviço específico, as coisas podem dar errado rapidamente.

Da mesma forma que o DNS resolve um determinado URL para uma máquina específica, seu micros serviço precisa de um nome exclusivo para que sua localização atual seja detectável. Os microserviços precisam de nomes endereçados que sejam independentes da infra-estrutura em que estão executando. Isso implica que há uma interação entre a maneira como seu serviço é implantado e como ele é detectado, porque deve haver um Registro do serviço. Quando uma máquina falha, o serviço de registro precisa dizer para onde o serviço foi movido.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um dos problemas mais difíceis de se resolver, especialmente em um sistema distribuído. Grande parte do código que escrevemos como desenvolvedores é para lidar com exceções. Durante os testes, também gastamos mais tempo no manuseio de exceções. O processo está mais envolvido do que escrever código para lidar com falhas. O que acontece quando a máquina em que o microserviço está em execução falha? Você precisa detectar a falha, que é um problema difícil por si só. Mas você também precisa reiniciar seu microserviço.

Para disponibilidade, um microserviço precisa ser resiliente a falhas e capaz de reiniciar em outra máquina. Além desses requisitos de resiliência, os dados não devem ser perdidos e os dados precisam permanecer consistentes.

A resiliência é difícil de alcançar quando ocorrem falhas durante um upgrade de aplicativo. O microsserviço, trabalhando com o sistema de implantação, não precisa fazer a recuperação. Ele precisa determinar se ele pode continuar a avançar para a versão mais recente ou reverter para uma versão anterior para manter um estado consistente. Você precisa considerar algumas perguntas, como se há máquinas suficientes disponíveis para continuar avançando e como recuperar versões anteriores do microserviço. Para tomar essas decisões, você precisa do microserviço para emitir informações de saúde.

### <a name="reports-health-and-diagnostics"></a>Relatórios de integridade e diagnóstico

Pode parecer óbvio, e muitas vezes é negligenciado, mas um microserviço precisa relatar sua saúde e diagnósticos. Caso contrário, você tem pouca visão de sua saúde do ponto de vista das operações. Correlacionar eventos de diagnóstico em um conjunto de serviços independentes e lidar com distorções de relógio da máquina para entender a ordem dos eventos é um desafio. Da mesma forma que você interage com um micros serviço em relação a protocolos e formatos de dados acordados, é necessário padronizar como registrar eventos de integridade e diagnóstico que acabarão acabando em um armazenamento de eventos para consulta e visualização. Com uma abordagem de microsserviços, diferentes equipes precisam concordar com um único formato de registro. Precisa ser uma abordagem consistente para exibir os eventos de diagnóstico no aplicativo inteiro.

Integridade é diferente de diagnóstico. Integridade significa que o microsserviço reporta seu estado atual para tomar as devidas ações. Um bom exemplo é trabalhar com mecanismos de atualização e implantação para manter a disponibilidade. Embora um serviço possa estar atualmente insalubre por causa de uma falha de processo ou reinicialização da máquina, o serviço ainda pode estar operacional. A última coisa que você precisa é piorar a situação começando uma atualização. A melhor abordagem é investigar primeiro ou dar tempo para que o microserviço se recupere. Os eventos de integridade de um microsserviço permitem tomar decisões bem informadas e ajudam realmente a criar serviços que recuperam a si próprios.

## <a name="guidance-for-designing-microservices-on-azure"></a>Orientação para a concepção de microsserviços no Azure

Visite o centro de arquitetura Azure para obter orientações sobre [o projeto e a construção de microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como uma plataforma de microsserviço

O Azure Service Fabric surgiu quando a Microsoft passou de entregar produtos em caixa, que eram tipicamente monolíticos, para fornecer serviços. A experiência de construir e operar grandes serviços, como o Azure SQL Database e o Azure Cosmos DB, moldou o Service Fabric. A plataforma evoluiu ao longo do tempo à medida que mais serviços a adotaram. O Service Fabric tinha que ser executado não apenas no Azure, mas também em implantações autônomas do Windows Server.

***O objetivo da Service Fabric é resolver os problemas difíceis de construir e executar um serviço e usar os recursos de infra-estrutura de forma eficiente, para que as equipes possam resolver problemas de negócios usando uma abordagem de microsserviços.***

O Service Fabric ajuda você a criar aplicativos que usam uma abordagem de micros serviços, fornecendo:

* Uma plataforma que fornece serviços do sistema para implantar, atualizar, detectar e reiniciar os serviços com falha, descobrir serviços, encaminhar mensagens, gerenciar o estado e monitorar a integridade.
* A capacidade de implantar aplicativos em execução em contêineres ou como processos. O Service Fabric é um orquestrador de contêiner e processo.
* APIs de programação produtiva para ajudá-lo a construir aplicativos como microsserviços: [ASP.NET Core, Atores Confiáveis e Serviços Confiáveis](service-fabric-choose-framework.md). Por exemplo, você pode obter informações de integridade e diagnóstico, ou você pode tirar proveito da alta disponibilidade interna.

***Service Fabric é agnóstico sobre como você constrói seu serviço, e você pode usar qualquer tecnologia. Mas fornece APIs de programação incorporadas que facilitam a construção de microsserviços.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrando aplicativos existentes para o Service Fabric

Service Fabric permite que você reutilize o código existente e o modernize com novos microserviços. Existem cinco etapas para a modernização da aplicação, e você pode começar e parar em qualquer estágio. As etapas são:

1) Comece com um aplicativo monolítico tradicional.  
2) Migrar. Use contêineres ou executáveis de hóspedes para hospedar o código existente no Service Fabric.  
3) Modernizar. Adicione novos microserviços ao lado do código contêiner existente.  
4) Inovar. Quebre a aplicação monolítica em microserviços com base na necessidade.  
5) Transforme aplicativos em microsserviços. Transforme aplicações monolíticas existentes ou crie novas aplicações greenfield.

![Migração para microsserviços][Image3]

Lembre-se, você pode *começar e parar em qualquer um desses estágios.* Você não tem que progredir para a próxima etapa. 

Vejamos exemplos para cada uma dessas etapas.

**Migrar**  
Por duas razões, muitas empresas estão migrando aplicações monolíticas existentes em contêineres:

* Redução de custos, seja devido à consolidação e remoção de hardware existente ou devido à execução de aplicativos em maior densidade.
* Um contrato de implantação consistente entre desenvolvimento e operações.

Reduções de custos são simples. Na Microsoft, muitos aplicativos existentes estão sendo contêineres, levando a milhões de dólares em economias. A implantação consistente é mais difícil de avaliar, mas igualmente importante. Isso significa que os desenvolvedores podem escolher as tecnologias que lhes convém, mas as operações aceitarão apenas um único método para implantar e gerenciar os aplicativos. Ele alivia as operações de ter que lidar com a complexidade de suportar diferentes tecnologias sem forçar os desenvolvedores a escolher apenas algumas. Essencialmente, cada aplicativo é contêiner em imagens de implantação independentes.

Muitas organizações param aqui. Eles já têm os benefícios dos contêineres, e a Service Fabric oferece a experiência completa de gerenciamento, incluindo implantação, upgrades, versionamentos, reversões e monitoramento de saúde.

**Modernizar**  
Modernização é a adição de novos serviços ao lado do código containerizado existente. Se você vai escrever um novo código, é melhor dar pequenos passos pelo caminho dos microsserviços. Isso pode significar adicionar um novo ponto final da API REST ou uma nova lógica de negócios. Dessa forma, você inicia o processo de construção de novos microsserviços e prática de desenvolvimento e implantação deles.

**Inove**  
Uma abordagem de microsserviços acomoda as necessidades de negócios em mudança. Nesta fase, você precisa decidir se deve começar a dividir a aplicação monolítica em serviços ou inovar. Um exemplo clássico aqui é quando um banco de dados que você está usando como uma fila de fluxo de trabalho se torna um gargalo de processamento. Conforme o número de solicitações de fluxo de trabalho aumenta o trabalho precisa ser distribuído para dimensionamento. Pegue essa peça específica do aplicativo que não está dimensionando, ou que precisa ser atualizada com mais freqüência, e divida-a como um microserviço e inove.

**Transforme aplicativos em microsserviços**  
Nesta fase, sua aplicação é totalmente composta de (ou dividido em) microserviços. Para chegar a este ponto, você fez a jornada de microsserviços. Você pode começar aqui, mas para fazê-lo sem uma plataforma de microsserviços para ajudá-lo requer um investimento significativo.

### <a name="are-microservices-right-for-my-application"></a>Os microsserviços são ideais para meu aplicativo?

Talvez. Na Microsoft, à medida que mais equipes começaram a construir para a nuvem por razões de negócios, muitos deles perceberam os benefícios de tomar uma abordagem semelhante a um microserviço. Bing, por exemplo, usa microsserviços há anos. Para as outras equipes, a abordagem dos microsserviços foi nova. As equipes descobriram que havia problemas difíceis de resolver fora de suas principais áreas de capacidade. É por isso que a Service Fabric ganhou tração como tecnologia para serviços de construção.

O objetivo da Service Fabric é reduzir as complexidades da construção de aplicativos de microsserviçopara que você não precise passar por tantos redesenhos caros. Comece pequeno, dimensione quando necessário, descontinue serviços, adicione novos e evolua com o uso do cliente. Também sabemos que há muitos outros problemas que ainda precisam ser resolvidos para tornar os microsserviços mais acessíveis à maioria dos desenvolvedores. Contêineres e o modelo de programação do ator são exemplos de pequenos passos nessa direção. Temos certeza de que mais inovações surgirão para facilitar uma abordagem de microsserviços.

## <a name="next-steps"></a>Próximas etapas

* [Microsserviços: uma revolução de aplicativo proporcionada pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Construindo microsserviços no Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Práticas recomendadas de aplicativo e cluster do Azure Service Fabric](service-fabric-best-practices-overview.md)
* [Visão geral da terminologia do Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
