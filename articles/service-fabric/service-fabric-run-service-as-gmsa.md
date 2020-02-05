---
title: Executar um serviço de Service Fabric do Azure em uma conta do gMSA
description: Saiba como executar um serviço como uma conta de serviço gerenciado por grupo (gMSA) em um Cluster Service Fabric Windows autônomo.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988389"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerenciado de grupo

Em um cluster autônomo do Windows Server, você pode executar um serviço como uma *conta de serviço gerenciado de grupo* (gMSA) usando uma política *runas* .  Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o processo de `Fabric.exe` é executado. Executar os aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, torna-os mais protegidos uns dos outros. Ao usar uma gMSA, a senha ou senha criptografada não será armazenada no manifesto do aplicativo.  Você também pode executar um serviço como [Grupo ou usuário do Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo a seguir mostra como criar uma conta do gMSA chamada *svc-Test $* , como implantar essa conta de serviço gerenciado nos nós do cluster e como configurar a entidade de usuário.

> [!NOTE]
> Usar um gMSA com um Cluster Service Fabric autônomo requer Active Directory local em seu domínio (em vez de Azure Active Directory (AD do Azure)).

Pré-requisitos:

- O domínio precisa de uma chave raiz KDS.
- Deve haver pelo menos um DC do Windows Server 2012 (ou R2) no domínio.

1. Ter um administrador de domínio Active Directory criar uma conta de serviço gerenciado por grupo usando o cmdlet `New-ADServiceAccount` e verificar se o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de Cluster Service Fabric. `AccountName`, `DnsHostName`, e `ServicePrincipalName` deve ser exclusivo.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster do Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure a entidade de usuário e configure o `RunAsPolicy` para fazer referência ao [usuário](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Os artigos a seguir irão orientá-lo nas próximas etapas:

- [Entenda o modelo de aplicativo](service-fabric-application-model.md)
- [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
- [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
