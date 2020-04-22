---
title: Loja de segredos centrais de malha de serviço do Azure
description: Este artigo descreve como usar a Central Secrets Store no Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770425"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Loja central de segredos em tecido de serviço azure 
Este artigo descreve como usar a Central Secrets Store (CSS) no Azure Service Fabric para criar segredos em aplicativos de Malha de Serviço. CSS é um cache de loja secreta local que mantém dados confidenciais, como senha, tokens e chaves, criptografados na memória.

## <a name="enable-central-secrets-store"></a>Habilite a Loja Central secrets
Adicione o script a seguir `fabricSettings` à configuração de cluster para ativar o CSS. Recomendamos que você use um certificado diferente de um certificado de cluster para CSS. Certifique-se de que o certificado de criptografia `NetworkService` está instalado em todos os nós e que tenha permissão de leitura para a chave privada do certificado.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Declare um recurso secreto
Você pode criar um recurso secreto usando a API REST.
  > [!NOTE] 
  > Se o cluster estiver usando a autenticação do windows, a solicitação REST será enviada por um canal HTTP não seguro. A recomendação é usar um cluster baseado em X509 com pontos finais seguros.

Para criar `supersecret` um recurso secreto usando a API `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`REST, faça uma solicitação PUT para . Você precisa do certificado de cluster ou do certificado de cliente administrador para criar um recurso secreto.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Defina o valor secreto

Use o script a seguir para usar a API REST para definir o valor secreto.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Examine o valor secreto
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Use o segredo em sua aplicação

Siga estas etapas para usar o segredo em seu aplicativo Service Fabric.

1. Adicione uma seção no arquivo **settings.xml** com o seguinte trecho. Observe aqui que o valor`secretname:version`está no formato { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importe a seção em **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   A variável `SecretPath` ambiente apontará para o diretório onde todos os segredos são armazenados. Cada parâmetro listado `testsecrets` na seção é armazenado em um arquivo separado. O aplicativo agora pode usar o segredo da seguinte forma:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monte os segredos em um contêiner. A única mudança necessária para disponibilizar os `specify` segredos dentro `<ConfigPackage>`do contêiner é um ponto de montagem em .
O trecho a seguir é o **aplicativo modificadoManifest.xml**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Segredos estão disponíveis sob o ponto de montagem dentro do seu recipiente.

1. Você pode vincular um segredo a uma `Type='SecretsStoreRef`variável de ambiente de processo especificando . O trecho a seguir é um exemplo `supersecret` `ver1` de como `MySuperSecret` vincular a versão à variável ambiente em **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [segurança de aplicativos e serviços.](service-fabric-application-and-service-security.md)
