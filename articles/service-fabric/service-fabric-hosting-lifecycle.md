---
title: O Azure Service Fabric hospedando o ciclo de vida de ativação e desativação
description: Explica o ciclo de vida do aplicativo e do pacote de pacotes em um nó
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: da395f020cf3d299211427e740975764653b487d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801946"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Ciclo de vida de hospedagem Service Fabric do Azure
Este artigo fornece uma visão geral dos eventos que ocorrem quando um aplicativo é ativado em um nó e várias configurações de cluster usadas para controlar o comportamento.

Antes de continuar, certifique-se de compreender os vários conceitos e relacionamentos explicados em [Modelar um aplicativo no Service Fabric][a1]. 

> [!NOTE]
> Neste artigo, a menos que seja explicitamente mencionado como significando algo diferente:
>
> - A *réplica* refere-se a uma réplica de um serviço com estado e uma instância de um serviço sem estado.
> - *CodePackage* é tratado como equivalente a um processo de *ServiceHost* que registra um *ServiceType*e hospeda réplicas de serviços desse *ServiceType*.
>

## <a name="activation-of-applicationservicepackage"></a>Ativação do aplicativo/pacote de pacotes

O pipeline para ativação é o seguinte:

1. Baixe o ApplicationPackage. Por exemplo: ApplicationManifest.xml etc.
2. Configurar o ambiente para o aplicativo para ex: criar usuários etc.
3. Iniciar o rastreamento de aplicativo para desativação.
4. Baixe o pacote de pacotes. Por exemplo: ServiceManifest.xml, código, config, pacotes de dados etc.
5. Configurar o ambiente do pacote de serviço para ex: instalar o firewall, alocar portas para pontos de extremidade, etc.
6. Comece a controlar o pacote de pacotes para desativação.
7. Inicie o SetupEntryPoint de CodePackages e aguarde a conclusão.
8. Iniciar MainEntryPoint de CodePackages.

### <a name="servicetype-blocklisting"></a>Inclusão na lista do ServiceType
**ServiceTypeDisableFailureThreshold** determina o número de falhas (ativação, download, falhas de CodePackage) após o qual o ServiceType está agendado para o inclusão na lista. Portanto, a primeira falha de ativação/download, ou falha CodePackage, deve disparar a agenda do ServiceType inclusão na lista. A configuração **ServiceTypeDisableGraceInterval** determina o intervalo de carência após o qual ServiceType é finalmente marcado como incluídos nesse nó. Observe que, para que isso aconteça, a reinicialização de ativação/download/CodePackage ainda deve estar no modo de repetição interno e sendo acompanhado pelo subsistema de hospedagem. Tentar novamente, significa, por exemplo: CodePackage será agendado para iniciar novamente após a falha, ou Service Fabric tentará baixar pacotes novamente.
Depois de incluídos, você deverá ver um erro "System. Hosting" relatado para a propriedade ' ServiceTypeRegistration: ServiceType '. O ServiceType foi desabilitado no nó. "

O ServiceType será habilitado novamente no nó 
- Se a operação de ativação for realizada com êxito ou atingir **ActivationMaxFailureCount** repetições após a falha.
- Se a operação de download for realizada com êxito ou atingir **DeploymentMaxFailureCount** repetições após a falha.
- Se um CodePackage que tenha falhado iniciar o backup e registrar o ServiceType com êxito.

O motivo para habilitar o ServiceType novamente após **ActivationMaxFailureCount** / **DeploymentMaxFailureCount** repetições é o máximo de tentativas Service Fabric será executado para ativar/baixar um aplicativo em um nó. Se não for bem sucedido, a operação atual não será repetida e, como Service Fabric deseja dar ao serviço outra oportunidade de ativação, o que pode ser bem sucedido, resultando na renovação automática do problema, ele está vinculado ao ciclo de vida da operação de ativação/download. Uma nova operação de ativação/download disparada pelo posicionamento de uma réplica pode disparar o ServiceType inclusão na lista novamente ou pode ter sucesso.

> [!NOTE]
> Se seu CodePackage que não registra um ServiceType estiver falhando, ele não afetará o ServiceType. Somente o CodePackage que hospeda uma falha de réplica afetará o ServiceType.
>

### <a name="codepackage-crash"></a>Falha de CodePackage
Quando uma falha CodePackage, o Service Fabric usa um back-off para iniciá-lo novamente e o back-off é independente de o pacote de códigos ter registrado um tipo conosco ou não.

O valor de retirada sempre é mín. (RetryTime, **ActivationMaxRetryInterval**) e esse valor pode ser constante, linear ou exponencial com base na configuração de **ActivationRetryBackoffExponentiationBase** .

- Constante: se **ActivationRetryBackoffExponentiationBase** = = 0, tente novamente = **ActivationRetryBackoffInterval**;
- Linear: se **ActivationRetryBackoffExponentiationBase** = = 0, tente novamente = ContinuousFailureCount * **ActivationRetryBackoffInterval** , em que ContinousFailureCount é o número de vezes que um CodePackage falha ou falha ao ser ativado.
- Exponencial: RetryTime = (**ActivationRetryBackoffInterval** em segundos) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Você pode controlar o comportamento conforme desejar, como reinícios rápidos. Vamos falar sobre linear. Isso significa que, se um CodePackage falhar, o intervalo de início será após 10, 20, 30 40 s até que o CodePackage seja desativado. 
    
A quantidade máxima de tempo Service Fabric faz logoff (espera) depois que uma falha é regida pelo **ActivationMaxRetryInterval**
    
Se o CodePackage falhar e voltar, ele precisará permanecer ativo para que o **CodePackageContinuousExitFailureResetInterval** para Service Fabric a considerar como íntegro em que ponto ele substituirá o relatório de integridade como OK e redefinirá o ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage não registrando ServiceType
Se um CodePackage permanecer vivo e for esperado registrar um ServiceType conosco, mas nunca faz isso, nesse caso Service Fabric gerará um aviso HealthReport depois de **ServiceTypeRegistrationTimeout** dizer que ServiceType não está sendo configurado dentro do tempo limite.

### <a name="activation-failure"></a>Falha na ativação
Service Fabric sempre usa uma retirada linear (igual à falha CodePackage) quando encontra um erro durante a ativação. Isso significa que a operação de ativação será exibida após (0 + 10 + 20 + 30 + 40) = 100 s (primeira tentativa é imediata). Depois que essa ativação não for repetida.
    
A retirada de ativação máxima pode ser **ActivationMaxRetryIntervalda** e tentar novamente **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Falha no download
Service Fabric sempre usa um retirada linear quando encontra um erro durante o download. Isso significa que a operação de ativação será exibida após (0 + 10 + 20 + 30 + 40) = 100 s (primeira tentativa é imediata). Depois que esse download não for repetido. A retirada linear para download é igual a ContinuousFailureCount ***DeploymentRetryBackoffInterval** e pode ser retirada máxima para **DeploymentMaxRetryInterval**. Assim como as ativações, a operação de download pode tentar novamente por **ActivationMaxFailureCount**.

> [!NOTE]
> Antes de alterar as configurações, aqui estão alguns exemplos que você deve ter em mente.

* Se o CodePackage continuar falhando e fizer o logoff, o ServiceType será desabilitado. Mas se a configuração de ativações for tal que ela tenha uma reinicialização rápida, o CodePackage poderá chegar algumas vezes antes de poder ver o desativação do ServiceType. Por exemplo: Suponha que seu CodePackage apareça, registra o ServiceType com Service Fabric e, em seguida, falha. Nesse caso, uma vez que a hospedagem recebe um registro de tipo, o período de **ServiceTypeDisableGraceInterval** é cancelado. E isso pode ser repetido até que o CodePackage faça o backup de um valor maior que **ServiceTypeDisableGraceInterval** e, depois, o ServiceType será desabilitado no nó. Portanto, pode ser um pouco antes de o ServiceType estar desabilitado no nó.

* No caso de ativações, quando Service Fabric sistema precisa posicionar uma réplica em um nó, a RA (ReconfigurationAgent) solicita que o subsistema de hospedagem ative o aplicativo e repete a solicitação de ativação a cada 15 segundos (**RAPMessageRetryInterval**). Para Service Fabric sistema saber que o ServiceType foi desabilitado, a operação de ativação na hospedagem precisa residir por um período mais longo do que o intervalo de repetição e **ServiceTypeDisableGraceInterval**. Por exemplo: Deixe que o cluster tenha as configurações **ActivationMaxFailureCount** definidas como 5 e **ActivationRetryBackoffInterval** definidas como 1 s. Isso significa que a operação de ativação será exibida após (0 + 1 + 2 + 3 + 4) = 10 s (primeira repetição é imediata) e, depois disso, a hospedagem será repetida. Nesse caso, a operação de ativação será concluída e não tentará novamente após 15 segundos. Isso aconteceu porque Service Fabric esgotou todas as repetições dentro de 15 segundos. Assim, todas as tentativas do ReconfigurationAgent criarão uma nova operação de ativação no subsistema de hospedagem e o padrão continuará repetindo e o ServiceType nunca será desabilitado no nó. Como o ServiceType não será desabilitado no componente do sistema do node it (Failovermanager), não moverá a réplica para um nó diferente.
> 

## <a name="deactivation"></a>Desativação

Quando um pacote de pacotes é ativado em um nó, ele é acompanhado para desativação. O desativador é a entidade que controla a ti.
O desativador funciona de duas maneiras:

1.  Periodicamente: a cada **DeactivationScanInterval**, ele verifica os pacotes de itens que nunca hospedaram uma réplica e as marca como candidatos para desativação.
2.  ReplicaClose: se uma réplica for fechada, o desativador obterá um DecrementUsageCount. Se a contagem chegar a 0, isso significa que o pacote de pacotes não está hospedando nenhuma réplica e, portanto, é um candidato para desativação.

 Com base no modo de ativação [exclusivo/compartilhado][a2], os candidatos para desativação são agendados após **DeactivationGraceInterval** para sharedmode/ **ExclusiveModeDeactivationGraceInterval** para exclusivemode. Se, nesse momento, um novo posicionamento de réplica chegar em seguida, a desativação será cancelada.

### <a name="periodically"></a>Regularmente
Exemplo 1: digamos que o desativador faça uma verificação no tempo T (**DeactivationScanInterval**). Sua próxima verificação será em 2T. Suponha que uma ativação do pacote de pacotes ocorreu em T + 1. Esse pacote de pacotes não está hospedando uma réplica e, portanto, precisa ser desativado. Para que o pacote de pacotes seja um candidato para desativação, ele precisa estar no estado de nenhuma réplica por pelo menos T time. Isso significa que ele será elegível para desativação em 2T + 1. Portanto, a verificação em 2T não encontrará esse pacote como um candidato para desativação. O próximo ciclo de desativação 3T agendará este pacote para desativação, pois agora ele não está no estado de réplica para o tempo T.  

Exemplo 2: digamos que um pacote de pacotes é ativado no momento em que T-1 e o desativador faz uma verificação em T. O pacote de pacotes não hospeda uma réplica. Em seguida, na próxima verificação 2T, este pacote de pacotes será encontrado como um candidato para desativação e, portanto, será agendado para desativação.  

Exemplo 3: digamos que um pacote de pacotes seja ativado em T – 1 e o desativador faz uma verificação em T. O pacote de pacotes não hospeda uma réplica ainda. Agora, em T + 1, uma réplica é colocada, ou seja, A hospedagem Obtém um IncrementUsageCount, o que significa que uma réplica é criada. Agora, em 2T, este pacote de pacotes não será agendado para desativação. Agora, a desativação será movida para a lógica ReplicaClose explicada abaixo.

Exemplo 4: digamos que seu pacote de pacotes seja grande, como 10 GB, pode demorar um pouco de tempo para baixar no nó. Depois que um aplicativo é ativado, o desativador rastreia seu ciclo de vida. Agora, se você tiver o **DeactivationScanInterval** config Small, poderá encontrar problemas em que o seu pacote de pacotes não está recebendo tempo para ser ativado no nó porque o tempo todo foi atingido no download. Para superar o problema, você pode [baixar previamente o pacote de pacotes no nó][p1]. 

> [!NOTE]
> Portanto, um pacote de pacotes pode ser desativado em qualquer lugar entre (**DeactivationScanInterval** para 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Fechamento de réplica:
O deactivator mantém a contagem de réplicas que um pacote de pacotes mantém. Se um pacote de pacotes estiver mantendo uma réplica e a réplica estiver fechada/inativa, a hospedagem obterá um DecrementUsageCount. Quando uma réplica é aberta, a hospedagem Obtém um IncrementUsageCount. O decréscimo significa que o pacote de pacotes agora está hospedando uma réplica less e quando a contagem cai para 0, então o pacote de pacotes está agendado para desativação e o tempo após o qual ele será desativado é **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Por exemplo: Vamos dizer que um decréscimo ocorre em T e o pacote está agendado para desativar em 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Se, durante esse tempo, a hospedagem obtiver um IncrementUsage significando que uma réplica foi criada, a desativação será cancelada.

> [!NOTE]
>Então, o que essa configuração basicamente significa: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: o tempo fornecido a um pacote de pacotes para hospedar novamente outra réplica depois de ter hospedado qualquer réplica. 
**DeactivationScanInterval**: o tempo mínimo dado ao pacote de pacotes para hospedar uma réplica se nunca tiver hospedado nenhuma réplica, ou seja, Se não for usado.
>

### <a name="ctrlc"></a>Ctrl+C
Após um pacote de pacotes passar o **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** e ainda não estiver hospedando uma réplica, a desativação não poderá ser cancelada. CodePackage são emitidos um manipulador CTRL + C que significa que agora o pipeline de desativação precisa passar por cima para reduzir o processo. Durante esse tempo, se uma nova réplica para o mesmo pacote de pacotes estiver tentando ser colocada, ela falhará porque não podemos fazer a transição da desativação para a ativação.

## <a name="cluster-configs"></a>Configurações de cluster

Configurações com padrões que afetam a ativação/decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: padrão 1. O limite para a contagem de falhas após o qual o FM (Failovermanager) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento.
**ServiceTypeDisableGraceInterval**: padrão de 30 s. intervalo de tempo após o qual o tipo de serviço pode ser desabilitado.
**ServiceTypeRegistrationTimeout**: padrão de 300 s. O tempo limite para o ServiceType se registrar com Service Fabric.

### <a name="activation"></a>Ativação
**ActivationRetryBackoffInterval**: intervalo de retirada de 10 segundos padrão em cada falha de ativação.
**ActivationMaxFailureCount**: padrão 20. Contagem máxima para a qual o sistema tentará novamente a ativação com falha antes de desistir. 
**ActivationRetryBackoffExponentiationBase**: padrão 1,5.
**ActivationMaxRetryInterval**: padrão de retirada máx. de 3600 s para ativação em falhas.
**CodePackageContinuousExitFailureResetInterval**: padrão de 300 s. O tempo limite para redefinir a contagem de falhas de saída contínua para CodePackage.

### <a name="download"></a>Baixar
**DeploymentRetryBackoffInterval**: o padrão é 10. Intervalo de retirada da falha de implantação.
**DeploymentMaxRetryInterval**: a retirada de 3600 s. máx. máxima para a implantação em falhas.
**DeploymentMaxFailureCount**: padrão 20. A implantação de aplicativo será repetida por DeploymentMaxFailureCount vezes antes que a implantação desse aplicativo no nó falhe.

### <a name="deactivation"></a>Desativação
**DeactivationScanInterval**: padrão de 600 s. tempo mínimo dado ao pacote de pacotes para hospedar uma réplica se nunca tiver hospedado nenhuma réplica, ou seja, Se não for usado.
**DeactivationGraceInterval**: padrão de 60 s. O tempo dado a um pacote de pacotes para hospedar novamente outra réplica depois de ter hospedado qualquer réplica no caso do modelo de processo **compartilhado** .
**ExclusiveModeDeactivationGraceInterval**: padrão 1 s. O tempo dado a um pacote de pacotes para hospedar novamente outra réplica depois de ter hospedado qualquer réplica no caso de um modelo de processo **exclusivo** .

## <a name="next-steps"></a>Próximas etapas
[Empacotar um aplicativo][a3] e prepará-lo para a implantação.

[Implantar e remover aplicativos][a4]. Esse artigo descreve como usar o PowerShell para gerenciar instâncias do aplicativo.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricservicepackagetonode
