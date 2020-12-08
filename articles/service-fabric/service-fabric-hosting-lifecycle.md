---
title: O Azure Service Fabric hospedando o ciclo de vida de ativação e desativação
description: Explica o ciclo de vida do aplicativo e do pacote de pacotes em um nó
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779593"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Ciclo de vida de hospedagem Service Fabric do Azure

Este artigo fornece uma visão geral dos eventos que ocorrem no Azure Service Fabric quando um aplicativo é ativado em um nó e várias configurações de cluster usadas para controlar o comportamento.

Antes de continuar, certifique-se de compreender os vários conceitos e relacionamentos explicados em [Modelar um aplicativo no Service Fabric][a1]. 

> [!NOTE]
> Neste artigo, a menos que seja explicitamente mencionado como significando algo diferente:
>
> - A *réplica* refere-se a uma réplica de um serviço com estado e uma instância de um serviço sem estado.
> - *CodePackage* é tratado como equivalente a um processo de *ServiceHost* que registra um *ServiceType* e hospeda réplicas de serviços desse *ServiceType*.
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
**ServiceTypeDisableFailureThreshold** determina o número de falhas (ativação, download, falhas de CodePackage) após o qual o ServiceType está agendado para o inclusão na lista. A primeira falha de ativação, falha de download ou falha de CodePackage agendará o ServiceType inclusão na lista. A configuração **ServiceTypeDisableGraceInterval** determina o intervalo de carência após o qual ServiceType é marcado como incluídos nesse nó. Embora tudo isso esteja acontecendo, a ativação, o download e a reinicialização CodePackage estão sendo repetidos em paralelo. Tentar novamente, significa, por exemplo, que o CodePackage será agendado para iniciar novamente após a falha ou Service Fabric tentará baixar pacotes novamente.

Quando ServiceType for incluídos, você verá um erro de integridade "System. Hosting" reportado erro para a propriedade ' ServiceTypeRegistration: ServiceType '. O ServiceType foi desabilitado no nó. "

O ServiceType será habilitado no nó novamente se qualquer uma das seguintes ações acontecer:
- A ativação é realizada com êxito ou atinge **ActivationMaxFailureCount** repetições após a falha.
- O download é executado com êxito ou atinge **DeploymentMaxFailureCount** repetições após a falha.
- Um CodePackage que falhou inicia e registra com êxito o ServiceType.

**ActivationMaxFailureCount** e **DeploymentMaxFailureCount** são o número máximo de tentativas que Service Fabric fará para ativar/baixar um aplicativo em um nó, após o qual Service Fabric habilitará o ServiceType para ativação novamente. Isso é para dar ao serviço uma outra oportunidade de ativação, o que pode ter sucesso, resultando na recuperação automática do problema. Uma nova operação de ativação/download disparada pelo posicionamento e ativação de uma réplica pode disparar o ServiceType inclusão na lista novamente ou pode ter sucesso.

> [!NOTE]
> Se seu CodePackage que não registra um ServiceType estiver falhando, ele não afetará o ServiceType. Somente o CodePackage que hospeda uma falha de réplica afetará o ServiceType.
>

### <a name="codepackage-crash"></a>Falha de CodePackage
Quando um CodePackage falha, o Service Fabric usa uma retirada para iniciá-lo novamente. A retirada é independente de o pacote de códigos ter registrado um tipo com o Service Fabric Runtime ou não.

O valor de retirada é min (RetryTime, **ActivationMaxRetryInterval**) e esse valor de retirada é incrementado em valores constantes, lineares ou exponenciais com base no parâmetro de configuração **ActivationRetryBackoffExponentiationBase** .

- Constante: se **ActivationRetryBackoffExponentiationBase** = = 0, tente novamente = **ActivationRetryBackoffInterval**;
- Linear: se  **ActivationRetryBackoffExponentiationBase** = = 0, tente novamente = ContinuousFailureCount * **ActivationRetryBackoffInterval** , em que ContinousFailureCount é o número de vezes que um CodePackage falha ou falha ao ser ativado.
- Exponencial: RetryTime = (**ActivationRetryBackoffInterval** em segundos) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
Você pode controlar o comportamento alterando os valores. Por exemplo, se você quiser várias tentativas de reinicialização rápida, poderá usar linear definindo **ActivationRetryBackoffExponentiationBase** como 0 e **ActivationRetryBackoffInterval** como 10. Com essas configurações, se um CodePackage tiver falhado, o intervalo de início será após 10 segundos. Se o pacote continuar a falhar, a retirada mudaria para, 20, 30, 40 segundos e assim por diante, até que a ativação do CodePackage tenha sido bem-sucedida ou o pacote de códigos fosse desativado. 
    
A quantidade máxima de tempo Service Fabric faz logoff (espera) depois que uma falha é regida pelo **ActivationMaxRetryInterval**.
    
Se o CodePackage falhar e voltar, ele precisará ficar ativo para que o **CodePackageContinuousExitFailureResetInterval** para Service Fabric a considerar como íntegro. nesse ponto, ele substituirá o relatório de integridade de erro anterior como OK e redefinirá o ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>CodePackage não registrando ServiceType
Se um CodePackage permanecer vivo e for esperado registrar um ServiceType conosco, mas não tiver, Service Fabric gerará um aviso HealthReport após **ServiceTypeRegistrationTimeout** dizendo que ServiceType não foi registrado dentro do período de tempo esperado.

### <a name="activation-failure"></a>Falha na ativação
Service Fabric sempre usa uma retirada linear (igual à falha CodePackage) quando encontra um erro durante a ativação. Isso significa que a operação de ativação será exibida após (0 + 10 + 20 + 30 + 40) = 100 s (primeira tentativa é imediata). Depois que essa ativação não for repetida.
    
A retirada de ativação máxima pode ser **ActivationMaxRetryIntervalda** e tentar novamente **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Falha no download
Service Fabric sempre usa um retirada linear quando encontra um erro durante o download. Isso significa que a operação de ativação será exibida após (0 + 10 + 20 + 30 + 40) = 100 s (primeira tentativa é imediata). Depois disso, o download não é repetido. A retirada linear para download é igual a ContinuousFailureCount **_DeploymentRetryBackoffInterval_* e pode ser retirada máxima para **DeploymentMaxRetryInterval**. Assim como as ativações, a operação de download pode tentar novamente por **ActivationMaxFailureCount**.

> [!NOTE]
> Antes de alterar essas configurações, aqui estão alguns exemplos que você deve ter em mente.

* Se o CodePackage continuar falhando e fizer o logoff, o ServiceType será desabilitado. Mas se a configuração de ativação for tal que ela tenha uma reinicialização rápida, o CodePackage poderá acabar algumas vezes antes que o ServiceType seja realmente incluídos. Por exemplo: Suponha que seu CodePackage apareça, registra o ServiceType com Service Fabric e, em seguida, falha. Nesse caso, uma vez que a hospedagem recebe um registro de tipo, o período de **ServiceTypeDisableGraceInterval** é cancelado. E isso pode se repetir até que o CodePackage faça o backup de um valor maior que  **ServiceTypeDisableGraceInterval** e, em seguida, ServiceType será incluídos no nó. Maytake mais tempo do que você espera ver o ServiceType incluídos.

* No caso de ativações, quando Service Fabric sistema precisa posicionar uma réplica em um nó, a RA (ReconfigurationAgent) solicita que o subsistema de hospedagem ative o aplicativo e repete a solicitação de ativação a cada 15 segundos (governado pelo parâmetro de configuração **RAPMessageRetryInterval** ). Para Service Fabric saber que o ServiceType foi incluídos, a operação de ativação na hospedagem precisa residir por um período maior do que o intervalo de repetição e o **ServiceTypeDisableGraceInterval**. Por exemplo: Suponha que o cluster tenha **ActivationMaxFailureCount** definido como 5 e **ActivationRetryBackoffInterval** definido como 1 segundo. Isso significa que a operação de ativação será exibida após (0 + 1 + 2 + 3 + 4) = 10 segundos (Lembre-se de que a primeira tentativa é imediata) e, depois disso, a hospedagem será repetida. Nesse caso, a operação de ativação será concluída e não tentará novamente após 15 segundos. Isso acontece porque Service Fabric esgotou todas as tentativas permitidas dentro de 15 segundos. Assim, todas as tentativas do ReconfigurationAgent criarão uma nova operação de ativação no subsistema de hospedagem e o padrão continuará repetindo. Como resultado, o ServiceType nunca será incluídos no nó. Como o ServiceType não terá incluídos no nó, a réplica não será movida e tentada em um nó diferente.
> 

## <a name="deactivation"></a>Desativação

Quando um pacote de pacotes é ativado em um nó, ele é acompanhado para desativação. 

A desativação funciona de duas maneiras:

- Periodicamente: a cada **DeactivationScanInterval**, ele verifica se há pacotes que nunca hospedaram uma réplica e os marca como candidatos para desativação.
- ReplicaClose: se uma réplica for fechada, o desativador obterá um DecrementUsageCount. Se a contagem chegar a 0, isso significará que o pacote de pacotes não está hospedando nenhuma réplica e, portanto, é um candidato para desativação.

 Com base no modo de ativação [exclusivo/compartilhado][a2], os candidatos para desativação são agendados após o **DeactivationGraceInterval** para sharedmode e depois de **ExclusiveModeDeactivationGraceInterval** para exclusivemode. Se um novo posicionamento de réplica entrar no momento, a desativação será cancelada.

### <a name="periodically"></a>Regularmente
Vamos discutir alguns exemplos de desativação periódica:

Exemplo 1: digamos que o desativador faça uma verificação no tempo T (**DeactivationScanInterval**). Sua próxima verificação será em 2T. Suponha que uma ativação do pacote de pacotes ocorreu em T + 1. Esse pacote de pacotes não está hospedando uma réplica e, portanto, precisa ser desativado. Para que o pacote de pacotes seja um candidato para desativação, ele precisa estar no estado de nenhuma réplica por pelo menos T time. Isso significa que ele será elegível para desativação em 2T + 1. Portanto, a verificação em 2T não encontrará esse pacote como um candidato para desativação. O próximo ciclo de desativação 3T agendará este pacote para desativação, pois agora ele não está no estado de réplica para o tempo T.  

Exemplo 2: digamos que um pacote de pacotes é ativado no momento em que T-1 e o desativador faz uma verificação em T. O pacote de pacotes não hospeda uma réplica. Em seguida, na próxima verificação 2T, este pacote de pacotes será encontrado como um candidato para desativação e, portanto, será agendado para desativação.  

Exemplo 3: digamos que um pacote de pacotes seja ativado em T – 1 e o desativador faz uma verificação em T. O pacote de pacotes não hospeda uma réplica ainda. Agora, em T + 1, uma réplica é colocada, ou seja, A hospedagem Obtém um IncrementUsageCount, o que significa que uma réplica é criada. Agora, em 2T, este pacote de pacotes não será agendado para desativação. Como ele contém uma réplica, a desativação será movida para a lógica ReplicaClose explicada abaixo.

Exemplo 4: digamos que seu pacote de pacotes seja grande, digamos 10 GB, e pode demorar um pouco de tempo para baixar no nó. Depois que um aplicativo é ativado, o desativador rastreia seu ciclo de vida. Agora, se você tiver a configuração **DeactivationScanInterval** definida como um valor pequeno, poderá encontrar problemas em que o seu pacote de pacotes não está recebendo tempo de ativação no nó porque o tempo todo foi atingido no download. Para superar o problema, você pode [baixar previamente o pacote de pacotes no nó][p1] ou aumentar o **DeactivationScanInterval**. 

> [!NOTE]
> Um pacote de pacotes pode ser desativado em qualquer lugar entre (**DeactivationScanInterval** a 2 **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * *. 
>

### <a name="replica-close"></a>Fechamento de réplica:
A desativação mantém a contagem de réplicas mantidas por um pacote de pacotes. Se um pacote de pacotes estiver mantendo uma réplica e a réplica estiver fechada/inativa, a hospedagem obterá um DecrementUsageCount. Quando uma réplica é aberta, a hospedagem Obtém um IncrementUsageCount. O decréscimo significa que o pacote de pacotes agora está hospedando uma réplica less e quando a contagem cai para 0, então o pacote de pacotes está agendado para desativação e o tempo após o qual ele será desativado é **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Por exemplo: Vamos dizer que um decréscimo ocorre em T e o pacote está agendado para desativar em 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Se, durante esse tempo, a hospedagem obtiver um IncrementUsage significando que uma réplica foi criada, a desativação será cancelada.

> [!NOTE]
> O que esses parâmetros de configuração significam?
**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: o tempo dado a um pacote de pacotes para hospedar novamente outra réplica depois de ter hospedado qualquer réplica. 
**DeactivationScanInterval**: o tempo mínimo dado ao pacote de pacotes para hospedar uma réplica se nunca tiver hospedado nenhuma réplica, ou seja, Se não for usado.
>

### <a name="ctrlc"></a>Ctrl+C
Após um pacote de pacotes passar o **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** e ainda não estiver hospedando uma réplica, a desativação não poderá ser cancelada. CodePackage são emitidos um manipulador CTRL + C que significa que agora o pipeline de desativação precisa passar por cima para reduzir o processo. Durante esse tempo, se uma nova réplica para o mesmo pacote de pacotes estiver tentando ser colocada, ela falhará porque não podemos fazer a transição da desativação para a ativação.

## <a name="cluster-configs"></a>Configurações de cluster

Configurações com padrões que afetam a ativação/decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: padrão 1. O limite para a contagem de falhas após o qual o FM (Failovermanager) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento.
**ServiceTypeDisableGraceInterval**: padrão de 30 s. Intervalo de tempo após o qual o tipo de serviço pode ser desabilitado.
**ServiceTypeRegistrationTimeout**: padrão de 300 s. O tempo limite para o ServiceType se registrar com Service Fabric.

### <a name="activation"></a>Ativação
**ActivationRetryBackoffInterval**: padrão de 10 segundos Intervalo de retirada em todas as falhas de ativação.
**ActivationMaxFailureCount**: padrão 20. Contagem máxima para a qual o sistema tentará novamente a ativação com falha antes de desistir. 
**ActivationRetryBackoffExponentiationBase**: padrão 1,5.
**ActivationMaxRetryInterval**: padrão de 3600 s. Retirada máxima para ativação em caso de falhas.
**CodePackageContinuousExitFailureResetInterval**: padrão de 300 s. O tempo limite para redefinir a contagem de falhas de saída contínua para CodePackage.

### <a name="download"></a>Baixar
**DeploymentRetryBackoffInterval**: o padrão é 10. Intervalo de retirada da falha de implantação.
**DeploymentMaxRetryInterval**: padrão de 3600 s. Retirada máxima para a implantação em falhas.
**DeploymentMaxFailureCount**: padrão 20. A implantação de aplicativo será repetida por DeploymentMaxFailureCount vezes antes que a implantação desse aplicativo no nó falhe.

### <a name="deactivation"></a>Desativação
**DeactivationScanInterval**: padrão de 600 s. Tempo mínimo dado ao pacote de pacotes para hospedar uma réplica se nunca tiver hospedado nenhuma réplica, ou seja, Se não for usado.
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

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
