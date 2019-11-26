---
title: Implementar a sincronização de hash de senha com o Azure AD Connect | Microsoft Docs
description: Fornece informações sobre como funciona a sincronização de hash de senha e como configurá-la.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5ca62bc032c12c568e2b8065630dcd8b687513
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483109"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implemente a sincronização de hash de senha com a sincronização do Azure AD Connect
Este artigo fornece as informações necessárias para sincronizar suas senhas de usuário de uma instância do AD (Active Directory) local para uma instância do Azure AD (Azure Active Directory) baseada na nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como a sincronização de hash de senha funciona
O serviço de domínio do Active Directory armazena senhas na forma de uma representação de valor de hash da senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o *algoritmo de hash*). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. 

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário da instância do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. As senhas são sincronizadas por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de hash de senha é semelhante à sincronização de dados de usuário. Contudo, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma senha, ela substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de hash de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar. However, if there are multiple connectors, it is possible to disable password hash sync for some connectors but not others using the [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) cmdlet.

Quando você altera uma senha local, a senha atualizada é sincronizada, geralmente em questão de minutos.
O recurso de sincronização de hash de senha repete automaticamente tentativas de sincronização com falha. Se ocorrer um erro durante uma tentativa de sincronização de senha, um erro é registrado no seu visualizador de eventos.

A sincronização de uma senha não afeta um usuário conectado atualmente.
A sessão de serviço de nuvem atual não será imediatamente afetada por uma alteração de senha sincronizada que ocorrer enquanto você estiver conectado no serviço de nuvem. No entanto, quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

O usuário deve inserir suas credenciais corporativas uma segunda vez para autenticar no Azure AD, independentemente se ele está conectado à rede corporativa. Porém, esse padrão poderá ser minimizado, se o usuário marcar a caixa de seleção de KMSI (Manter-me conectado) ao entrar. Essa seleção define um cookie de sessão que ignora a autenticação por 180 dias. O comportamento KMSI pode ser habilitado ou desabilitado pelo administrador do Azure AD. Além disso, é possível reduzir os prompts de senha ativando o [SSO Contínuo](how-to-connect-sso.md), que conecta os usuários automaticamente quando estão em dispositivos corporativos conectados à rede corporativa.

> [!NOTE]
> Somente há suporte para a sincronização de senha para o usuário do tipo de objeto no Active Directory. Não há suporte para o tipo de objeto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrição detalhada de como funciona a sincronização de hash senha

A seção a seguir descreve detalhadamente como a sincronização de hash de senha funciona entre o Azure AD e Active Directory.

![Fluxo da senha detalhado](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. A cada dois minutos, o agente de sincronização de hash de senha no servidor do AD Connect solicita hashes de senha armazenados (o atributo unicodePwd) de um DC.  Essa solicitação é feita através do protocolo de replicação padrão [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usado para sincronizar dados entre DCs. A conta do serviço deve ter as permissões do AD Replicar Alterações de Diretório e Replicar Todas as Alterações de Diretório (concedidas por padrão na instalação) para obter os hashes de senha.
2. Antes de enviar, o controlador de domínio criptografa o hash de senha MD4 usando uma chave que é um hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) da chave de sessão RPC e um valor de sal. Em seguida, ele envia o resultado para o agente de sincronização de hash de senha por RPC. O controlador de domínio também passa o sal para o agente de sincronização usando o protocolo de replicação do controlador de domínio, para que o agente possa descriptografar o envelope.
3. Depois que o agente de sincronização de hash de senha tiver o envelope criptografado, ele usará [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e o sal para gerar uma chave para descriptografar os dados recebidos de volta para seu formato original de MD4. O agente de sincronização de hash de senha nunca tem acesso à senha de texto não criptografado. Uso que o agente de sincronização de hash de senha faz do MD5 é estritamente para compatibilidade de protocolo de replicação com o controlador de domínio, e é usado somente no local entre o controlador de domínio e o agente de sincronização de hash de senha.
4. O agente de sincronização de hash de senha expande o hash de senha binária de 16 bits para 64 bytes convertendo primeiro o hash em uma cadeia hexadecimal de 32 bytes, depois convertendo essa cadeia de caracteres de volta para o binário com a codificação UTF-16.
5. O agente de sincronização de hash de senha adiciona um sal por usuário – que é composto por um sal de 10 bytes – ao binário de 64 bytes a fim de proteger ainda mais o hash original.
6. Em seguida, o agente de sincronização de hash de senha combina o hash MD4 e o sal por usuário e usa o resultado como entrada na função [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). São usadas 1000 iterações do algoritmo de hash com chave [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx). 
7. O agente de sincronização de hash de senha usa o hash resultante de 32 bytes, concatena o sal por usuário e o número de iterações SHA256 com ele (para uso pelo Azure AD) e, em seguida, transmite a cadeia de caracteres do Azure AD Connect para o Azure AD por SSL.</br> 
8. Quando um usuário tenta entrar no Azure AD e insere sua senha, a senha é executada por meio do mesmo processo de MD4 + sal + PBKDF2 + HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado no Azure AD, isso significa que o usuário digitou a senha correta e será autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado no Azure AD for obtido, ele não poderá ser usada em um ataque de passagem de hash no local.

### <a name="security-considerations"></a>Considerações de segurança

Ao sincronizar senhas, a versão da sua senha em texto sem formatação não é exposta ao recurso de sincronização de hash de senha, nem ao Azure AD ou qualquer um dos serviços associados.

A autenticação do usuário ocorre no Azure AD e não na própria instância do Active Directory da organização. Os dados de senha SHA256 armazenados no Azure AD - um hash do hash MD4 original - são mais seguros que os armazenados no Active Directory. Além disso, como não é possível descriptografar esse hash SHA256, ele não pode ser levado de volta ao ambiente do Active Directory da organização e apresentado como uma senha de usuário válida em um ataque de passagem de hash.

### <a name="password-policy-considerations"></a>Considerações sobre política de senha

Há dois tipos de políticas de senha que são afetadas ao habilitar a sincronização de hash de senha:

* Política de complexidade de senha
* Política de expiração de senha

#### <a name="password-complexity-policy"></a>Política de complexidade de senha

Quando a sincronização de hash de senha é habilitada, as políticas de complexidade de senha na instância do Active Directory local substituem as políticas de complexidade na nuvem para usuários sincronizados. Você pode usar todas as senhas válidas da sua instância do Active Directory local para acessar os serviços do Azure AD.

> [!NOTE]
> Senhas de usuários criadas diretamente na nuvem ainda estão sujeitas a políticas de senha, conforme definido na nuvem.

#### <a name="password-expiration-policy"></a>Política de expiração de senha

If a user is in the scope of password hash synchronization, by default the cloud account password is set to *Never Expire*.

Você pode continuar entrando nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Public preview of the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature

If there are synchronized users that only interact with Azure AD integrated services and must also comply with a password expiration policy, you can force them to comply with your Azure AD password expiration policy by enabling the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature.

When *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is disabled (which is the default setting), Azure AD Connect sets the PasswordPolicies attribute of synchronized users to "DisablePasswordExpiration". This is done every time a user's password is synchronized and instructs Azure AD to ignore the cloud password expiration policy for that user. You can check the value of the attribute using the Azure AD PowerShell module with the following command:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


To enable the EnforceCloudPasswordPolicyForPasswordSyncedUsers feature, run the following command using the MSOnline PowerShell module:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

Once enabled, Azure AD does not go to each synchronized user to remove the `DisablePasswordExpiration` value from the PasswordPolicies attribute. Instead, the value is set to `None` during the next password sync for each user when they next change their password in on-premises AD.  

It is recommended to enable EnforceCloudPasswordPolicyForPasswordSyncedUsers, prior to enabling password hash sync, so that the initial sync of password hashes does not add the `DisablePasswordExpiration` value to the PasswordPolicies attribute for the users.

The default Azure AD password policy requires users to change their passwords every 90 days. If your policy in AD is also 90 days, the two policies should match. However, if the AD policy is not 90 days, you can update the Azure AD password policy to match by using the Set-MsolPasswordPolicy PowerShell command.

Azure AD supports a separate password expiration policy per registered domain.

Caveat: If there are synchronized accounts that need to have non-expiring passwords in Azure AD, you must explicitly add the `DisablePasswordExpiration` value to the PasswordPolicies attribute of the user object in Azure AD.  You can do this by running the following command.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Public Preview of synchronizing temporary passwords and "Force Password on Next Logon"

It is typical to force a user to change their password during their first logon, especially after an admin password reset occurs.  It is commonly known as setting a "temporary" password and is completed by checking the "User must change password at next logon" flag on a user object in Active Directory (AD).
  
The temporary password functionality helps to ensure that the transfer of ownership of the credential is completed on first use, to minimize the duration of time in which more than one individual has knowledge of that credential.

To support temporary passwords in Azure AD for synchronized users, you can enable the *ForcePasswordResetOnLogonFeature* feature, by running the following command on your Azure AD Connect server, replacing <AAD Connector Name> with the connector name specific to your environment:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

You can use the following command to determine the connector name:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

Caveat:  Forcing a user to change their password on next logon requires a password change at the same time.  AD Connect will not pick up the force password change flag by itself, it is supplemental to the detected password change that occurs during password hash sync.

> [!CAUTION]
> If you do not enable Self-service Password Reset (SSPR) in Azure AD users will have a confusing experience when they reset their password in Azure AD and then attempt to sign in in Active Directory with the new password, as the new password isn’t valid in Active Directory. You should only use this feature when SSPR and Password Writeback is enabled on the tenant.

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="account-expiration"></a>Expiração da conta

Se a organização estiver usando o atributo accountExpires como parte do gerenciamento de conta de usuário, esse atributo não será sincronizado com o Azure AD. Como resultado, uma conta expirada do Active Directory em um ambiente configurado para sincronização de hash de senha ainda estará ativa no Azure AD. Recomendamos que, se a conta expirar, uma ação de fluxo de trabalho dispare um script do PowerShell que desabilite a conta de usuário do Azure AD (use o cmdlet [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)). Por outro lado, quando a conta for ativada, a instância do Azure AD deverá ser ativada.

### <a name="overwrite-synchronized-passwords"></a>Substituir senhas sincronizadas

Um administrador pode redefinir sua senha manualmente usando o Windows PowerShell.

Nesse caso, a nova senha substitui a senha sincronizada e todas as políticas de senha definidas na nuvem se aplicam à nova senha.

Se você alterar a senha local novamente, a nova senha será sincronizada com a nuvem e substituirá a senha atualizada manualmente.

A sincronização de uma senha não afeta um usuário do Azure que está conectado. Sua sessão de serviço de nuvem atual não será afetada imediatamente por uma alteração de senha sincronizada que ocorre enquanto você está conectado a um serviço de nuvem. KMSI ampliará a duração dessa diferença. Quando o serviço de nuvem exigir que você se autentique novamente, será necessário fornecer a nova senha.

### <a name="additional-advantages"></a>Vantagens adicionais

- Em geral, a sincronização de hash de senha é mais simples de implementar do que um serviço de federação. Ela não exige quaisquer servidores adicionais e elimina a dependência de um serviço de federação altamente disponível para autenticar usuários.
- A sincronização de hash de senha também pode ser habilitada além da federação. Ela pode ser usada como um fallback se seu serviço de federação sofrer uma interrupção.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Password hash sync process for Azure AD Domain Services

If you use Azure AD Domain Services to provide legacy authentication for applications and services that need to use Keberos, LDAP, or NTLM, some additional processes are part of the password hash synchronization flow. Azure AD Connect uses the additional following process to synchronize password hashes to Azure AD for use in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect should only be installed and configured for synchronization with on-premises AD DS environments. It's not supported to install Azure AD Connect in an Azure AD DS managed domain to synchronize objects back to Azure AD.
>
> Azure AD Connect only synchronizes legacy password hashes when you enable Azure AD DS for your Azure AD tenant. The following steps aren't used if you only use Azure AD Connect to synchronize an on-premises AD DS environment with Azure AD.
>
> If your legacy applications don't use NTLM authentication or LDAP simple binds, we recommend that you disable NTLM password hash synchronization for Azure AD DS. For more information, see [Disable weak cipher suites and NTLM credential hash synchronization](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect retrieves the public key for the tenant's instance of Azure AD Domain Services.
1. When a user changes their password, the on-premises domain controller stores the result of the password change (hashes) in two attributes:
    * *unicodePwd* for the NTLM password hash.
    * *supplementalCredentials* for the Kerberos password hash.
1. Azure AD Connect detects password changes through the directory replication channel (attribute changes needing to replicate to other domain controllers).
1. For each user whose password has changed, Azure AD Connect performs the following steps:
    * Generates a random AES 256-bit symmetric key.
    * Generates a random initialization vector needed for the first round of encryption.
    * Extracts Kerberos password hashes from the *supplementalCredentials* attributes.
    * Checks the Azure AD Domain Services security configuration *SyncNtlmPasswords* setting.
        * If this setting is disabled, generates a random, high-entropy NTLM hash (different from the user's password). This hash is then combined with the exacted Kerberos password hashes from the *supplementalCrendetials* attribute into one data structure.
        * If enabled, combines the value of the *unicodePwd* attribute with the extracted Kerberos password hashes from the *supplementalCredentials* attribute into one data structure.
    * Encrypts the single data structure using the AES symmetric key.
    * Encrypts the AES symmetric key using the tenant's Azure AD Domain Services public key.
1. Azure AD Connect transmits the encrypted AES symmetric key, the encrypted data structure containing the password hashes, and the initialization vector to Azure AD.
1. Azure AD stores the encrypted AES symmetric key, the encrypted data structure, and the initialization vector for the user.
1. Azure AD pushes the encrypted AES symmetric key, the encrypted data structure, and the initialization vector using an internal synchronization mechanism over an encrypted HTTP session to Azure AD Domain Services.
1. Azure AD Domain Services retrieves the private key for the tenant's instance from Azure Key vault.
1. For each encrypted set of data (representing a single user's password change), Azure AD Domain Services then performs the following steps:
    * Uses its private key to decrypt the AES symmetric key.
    * Uses the AES symmetric key with the initialization vector to decrypt the encrypted data structure that contains the password hashes.
    * Writes the Kerberos password hashes it receives to the Azure AD Domain Services domain controller. The hashes are saved into the user object's *supplementalCredentials* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.
    * Azure AD Domain Services writes the NTLM password hash it received to the Azure AD Domain Services domain controller. The hash is saved into the user object's *unicodePwd* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.

## <a name="enable-password-hash-synchronization"></a>Permitir a sincronização de hash de senha

>[!IMPORTANT]
>Se você estiver migrando do AD FS (ou outras tecnologias de federação) para Sincronização de Hash de Senha, recomendamos fortemente seguir nosso guia detalhado de implantação publicado [aqui](https://aka.ms/adfstophsdpdownload).

Quando você instala o Azure AD Connect usando as **Configurações Expressas**, a sincronização de hash de senha é habilitada automaticamente. Para obter mais informações, consulte [Introdução ao Azure AD Connect usando configurações expressas](how-to-connect-install-express.md).

Se você usar configurações personalizadas quando instalar o Azure AD Connect, a sincronização de hash de senha estará disponível na página de entrada do usuário. Para saber mais, confira [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

![Permitindo a sincronização de hash de senha](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronização de hash de senha e FIPS
Se o servidor tiver sido bloqueado de acordo com o FIPS (Federal Information Processing Standard), isso significará que o MD5 foi desabilitado.

**Para habilitar MD5 para a sincronização de hash de senha, execute as seguintes etapas:**

1. Vá para %programfiles%\Azure AD Sync\Bin.
2. Abra miiserver.exe.config.
3. Acesse o nó configuração/runtime no fim do arquivo.
4. Adicione o seguinte nó: `<enforceFIPSPolicy enabled="false"/>`
5. Salve suas alterações.

Para referência, esse snippet mostra como deve ser a aparência:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre segurança e FIPS, consulte [Sincronização de hash de senha do Azure, criptografia e conformidade FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Solução de Problemas de Sincronização de hash de Senha
Caso tenha problemas com a sincronização de hash de senha, veja [Solucionar problemas de sincronização de hash de senha](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Próximos passos
* [Sincronização do Azure AD Connect: personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
* [Obter um plano de implantação passo a passo para migração do AD FS para sincronização de Hash de senha](https://aka.ms/authenticationDeploymentPlan)
