---
title: Recuperação de desastres do Azure Service Fabric
description: O Azure Service Fabric oferece recursos para lidar com desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371640"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastre no Azure Service Fabric
Uma parte fundamental da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos diferentes de falhas. Isso é especialmente importante para falhas que não são planejadas e fora do seu controle. 

Este artigo descreve alguns modos de falha comuns que podem ser desastres se não forem modelados e gerenciados corretamente. Também discute mitigações e ações a serem tomadas se um desastre acontecer de qualquer maneira. O objetivo é limitar ou eliminar o risco de inatividade ou perda de dados quando ocorrem falhas, planejadas ou não.

## <a name="avoiding-disaster"></a>Evitando desastres
O principal objetivo do Azure Service Fabric é ajudá-lo a modelar seu ambiente e seus serviços de forma que tipos comuns de falha não sejam desastres. 

Em geral, existem dois tipos de cenários de desastre/falha:
- Falhas de hardware e software
- Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A maneira mais fácil de sobreviver a falhas é executando mais cópias do serviço através dos limites de falha de hardware ou software. 

Por exemplo, se o seu serviço está sendo executado em apenas uma máquina, a falha dessa máquina é um desastre para esse serviço. A maneira simples de evitar esse desastre é garantir que o serviço esteja sendo executado em várias máquinas. Os testes também são necessários para garantir que a falha de uma máquina não interrompa o serviço em execução. O planejamento de capacidade garante que uma instância de substituição possa ser criada em outro lugar e que a redução da capacidade não sobrecarregue os serviços restantes. 

O mesmo padrão funciona independentemente da falha que você estiver tentando evitar. Por exemplo, se você está preocupado com a falha de uma SAN, você se depara com várias SANs. Se estiver preocupado com a perda de um rack de servidores, execute entre vários racks. Se você está preocupado com a perda de data centers, seu serviço deve ser executado em várias regiões do Azure, em várias Zonas de Disponibilidade do Azure ou em seus próprios data centers. 

Quando um serviço é estendido em várias instâncias físicas (máquinas, racks, data centers, regiões), você ainda está sujeito a alguns tipos de falhas simultâneas. Mas falhas únicas e até mesmo múltiplas de um determinado tipo (por exemplo, uma única falha de máquina virtual ou link de rede) são automaticamente tratadas e, portanto, não são mais um "desastre". 

A Service Fabric fornece mecanismos para expandir o cluster e as alças que trazem os nódulos e serviços com falha de volta. O Service Fabric também permite executar muitas instâncias de seus serviços para evitar que falhas não planejadas se transformem em desastres reais.

Pode haver razões pelas quais executar uma implantação grande o suficiente para cobrir falhas não é viável. Por exemplo, pode ser necessário mais recursos de hardware do que você está disposto a pagar em relação à chance de falhar. Quando você está lidando com aplicativos distribuídos, saltos adicionais de comunicação ou custos de replicação do estado em distâncias geográficas podem causar latência inaceitável. Esse limite varia com cada aplicativo. 

Para falhas de software especificamente, a falha pode estar no serviço que você está tentando dimensionar. Neste caso, mais cópias não impedem o desastre, porque a condição de falha está correlacionada em todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o serviço esteja estendido por todo o mundo com muitas redundâncias, ele ainda poderá passar por eventos desastrosos. Por exemplo, alguém pode acidentalmente reconfigurar o nome DNS para o serviço ou excluí-lo completamente. 

Por exemplo, digamos que você tivesse um serviço do Service Fabric com estado e alguém excluísse esse serviço acidentalmente. A menos que haja alguma outra atenuação, esse serviço e todo o estado que ele tinha agora se foram. Esses tipos de desastres operacionais ("ops") exigem mitigações e etapas de recuperação diferentes do que as que são necessárias para falhas não planejadas regulares. 

As melhores maneiras de evitar esses tipos de falhas operacionais são:
- Restringir o acesso operacional ao meio ambiente.
- Estritamente auditar operações perigosas.
- Imponha a automação, evite alterações manuais ou fora da banda e valide alterações específicas em relação ao ambiente antes de promulgá-las.
- Certifique-se de que as operações destrutivas são "suaves". As operações suaves não fazem efeito imediatamente ou podem ser desfeitas dentro de uma janela de tempo.

A Service Fabric fornece mecanismos para evitar falhas operacionais, como fornecer controle de acesso [baseado em função](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria dessas falhas operacionais exige esforços organizacionais e outros sistemas. A Service Fabric fornece mecanismos para sobreviver a falhas operacionais, principalmente [backup e restauração de serviços estatais.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Gerenciando falhas
O objetivo do Service Fabric é o gerenciamento automático de falhas. Mas para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de falhas _não_ devem ser automaticamente tratadas por razões de segurança e continuidade de negócios. 

### <a name="handling-single-failures"></a>Lidando com falhas únicas
Um único computador pode falhar por diversos tipos de motivos. Às vezes, são causas de hardware, como fontes de alimentação e falhas de hardware de rede. Outras falhas estão no software. Estas incluem falhas do sistema operacional e do próprio serviço. A Service Fabric detecta automaticamente esses tipos de falhas, incluindo casos em que a máquina fica isolada de outras máquinas por causa de problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha única, a coisa mais simples que você pode fazer é garantir que seus serviços são executados em mais de um nó por padrão. Para serviços apátridas, certifique-se de que `InstanceCount` seja maior que 1. Para serviços estaduais, a `TargetReplicaSetSize` `MinReplicaSetSize` recomendação mínima é que e ambos são definidos como 3. Executar mais cópias do código de seu serviço garante que o serviço seja capaz de lidar com qualquer falha única automaticamente. 

### <a name="handling-coordinated-failures"></a>Lidando com falhas coordenadas
Falhas coordenadas em um cluster podem ser devido a falhas e alterações de infra-estrutura planejadas ou não planejadas ou mudanças planejadas de software. O Service Fabric modela zonas de infra-estrutura que experimentam falhas coordenadas como *domínios de falhas*. As áreas que sofrerão alterações coordenadas de software são modeladas como *domínios de upgrade.* Para obter mais informações sobre domínios de falhas, domínios de upgrade e topologia de cluster, consulte [Descrever um cluster de malha de serviço usando o Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md).

Por padrão, o Service Fabric considera os domínios de falha e atualização ao planejar onde seus serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que seus serviços se executem em vários domínios de falha e upgrade para que, se as alterações planejadas ou não planejadas acontecerem, seus serviços permaneçam disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faz com que todas as máquinas em um rack falhem simultaneamente. Com várias cópias do serviço em execução, a perda de muitas máquinas em falha de domínio de falha se transforma em apenas mais um exemplo de uma única falha para um serviço. É por isso que o gerenciamento de domínios de falha e atualização é fundamental para garantir alta disponibilidade de seus serviços. 

Quando você está executando o Service Fabric no Azure, os domínios de falha e os domínios de upgrade são gerenciados automaticamente. Em outros ambientes, eles podem não ser. Se você estiver construindo seus próprios clusters no local, certifique-se de mapear e planejar seu layout de domínio de falha corretamente.

Os domínios de upgrade são úteis para modelar áreas onde o software será atualizado ao mesmo tempo. Por causa disso, os domínios de upgrade também definem frequentemente os limites onde o software é retirado durante atualizações planejadas. Atualizações do Service Fabric e de seus serviços seguem o mesmo modelo. Para obter mais informações sobre upgrades de rolamento, domínios de upgrade e o modelo de saúde service fabric que ajuda a evitar que alterações não intencionais afetem o cluster e seu serviço, consulte:

 - [Atualização de aplicativos](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicativos](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de saúde do Service Fabric](service-fabric-health-introduction.md)

Você pode visualizar o layout do seu cluster usando o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Os nós espalhados por domínios de falha no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelar áreas de falha, atualizações de rolamento, executar muitas instâncias do seu código de serviço e estado, regras de colocação para garantir que seus serviços sejam executados em domínios de falha e upgrade, e monitoramento de saúde incorporado são apenas *alguns* dos recursos que o Service Fabric fornece para evitar que problemas operacionais normais e falhas se transformem em desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Lidando com falhas simultâneas de hardware ou software
Temos falado de falhas individuais. Como você pode ver, eles são fáceis de manusear para serviços apátridas e estaduais apenas mantendo mais cópias do código (e estado) rodando em domínios de falha e upgrade. 

Várias falhas aleatórias simultâneas também podem ocorrer. Estes são mais propensos a levar a tempo de inatividade ou a um desastre real.


#### <a name="stateless-services"></a>Serviços apátridas
A contagem de instâncias para um serviço apátrida indica o número desejado de instâncias que precisam ser executados. Quando qualquer (ou todas) das instâncias falham, o Service Fabric responde criando automaticamente instâncias de substituição em outros nós. O Service Fabric continua a criar substituições até que o serviço volte à contagem de instâncias desejada.

Por exemplo, suponha que `InstanceCount` o serviço apátrida tenha um valor de -1. Esse valor significa que uma instância deve ser executado em cada nó no cluster. Se algumas dessas instâncias falharem, o Service Fabric detectará que o serviço não está no estado desejado e tentará criar as instâncias nos ártões onde eles estão faltando.

#### <a name="stateful-services"></a>Serviços estatais
Existem dois tipos de serviços estatais:
- Estado cheio de estado persistente.
- Estado cheio de estado não persistiu. (O estado é armazenado na memória.)

A recuperação de um serviço estadual depende do tipo de serviço estatal, quantas réplicas o serviço tinha e quantas réplicas falharam.

Em um serviço estadual, os dados de entrada são replicados entre réplicas (as secundárias primárias e quaisquer ativas). Se a maioria das réplicas receber os dados, os dados são considerados *quórum* comprometido. (Para cinco réplicas, três serão um quórum.) Isso significa que, a qualquer momento, haverá pelo menos um quórum de réplicas com os dados mais recentes. Se as réplicas falharem (digamos duas de cinco), podemos usar o valor do quórum para calcular se podemos recuperar. (Como as três das cinco réplicas restantes ainda estão em cima, é garantido que pelo menos uma réplica terá dados completos.)

Quando um quórum de réplicas falha, a partição é declarada em estado de perda de *quórum.* Digamos que uma partição tenha cinco réplicas, o que significa que pelo menos três têm dados completos. Se um quórum (três em cinco) de réplicas falhar, o Service Fabric não poderá determinar se as réplicas restantes (duas em cinco) têm dados suficientes para restaurar a partição. Nos casos em que o Service Fabric detecta perda de quórum, seu comportamento padrão é evitar gravações adicionais à partição, declarar perda de quórum e esperar que um quórum de réplicas seja restaurado.

Determinar se um desastre ocorreu para um serviço estadual e, em seguida, gerenciá-lo segue três etapas:

1. Determinando se houve perda de quórum ou não.
   
   A perda de quórum é declarada quando a maioria das réplicas de um serviço estatal são perdidas ao mesmo tempo.
2. Determinar se a perda de quórum é permanente ou não.
   
   Na maioria das vezes, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, as máquinas virtuais são relançadas e as partições de rede se curam. Às vezes, porém, as falhas são permanentes. Se as falhas são permanentes ou não depende se o serviço estatal persiste em seu estado ou se o mantém apenas na memória: 
   
   - Para serviços sem estado persistente, a falha de um quorum ou mais de réplicas causa _imediatamente_ uma perda de quorum permanente. Quando o Service Fabric detecta a perda de quorum em um serviço não persistente com estado, ele prossegue imediatamente para a etapa 3, declarando uma (potencial) perda de dados. Proceder à perda de dados faz sentido porque o Service Fabric sabe que não há razão para esperar as réplicas voltarem. Mesmo que se recuperem, os dados serão perdidos por causa da natureza não persistida do serviço.
   - Para serviços persistentes estaduais, uma falha no quórum ou mais de réplicas faz com que o Service Fabric aguarde que as réplicas voltem e restaure o quórum. Isso resulta em uma interrupção de serviço para qualquer _gravação_ na partição afetada (ou "conjunto de réplicas") do serviço. No entanto, as leituras ainda podem ser possíveis com garantias de consistência reduzidas. O tempo padrão que o Service Fabric espera para que o quórum seja restaurado é *infinito,* porque o processo é um evento (potencial) de perda de dados e traz outros riscos. Isso significa que a Service Fabric não prosseguirá para a próxima etapa, a menos que um administrador tome medidas para declarar perda de dados.
3. Determinar se os dados são perdidos e restaurar a partir de backups.

   Se a perda de quórum tiver sido declarada (automaticamente ou por ação administrativa), a Service Fabric e os serviços passam a determinar se os dados foram realmente perdidos. Neste ponto, a Service Fabric também sabe que as outras réplicas não estão voltando. Essa foi a decisão tomada quando paramos de esperar que a perda de quorum se resolvesse. Normalmente, a melhor alternativa para o serviço é congelar e aguardar a intervenção administrativa específica.
   
   Quando o Service `OnDataLossAsync` Fabric chama o método, é sempre por causa da _suspeita_ de perda de dados. O Service Fabric garante que essa chamada seja entregue para a _melhor_ réplica restante. Essa seria qualquer réplica tenha tido o maior progresso. 
   
   A razão pela qual sempre dizemos _perda suspeita_ de dados é que é possível que a réplica restante tenha o mesmo estado que a primária fez quando o quórum foi perdido. No entanto, sem o estado para compará-la, não há nenhum modo adequado do Service Fabric ou dos operadores terem certeza.     
   
   Então, o que uma implementação típica do método `OnDataLossAsync` faz?
   1. Os registros de `OnDataLossAsync` implementação que foram acionados, e ele dispara quaisquer alertas administrativos necessários.
   1. Normalmente, a implementação pausa e aguarda novas decisões e ações manuais a serem tomadas. Isso porque, mesmo que os backups estejam disponíveis, eles podem precisar estar preparados. 
   
      Por exemplo, se dois serviços diferentes coordenarem as informações, esses backups podem precisar ser modificados para garantir que, após a restauração, as informações com as quais esses dois serviços se preocupam sejam consistentes. 
   1. Muitas vezes há alguma outra telemetria ou exaustão do serviço. Esses metadados podem estar contidos em outros serviços ou em logs. Essas informações podem ser usadas conforme necessário para determinar se houve chamadas recebidas e processadas na primária que não estavam presentes no backup ou replicadas nesta réplica em particular. Essas chamadas podem precisar ser repetidas ou adicionadas ao backup antes que a restauração seja viável.  
   1. A implementação compara o estado da réplica restante com o contido em quaisquer backups disponíveis. Se você estiver usando coleções confiáveis da Service Fabric, existem [ferramentas e processos](service-fabric-reliable-services-backup-restore.md) disponíveis para fazê-lo. O objetivo é ver se o estado dentro da réplica é suficiente, e ver o que o backup pode estar faltando.
   1. Após a comparação ser feita, e após a restauração ser concluída (se necessário), o código de serviço deve retornar **verdadeiro** se alguma alteração de estado for feita. Se a réplica determinou que era a melhor cópia disponível do estado e não fez alterações, o código retorna **falso**. 
   
      Um valor **verdadeiro** indica que quaisquer _outras_ réplicas restantes podem agora ser inconsistentes com esta. Elas serão descartadas e recriadas desta réplica. Um valor **falso** indica que não foram feitas alterações de estado, para que as outras réplicas possam manter o que têm. 

É extremamente importante que os autores de serviços pratiquem potenciais cenários de perda de dados e falhas antes que os serviços sejam implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante fazer backup periódico [do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços estaduais para uma loja geo-redundante. 

Você também deve garantir que você tem a capacidade de restaurar o estado. Como backups de muitos serviços diferentes são feitos em momentos diferentes, você precisa garantir que, após uma restauração, seus serviços tenham uma visão consistente um do outro. 

Por exemplo, considere uma situação em que um serviço gera um número e o armazena e, em seguida, envia-o para outro serviço que também o armazena. Depois de uma restauração, você pode descobrir que o segundo serviço tem o número, mas o primeiro não, porque seu backup não incluiu essa operação.

Se você descobrir que as réplicas restantes são insuficientes para continuar em um cenário de perda de dados e não puder reconstruir o estado de serviço a partir de telemetria ou exaustão, a frequência de seus backups determina o melhor objetivo de ponto de recuperação (RPO) possível. Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quórum permanente e perda de dados que requer restauração de um backup. Esses cenários são incluídos como parte das ferramentas de testability em Service Fabric, gerenciadas pelo Serviço de Análise de Falhas. Para obter mais informações sobre essas ferramentas e padrões, consulte [Introdução ao Serviço de Análise de Falhas](service-fabric-testability-overview.md). 

> [!NOTE]
> Os serviços do sistema também podem sofrer perda de quórum. O impacto é específico para o serviço em questão. Por exemplo, a perda de quórum no serviço de nomeação afeta a resolução de nomes, enquanto a perda de quórum no serviço Failover Manager bloqueia a criação de novos serviços e failovers. 
> 
> Os serviços do sistema Service Fabric seguem o mesmo padrão de seus serviços para a gestão do estado, mas não recomendamos que você tente movê-los para fora da perda de quórum e para uma possível perda de dados. Em vez disso, recomendamos que você [busque apoio](service-fabric-support.md) para encontrar uma solução direcionada à sua situação. Normalmente é preferível esperar até que as réplicas para baixo retornem.
>

#### <a name="troubleshooting-quorum-loss"></a>Solução de problemas perda de quórum

As réplicas podem estar desligadas intermitentemente por causa de uma falha passageira. Espere algum tempo enquanto o Service Fabric tenta trazê-los à tona. Se as réplicas foram desligadas por mais de uma duração esperada, siga estas ações de solução de problemas:
- As réplicas podem estar falhando. Verifique os relatórios de saúde em nível de réplica e os registros de aplicativos. Colete lixeiras e tome as medidas necessárias para se recuperar.
- O processo da réplica pode ter ficado sem resposta. Inspecione os registros de aplicativos para verificar isso. Coletar despejos de processo e, em seguida, parar o processo sem resposta. A Service Fabric criará um processo de substituição e tentará trazer a réplica de volta.
- Os nódulos que hospedam as réplicas podem estar desligados. Reinicie a máquina virtual subjacente para levantar os nódulos.

Às vezes, pode não ser possível recuperar réplicas. Por exemplo, as unidades falharam ou as máquinas fisicamente não estão respondendo. Nestes casos, o Service Fabric precisa ser dito para não esperar pela recuperação da réplica.

*Não use* esses métodos se a perda de dados potencial é inaceitável para colocar o serviço online. Nesse caso, todos os esforços devem ser feitos para recuperar máquinas físicas.

As seguintes ações podem resultar em perda de dados. Verifique antes de segui-los.
   
> [!NOTE]
> Nunca é _seguro_ usar esses métodos além de uma maneira direcionada contra partições específicas. 
>

- Use `Repair-ServiceFabricPartition -PartitionId` a `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API. Esta API permite especificar o ID da partição para sair da perda de quórum e para uma perda de dados em potencial.
- Se o cluster encontrar falhas frequentes que fazem com que os serviços entrem em um estado de perda de quórum e a perda potencial _de dados seja aceitável,_ especificar um valor apropriado [do QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pode ajudar seu serviço a se recuperar automaticamente. Service Fabric aguardará o `QuorumLossWaitDuration` valor fornecido (o padrão é infinito) antes de realizar a recuperação. Não *don't* recomendamos este método porque pode causar perdas inesperadas de dados.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster do Service Fabric
Em geral, o cluster Service Fabric é um ambiente altamente distribuído sem pontos únicos de falha. Uma falha de qualquer nó não causará problemas de disponibilidade ou confiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas diretrizes fornecidas anteriormente. Ou seja, eles sempre são executados com três ou mais réplicas por padrão, e os serviços do sistema que são insem estatais são executados em todos os nós. 

As camadas subjacentes de detecção de falha e rede do Service Fabric são totalmente distribuídas. A maioria dos serviços de sistema podem ser recriados dos metadados no cluster ou sabem como ressincronizar seu estado de outros locais. A disponibilidade do cluster pode ficar comprometida se os serviços do sistema entrarem em situações de perda de quórum como as descritas anteriormente. Nesses casos, você pode não ser capaz de executar certas operações no cluster (como iniciar um upgrade ou implantar novos serviços), mas o cluster em si ainda está em funcionamento. 

Os serviços em um cluster em execução continuarão funcionando nessas condições, a menos que exijam que os serviços do sistema continuem funcionando. Por exemplo, se o Failover Manager estiver em perda de quórum, todos os serviços continuarão a ser executados. Mas todos os serviços que falharem não serão capazes de reiniciar automaticamente, porque isso requer o envolvimento do Failover Manager. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Falhas de um data center ou de uma região do Azure
Em casos raros, um datacenter físico pode ficar temporariamente indisponível por perda de energia ou conectividade de rede. Nesses casos, seus clusters e serviços do Service Fabric nesse data center ou região do Azure ficarão indisponíveis. No entanto, _os dados ficam preservados_. 

Para os clusters em execução no Azure, você pode exibir as atualizações sobre interrupções na [Página de status do Azure][azure-status-dashboard]. No caso altamente improvável de que um datacenter físico seja parcial ou totalmente destruído, qualquer cluster de Malha de Serviço hospedado lá, ou os serviços dentro deles, podem ser perdidos. Essa perda inclui qualquer estado que não tenha backup fora desse datacenter ou região.

Existem duas estratégias diferentes para sobreviver à falha permanente ou sustentada de um único datacenter ou região: 

- Execute clusters de malha de serviço separados em várias regiões e use algum mecanismo para failover e failback entre esses ambientes. Esse tipo de modelo ativo/ativo/ativo/passivo de vários clusters requer gerenciamento adicional e código de operações. Esse modelo também requer a coordenação de backups dos serviços em um data center ou região para que eles estejam disponíveis em outros data centers ou regiões quando um falha. 
- Executar um único cluster do Service Fabric que abrange vários data centers ou regiões. A configuração mínima suportada para essa estratégia é de três data centers ou regiões. O número recomendado de regiões ou data centers é cinco. 
  
  Este modelo requer uma topologia de cluster mais complexa. No entanto, o benefício é que a falha de um data center ou região é convertida de um desastre em uma falha normal. Essas falhas podem ser tratadas pelos mecanismos que funcionam para clusters dentro de uma única região. Domínios de falhas, domínios de upgrade e regras de colocação do Service Fabric garantem que as cargas de trabalho sejam distribuídas para que eles tolerem falhas normais. 
  
  Para obter mais informações sobre políticas que possam ajudar a operar serviços neste tipo de cluster, consulte [Políticas de colocação para serviços de malha de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Falhas aleatórias que levam a falhas de cluster
Service Fabric tem o conceito de *nodes de sementes.* Trata-se de nós que mantêm a disponibilidade do cluster subjacente. 

Os nódulos de sementes ajudam a garantir que o cluster permaneça em funcionamento estabelecendo arrendamentos com outros nódulos e servindo como desempates durante certos tipos de falhas. Se falhas aleatórias removerem a maioria dos nódulos de sementes no cluster e não forem trazidas de volta rapidamente, seu cluster será automaticamente desligado. O cluster então falha. 

No Azure, o Service Fabric Resource Provider gerencia configurações de cluster de malha de malha de serviço. Por padrão, o Provedor de recursos distribui nós de sementes entre domínios de falha e upgrade para o *tipo de nó principal*. Se o tipo de nó primário for marcado como durabilidade de prata ou ouro, quando você remover um nó de semente (seja dimensionando no seu tipo de nó primário ou removendo-o manualmente), o cluster tentará promover outro nó não-semente da capacidade disponível do tipo de nó primário. Esta tentativa falhará se você tiver menos capacidade disponível do que o nível de confiabilidade do cluster exige para o seu tipo de nó principal.

Em ambos os clusters de malha de serviço autônomos e no Azure, o tipo de nó primário é o que executa as sementes. Quando você está definindo um tipo de nó primário, o Service Fabric aproveitará automaticamente o número de nós fornecidos criando até nove nades de sementes e sete réplicas de cada serviço do sistema. Se um conjunto de falhas aleatórias eliminar a maioria dessas réplicas simultaneamente, os serviços do sistema entrarão em perda de quórum. Se a maioria desses nós de semente for perdida, o cluster será desligado logo em seguida.

## <a name="next-steps"></a>Próximas etapas
- Aprenda a simular várias falhas usando a [estrutura de testability](service-fabric-testability-overview.md).
- Leia outros recursos de recuperação de desastres e alta disponibilidade. A Microsoft publicou várias orientações sobre estes tópicos. Embora alguns desses recursos se refiram a técnicas específicas para uso em outros produtos, eles contêm muitas práticas recomendadas gerais que você pode aplicar no contexto do Service Fabric:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Executando a análise de recuperação de desastre](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação de desastre e alta disponibilidade para aplicativos do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
