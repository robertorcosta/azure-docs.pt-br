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
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331361"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implemente a sincronização de hash de senha com a sincronização do Azure AD Connect
Este artigo fornece as informações necessárias para sincronizar suas senhas de usuário de uma instância do AD (Active Directory) local para uma instância do Azure AD (Azure Active Directory) baseada na nuvem.

## <a name="how-password-hash-synchronization-works"></a>Como a sincronização de hash de senha funciona
O serviço de domínio do Active Directory armazena senhas na forma de uma representação de valor de hash da senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o *algoritmo de hash*). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. 

Para sincronizar sua senha, a sincronização do Azure AD Connect extrai o hash da senha de usuário da instância do Active Directory local. O processamento de segurança adicional é aplicado ao hash da senha antes que ele seja sincronizado com o serviço de autenticação do Azure Active Directory. As senhas são sincronizadas por usuário e em ordem cronológica.

O fluxo de dados real do processo de sincronização de hash de senha é semelhante à sincronização de dados de usuário. Contudo, as senhas são sincronizadas com mais frequência do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de hash senha é executado a cada 2 minutos. Não é possível modificar a frequência desse processo. Ao sincronizar uma senha, ela substituirá a senha de nuvem existente.

Na primeira vez que você habilitar o recurso de sincronização de hash de senha, ele executará uma sincronização inicial das senhas de todos os usuários no escopo. Você não pode definir explicitamente um subconjunto de senhas de usuário que deseja sincronizar. No entanto, se houver vários conectores, é possível desativar a sincronização de hash de senha para alguns conectores, mas não outros usando o [cmdlet Set-ADSyncAADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

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
7. O agente de sincronização hash de senha pega o hash de 32 bytes resultante, concatena tanto o sal por usuário quanto o número de iterações SHA256 para ele (para uso pelo Azure AD), em seguida, transmite a seqüência do Azure AD Connect para Azure AD sobre TLS.</br> 
8. Quando um usuário tenta entrar no Azure AD e insere sua senha, a senha é executada por meio do mesmo processo de MD4 + sal + PBKDF2 + HMAC-SHA256. Se o hash resultante corresponder ao hash armazenado no Azure AD, isso significa que o usuário digitou a senha correta e será autenticado.

> [!NOTE]
> O hash MD4 original não é transmitido para o Azure AD. Em vez disso, o hash SHA256 do hash MD4 original é transmitido. Como resultado, se o hash armazenado no Azure AD for obtido, ele não poderá ser usada em um ataque de passagem de hash no local.

### <a name="security-considerations"></a>Considerações sobre segurança

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

Se um usuário estiver no escopo da sincronização de hash de senha, por padrão, a senha da conta na nuvem será definida como *Never Expire*.

Você pode continuar entrando nos serviços de nuvem usando uma senha sincronizada que expirou no seu ambiente local. A senha de nuvem será atualizada na próxima vez que você alterar a senha no ambiente local.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Visualização pública do recurso *EnforceCloudPasswordPasswordForPasswordSyncedUsers*

Se houver usuários sincronizados que só interagem com os serviços integrados do Azure AD e também devem cumprir uma política de expiração de senha, você pode forçá-los a cumprir a política de expiração de senha do Azure AD, ativando o recurso *EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

Quando *enforceCloudPasswordPolicyForPasswordSyncedUsers* é desativado (que é a configuração padrão), o Azure AD Connect define o atributo PasswordPolicies dos usuários sincronizados como "DisablePasswordExpiration". Isso é feito toda vez que a senha do usuário é sincronizada e instrui o Azure AD a ignorar a política de expiração de senha sustal para esse usuário. Você pode verificar o valor do atributo usando o módulo Azure AD PowerShell com o seguinte comando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Para habilitar o recurso EnforceCloudPasswordPolicyForPasswordSyncedUsers, execute o seguinte comando usando o módulo MSOnline PowerShell, conforme mostrado abaixo. Você teria que digitar sim para o parâmetro Ativar como mostrado abaixo:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Uma vez ativado, o Azure AD não vai `DisablePasswordExpiration` para cada usuário sincronizado para remover o valor do atributo PasswordPolicies. Em vez disso, `None` o valor é definido para durante a próxima sincronização de senha para cada usuário quando eles mudam sua senha no AD local.  

Recomenda-se ativar o EnforceCloudPasswordPolicyForPasswordSyncedUsers, antes de ativar a sincronização de hash de senha, `DisablePasswordExpiration` para que a sincronização inicial dos hashes de senha não adicione o valor ao atributo PasswordPolicies para os usuários.

A política padrão de senha do Azure AD exige que os usuários alterem suas senhas a cada 90 dias. Se sua apólice em AD também for de 90 dias, as duas políticas devem coincidir. No entanto, se a política de Anúncio não for de 90 dias, você pode atualizar a diretiva de senha Do Azure AD para combinar usando o comando Set-MsolPasswordPolicy PowerShell.

O Azure AD suporta uma política de expiração de senha separada por domínio registrado.

Ressalva: Se houver contas sincronizadas que precisam ter senhas não expiradas no Azure `DisablePasswordExpiration` AD, você deve adicionar explicitamente o valor ao atributo PasswordPolicies do objeto de usuário no Azure AD.  Você pode fazer isso executando o seguinte comando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Este recurso está em Public Preview agora.
> O comando Set-MsolPasswordPolicy PowerShell não funcionará em domínios federados. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Visualização pública da sincronização de senhas temporárias e "Force Password Change on Next Logon"

É típico forçar um usuário a alterar sua senha durante seu primeiro logon, especialmente depois que uma redefinição de senha de admin ocorre.  É comumente conhecido como definir uma senha "temporária" e é concluído verificando o sinalizador "O usuário deve alterar a senha no próximo logon" em um objeto de usuário no Active Directory (AD).
  
A funcionalidade temporária da senha ajuda a garantir que a transferência de propriedade da credencial seja concluída no primeiro uso, para minimizar a duração do tempo em que mais de um indivíduo tem conhecimento dessa credencial.

Para suportar senhas temporárias no Azure AD para usuários sincronizados, você pode ativar o recurso *ForcePasswordChangeOnLogOn,* executando o seguinte comando no servidor Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Forçar um usuário a alterar sua senha no próximo logon requer uma alteração de senha ao mesmo tempo.  O Azure AD Connect não pegará o sinalizador de mudança de senha de força por si só; é suplementar à alteração de senha detectada que ocorre durante a sincronização de hash de senha.

> [!CAUTION]
> Você só deve usar esse recurso quando o SSPR e a Password Writeback estiverem ativados no inquilino.  Isso é para que, se um usuário alterar sua senha via SSPR, ela será sincronizada com o Active Directory.

> [!NOTE]
> Este recurso está em visualização pública agora.

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

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo de sincronização de hash de senha para serviços de domínio Azure AD

Se você usar o Azure AD Domain Services para fornecer autenticação herdada para aplicativos e serviços que precisam usar Kerberos, LDAP ou NTLM, alguns processos adicionais fazem parte do fluxo de sincronização de hash de senha. O Azure AD Connect usa o seguinte processo adicional para sincronizar hashes de senha com o Azure AD para uso no Azure AD Domain Services:

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.
>
> O Azure AD Connect só sincroniza hashes de senha legados quando você habilita o Azure AD DS para o seu inquilino Azure AD. As etapas a seguir não são usadas se você usar apenas o Azure AD Connect para sincronizar um ambiente AD DS no local com o Azure AD.
>
> Se seus aplicativos legados não usarem autenticação NTLM ou obrigações simples lDAP, recomendamos que você desative a sincronização de hash de senha NTLM para o Azure AD DS. Para obter mais informações, consulte [Desativar suítes de cifra fraca e sincronização de hash de credencial NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. O Azure AD Connect recupera a chave pública para a instância do inquilino dos Serviços de Domínio AD do Azure.
1. Quando um usuário altera sua senha, o controlador de domínio local armazena o resultado da alteração de senha (hashes) em dois atributos:
    * *unicodePwd* para o hash de senha NTLM.
    * *credenciais suplementares* para o hash de senha kerberos.
1. O Azure AD Connect detecta alterações de senha através do canal de replicação do diretório (alterações de atributo que precisam ser replicadas para outros controladores de domínio).
1. Para cada usuário cuja senha foi alterada, o Azure AD Connect executa as seguintes etapas:
    * Gera uma chave simétrica aes aleatória de 256 bits.
    * Gera um vetor de inicialização aleatório necessário para a primeira rodada de criptografia.
    * Extrai hashes de senha kerberos dos atributos *suplementaresCredentials.*
    * Verifica a configuração de segurança do Azure AD Domain Services *SyncNtlmPasswords.*
        * Se essa configuração estiver desativada, gerará um hash NTLM aleatório e de alta entropia (diferente da senha do usuário). Este hash é então combinado com os hashes de senha kerberos exigentes do atributo *crendetials suplementarem* em uma estrutura de dados.
        * Se ativado, combina o valor do atributo *unicodePwd* com os hashes de senha Kerberos extraídos do atributo *SuplementaralCredentials* em uma estrutura de dados.
    * Criptografa a estrutura de dados única usando a chave simétrica AES.
    * Criptografa a chave simétrica AES usando a chave pública Azure AD Domain Services do inquilino.
1. O Azure AD Connect transmite a chave simétrica AES criptografada, a estrutura de dados criptografada que contém os hashes de senha e o vetor de inicialização do Azure AD.
1. O Azure AD armazena a chave simétrica AES criptografada, a estrutura de dados criptografada e o vetor de inicialização para o usuário.
1. O Azure AD empurra a chave simétrica AES criptografada, a estrutura de dados criptografada e o vetor de inicialização usando um mecanismo de sincronização interna através de uma sessão HTTP criptografada para o Azure AD Domain Services.
1. O Azure AD Domain Services recupera a chave privada para a instância do inquilino do cofre Azure Key.
1. Para cada conjunto criptografado de dados (representando a alteração de senha de um único usuário), o Azure AD Domain Services executa as seguintes etapas:
    * Usa sua chave privada para descriptografar a chave simétrica AES.
    * Usa a chave simétrica AES com o vetor de inicialização para descriptografar a estrutura de dados criptografadas que contém os hashes de senha.
    * Grava os hashes de senha do Kerberos que recebe no controlador de domínio do Azure AD Domain Services. Os hashes são salvos no atributo *suplementarCredenciais* do objeto do usuário que é criptografado para a chave pública do controlador de domínio do Azure AD Domain Services.
    * O Azure AD Domain Services escreve o hash de senha NTLM que recebeu no controlador de domínio do Azure AD Domain Services. O hash é salvo no atributo *unicodePwd* do objeto do usuário que é criptografado para a chave pública do controlador de domínio do Azure AD Domain Services.

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

## <a name="next-steps"></a>Próximas etapas
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrando suas identidades locais ao Azure Active Directory](whatis-hybrid-identity.md)
* [Obter um plano de implantação passo a passo para migração do AD FS para sincronização de Hash de senha](https://aka.ms/authenticationDeploymentPlan)
