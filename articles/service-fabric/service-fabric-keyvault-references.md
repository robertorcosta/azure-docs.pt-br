---
title: Tecido de serviço do Azure - Usando referências do aplicativo KeyVault do aplicativo de malha de serviço
description: Este artigo explica como usar o suporte keyvaultreference de malha de serviço para segredos de aplicativos.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545476"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Suporte keyVaultReference para aplicativos de malha de serviço (visualização)

Um desafio comum ao criar aplicativos em nuvem é como armazenar com segurança segredos exigidos pelo seu aplicativo. Por exemplo, você pode querer armazenar as credenciais do repositório de contêiner no cofre de chaves e referencia-lo no manifesto do aplicativo. A Tecla de Malha de ServiçoVaultReference usa a identidade gerenciada do fabric de serviço e facilita a referência aos segredos do cofre de chaves. O restante deste artigo detalha como usar o Service Fabric KeyVaultReference e inclui alguns usos típicos.

## <a name="prerequisites"></a>Pré-requisitos

- Identidade gerenciada para aplicativo (MIT)
    
    O suporte ao Service Fabric KeyVaultReference usa a Identidade Gerenciada do aplicativo e, portanto, os aplicativos que planejam usar o KeyVaultReferences devem usar identidade gerenciada. Siga este [documento](concepts-managed-identity.md) para habilitar a identidade gerenciada para sua aplicação.

- Loja Central Secrets (CSS).

    Central Secrets Store (CSS) é o cache de segredos locais criptografados da Service Fabric. CSS é um cache de loja secreta local que mantém dados confidenciais, como senha, tokens e chaves, criptografados na memória. KeyVaultReference, uma vez buscado, são armazenados em cache no CSS.

    Adicione o abaixo à `fabricSettings` configuração do cluster para habilitar todos os recursos necessários para o suporte ao KeyVaultReference.

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
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Recomenda-se usar um certificado de criptografia separado para CSS. Você pode adicioná-lo na seção "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Para que as alterações entrem em vigor, você também precisará alterar a política de upgrade para especificar uma reinicialização vigorosa do tempo de execução do Service Fabric em cada nó à medida que a atualização progride através do cluster. Essa reinicialização garante que o serviço do sistema recém-habilitado seja iniciado e executado em cada nó. No trecho abaixo, forceRestart é a configuração essencial; use seus valores existentes para o restante das configurações.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Permissão de acesso de identidade gerenciada do aplicativo de concessão ao cofre de chaves

    Consulte este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerenciado ao cofre de chaves. Observe também se você estiver usando a Identidade Gerenciada Atribuída ao Sistema, a identidade gerenciada será criada somente após a implantação do aplicativo.

## <a name="keyvault-secret-as-application-parameter"></a>Chave secreta do cofre como parâmetro de aplicação
Digamos que o aplicativo precisa ler a senha do banco de dados backend armazenada no cofre de chaves, o suporte ao Service Fabric KeyVaultReference facilita. Abaixo o `DBPassword` exemplo lê o segredo do cofre de chaves usando o suporte service fabric keyVaultReference.

- Adicionar uma seção às configurações.xml

    Definir `DBPassword` parâmetro com `KeyVaultReference` tipo e valor`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Consulte a nova seção em ApplicationManifest.xml in`<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Usando keyvaultReference em seu aplicativo

    A instanciação do service Fabric on service resolverá o Parâmetro KeyVaultReference usando a identidade gerenciada do aplicativo. Cada parâmetro listado `<Section  Name=dbsecrets>` será um arquivo a pasta apontada pelo EnvironmentVariable SecretPath. Abaixo c# trecho de código mostrar como ler DBPassword em seu aplicativo.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Para o cenário do contêiner, você pode `secrets` usar o MountPoint para controlar onde o será montado.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault secreto como variável de ambiente

As variáveis de ambiente service fabric agora suportam o tipo KeyVaultReference, abaixo exemplo mostra como vincular uma variável de ambiente a um segredo armazenado no KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segredo do cofre de chaves como senha do repositório de contêineres
KeyVaultReference é um tipo suportado para repositório de contêineresCredenciais, abaixo exemplo mostra como usar uma referência de cofre de chaves como senha de repositório de contêiner.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Perguntas frequentes
- A identidade gerenciada precisa ser ativada para o suporte ao KeyVaultReference, a ativação do aplicativo falhará se o KeyVaultReference for usado sem habilitar identidade gerenciada.

- Se você estiver usando a identidade atribuída ao sistema, ela será criada somente após a implantação do aplicativo e isso cria uma dependência circular. Uma vez que seu aplicativo seja implantado, você pode conceder ao sistema permissão de acesso de identidade atribuído ao cofre de chaves. Você pode encontrar a identidade atribuída ao sistema pelo nome {cluster}/{nome do aplicativo}/{servicename}

- O cofre de chaves precisa estar na mesma assinatura do cluster de malha de serviço. 

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure KeyVault](https://docs.microsoft.com/azure/key-vault/)
