---
title: Testes de malha de serviço azure
description: Como modelar o Liveness Probe no Azure Service Fabric usando arquivos manifestos de aplicativos e serviços.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431209"
---
# <a name="liveness-probe"></a>Sonda de vida
A partir do 7.1 Service Fabric suporta o mecanismo Liveness Probe para aplicações [contêiner.][containers-introduction-link] Liveness Probe ajuda a anunciar a vida do aplicativo contêiner e quando eles não respondem em tempo hábil, isso resultará em uma reinicialização.
Este artigo fornece uma visão geral de como definir um Liveness Probe através de arquivos manifestos.

Antes de prosseguir com este artigo, recomendamos familiarizar-se com o [modelo de aplicação service fabric][application-model-link] e o modelo de [hospedagem Service Fabric][hosting-model-link].

> [!NOTE]
> O Liveness Probe só é suportado para contêineres no modo de rede NAT.

## <a name="semantics"></a>Semântica
Você pode especificar apenas 1 Sonda de Vivacidade por contêiner e pode controlar seu comportamento com esses campos:

* `initialDelaySeconds`: O atraso inicial em segundos para começar a executar a sonda assim que o contêiner tiver sido iniciado. O valor suportado é int. A inadimplência é 0. O mínimo é 0.

* `timeoutSeconds`: Período em segundos após o qual consideramos a sonda como falha se não tiver sido concluída com sucesso. O valor suportado é int. O padrão é 1. O mínimo é 1.

* `periodSeconds`: Período em segundos para especificar com que frequência sondamos. O valor suportado é int. O padrão é 10. O mínimo é 1.

* `failureThreshold`: Assim que atingirmos failurethreshold, o contêiner será reiniciado. O valor suportado é int. O padrão é 3. O mínimo é 1.

* `successThreshold`: No fracasso, para que a sonda seja considerada sucesso, ela tem que ser executada com sucesso para SuccessThreshold. O valor suportado é int. O padrão é 1. O mínimo é 1.

Haverá no máximo 1 sonda para contêiner em um momento. Se a sonda não for concluída no **tempo limiteSegundos** continuamos esperando e contando-o para o **limite de falha**. 

Além disso, o ServiceFabric aumentará após os [relatórios de saúde do][health-introduction-link] teste no DeployedServicePackage:

* `Ok`: Se a sonda tiver **sucessoLimite,** então reportamos saúde como Ok.

* `Error`: Se a falha do testeCount == **failureThreshold**, antes de reiniciar o contêiner, reportamos erro.

* `Warning`: 
    1. Se o teste falhar e a falhaCount < **failureThreshold,** informamos aviso. Este relatório de saúde permanece até que a contagem de falhas atinja **o limite** ou **o sucessoThreshold**.
    2. No sucesso pós-fracasso, ainda reportamos Aviso, mas com sucesso consecutivo atualizado.

## <a name="specifying-liveness-probe"></a>Especificando a sonda Liveness

Você pode especificar o teste no ApplicationManifest.xml em ServiceManifestImport:

A sonda pode qualquer um de:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Sonda HTTP

Para o teste HTTP, o Service Fabric enviará uma solicitação HTTP para a porta e o caminho especificado. O código de retorno maior ou igual a 200 e menos de 400 indica sucesso.

Aqui está um exemplo de como especificar o teste HttpGet:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

O teste HttpGet tem propriedades adicionais que você pode definir:

* `path`: Caminho para acesso na solicitação HTTP.

* `port`: Porta para acesso a sondas. O alcance é de 1 a 65535. Mandatory.

* `scheme`: Esquema a ser usado para conectar-se ao pacote de código. Se definido como HTTPS, a verificação do certificado será ignorada. Padrão para HTTP

* `httpHeader`: Cabeçalhos a definir no pedido. Você pode especificar vários desses.

* `host`: IP do host para conectar.

## <a name="tcp-probe"></a>Sonda TCP

Para a sonda TCP, a Service Fabric tentará abrir um soquete no contêiner com a porta especificada. Se ele pode estabelecer uma conexão, a sonda é considerada sucesso. Aqui está um exemplo de como especificar o teste que usa o soquete TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Sonda Exec

Esta sonda emitirá um executivo no contêiner e aguardará a conclusão do comando.

> [!NOTE]
> O comando exec leva uma seqüência de sementes de comma. O comando a seguir, no exemplo, funcionará para o contêiner Linux.
> Se você estiver testando o recipiente do windows, use <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir para obter informações relacionadas.
* [Malha de serviço e contêineres.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

