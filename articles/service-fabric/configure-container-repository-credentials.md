---
title: Azure Service Fabric - Configure credenciais de repositório de contêineres
description: Configure credenciais de repositório para baixar imagens do registro de contêineres
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934985"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configure credenciais de repositório para seu aplicativo para baixar imagens de contêiner

Configure a autenticação `RepositoryCredentials` do `ContainerHostPolicies` registro de contêineres adicionando à seção do manifesto do aplicativo. Adicione a conta e a senha do seu registro de contêiner (*myregistry.azurecr.io* no exemplo abaixo), o que permite que o serviço baixe a imagem do contêiner do repositório.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Recomenda-se que você criptografe a senha do repositório usando um certificado de cifra que é implantado em todos os nós do cluster. Quando o Service Fabric implanta o pacote de serviço para o cluster, o certificado de criptografia é usado para descriptografar o texto cifrado. O cmdlet Invoke-ServiceFabricEncryptText é usado para criar o texto cifrado para a senha, que é adicionado ao arquivo ApplicationManifest.xml.
Consulte [a Gerência Secreta](service-fabric-application-secret-management.md) para obter mais informações sobre certificados e semântica de criptografia.

## <a name="configure-cluster-wide-credentials"></a>Configurar credenciais de todo o cluster

O Service Fabric permite configurar credenciais em todo o cluster que podem ser usadas como credenciais de repositório padrão por aplicativos.

Esse recurso pode ser ativado ou `UseDefaultRepositoryCredentials` desativado `ContainerHostPolicies` adicionando o atributo `true` `false` ao ApplicationManifest.xml com um ou valor.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

O Service Fabric usa as credenciais de repositório padrão `Hosting` que podem ser especificadas no ClusterManifest a seção.  Se `UseDefaultRepositoryCredentials` for `true`, o Service Fabric lerá os seguintes valores de ClusterManifest:

* DefaultContainerRepositoryAccountName (cadeia de caracteres)
* DefaultContainerRepositoryPassword (cadeia de caracteres)
* IsDefaultContainerRepositoryPasswordEncrypted (booliano)
* PadrãoContêinerResitoryPasswordType (string)

Aqui está um exemplo do que `Hosting` pode ser adicionado dentro da seção no arquivo ClusterManifestTemplate.json. A `Hosting` seção pode ser adicionada na criação de cluster ou mais tarde em uma atualização de configuração. Para obter mais informações, consulte [Alterar configurações de cluster do Azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gerenciar segredos de aplicativo do Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Use tokens como credenciais de registro

Service Fabric suporta o uso de tokens como credenciais para baixar imagens para seus contêineres.  Esse recurso aproveita a *identidade gerenciada* da escala de máquina virtual subjacente definida para autenticar no registro, eliminando a necessidade de gerenciamento de credenciais do usuário.  Consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações.  O uso deste recurso requer as etapas a seguir:

1. Certifique-se de que *a identidade gerenciada atribuída pelo sistema* está ativada para a VM.

    ![Portal Azure: Crie opção de identidade de conjunto de escala de máquina virtual](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Conceda permissões à escala de máquina virtual definida para retirar/ler imagens do registro. A partir da lâmina de controle de acesso (IAM) do seu Registro de Contêineres Azure no portal Azure, adicione uma *atribuição de função* para sua máquina virtual:

    ![Adicionar o principal VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Em seguida, modifique o manifesto da aplicação. Na `ContainerHostPolicies` seção, adicione `‘UseTokenAuthenticationCredentials=”true”`o atributo .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > O `UseDefaultRepositoryCredentials` sinalizador definido `UseTokenAuthenticationCredentials` como verdadeiro enquanto é verdadeiro causará um erro durante a implantação.

## <a name="next-steps"></a>Próximas etapas

* Veja mais sobre [a autenticação do registro de contêineres](../container-registry/container-registry-authentication.md).
