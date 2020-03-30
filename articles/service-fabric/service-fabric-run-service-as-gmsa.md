---
title: Execute um serviço de malha de serviço do Azure uma conta gMSA
description: Aprenda a executar um serviço como uma conta de serviço gerenciada em grupo (gMSA) em um cluster autônomo do Service Fabric Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988389"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerenciado de grupo

Em um cluster autônomo do Windows Server, você pode executar um serviço como uma *conta de serviço gerenciado em grupo* (gMSA) usando uma diretiva *RunAs.*  Por padrão, os aplicativos Service Fabric `Fabric.exe` são executados a conta em que o processo é executado. Executar os aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, torna-os mais protegidos uns dos outros. Ao usar uma gMSA, a senha ou senha criptografada não será armazenada no manifesto do aplicativo.  Você também pode executar um serviço como [Grupo ou usuário do Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo a seguir mostra como criar uma conta gMSA chamada *svc-Test$,* como implantar essa conta de serviço gerenciada nos ádeis de cluster e como configurar o principal do usuário.

> [!NOTE]
> O uso de um gMSA com um cluster de malha de serviço autônomo requer o Active Directory no local dentro do seu domínio (em vez do Azure Active Directory (Azure AD)).

Pré-requisitos:

- O domínio precisa de uma chave raiz KDS.
- Deve haver pelo menos um Windows Server 2012 (ou R2) DC no domínio.

1. Crie um administrador de domínio do Active Directory `New-ADServiceAccount` para criar uma conta `PrincipalsAllowedToRetrieveManagedPassword` de serviço gerenciada em grupo usando o cmdlet e certifique-se de que os nós de cluster do Service Fabric incluam todos os nós de cluster de malha de serviço. `AccountName`, `DnsHostName`, e `ServicePrincipalName` deve ser exclusivo.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster do Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure o principal do Usuário `RunAsPolicy` e configure o para fazer referência ao [Usuário](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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

Os seguintes artigos irão guiá-lo sobre os próximos passos:

- [Entenda o modelo de aplicativo](service-fabric-application-model.md)
- [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
- [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
