---
title: Use a identidade gerenciada com um aplicativo
description: Como usar identidades gerenciadas no código do aplicativo Azure Service Fabric para acessar os Serviços do Azure.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: cbdb1190ec3238a6accd34db3025e08c194d60b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415610"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Como aproveitar a identidade gerenciada de um aplicativo de malha de serviço para acessar os serviços do Azure

Os aplicativos de fabric de serviço podem aproveitar identidades gerenciadas para acessar outros recursos do Azure que suportam a autenticação baseada no Azure Active Directory. Um aplicativo pode obter um [token de acesso](../active-directory/develop/developer-glossary.md#access-token) representando sua identidade, que pode ser atribuído ao sistema ou atribuído pelo usuário, e usá-lo como um token 'portador' para autenticar-se a outro serviço - também conhecido como [servidor de recursos protegido](../active-directory/develop/developer-glossary.md#resource-server). O token representa a identidade atribuída ao aplicativo Service Fabric e só será emitido para os recursos do Azure (incluindo aplicativos SF) que compartilham essa identidade. Consulte a documentação de visão geral de [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para uma descrição detalhada das identidades gerenciadas, bem como a distinção entre identidades atribuídas ao sistema e atribuídas pelo usuário. Vamos nos referir a um aplicativo de malha de serviço habilitado para identidade gerenciada como o [aplicativo cliente](../active-directory/develop/developer-glossary.md#client-application) ao longo deste artigo.

> [!IMPORTANT]
> Uma identidade gerenciada representa a associação entre um recurso do Azure e um diretor de serviço no inquilino Azure AD correspondente associado à assinatura que contém o recurso. Como tal, no contexto do Service Fabric, as identidades gerenciadas são suportadas apenas para aplicativos implantados como recursos do Azure. 

> [!IMPORTANT]
> Antes de usar a identidade gerenciada de um aplicativo de malha de serviço, o aplicativo cliente deve ter acesso ao recurso protegido. Consulte a lista de [serviços do Azure que suportam a autenticação azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) para verificar o suporte e, em seguida, à documentação do respectivo serviço para etapas específicas para conceder acesso à identidade a recursos de interesse. 

## <a name="acquiring-an-access-token-using-rest-api"></a>Adquirindo um token de acesso usando a API REST
Em clusters habilitados para identidade gerenciada, o tempo de execução do Service Fabric expõe um ponto final local host que os aplicativos podem usar para obter tokens de acesso. O ponto final está disponível em todos os nós do cluster, e é acessível a todas as entidades nesse nó. Os chamadores autorizados podem obter tokens de acesso ligando para este ponto final e apresentando um código de autenticação; o código é gerado pelo tempo de execução do Service Fabric para cada ativação distinta do pacote de código de serviço, e está vinculado à vida útil do pacote de código de serviço.

Especificamente, o ambiente de um serviço de malha de serviço habilitado para identidade gerenciada será semeado com as seguintes variáveis:
- 'IDENTITY_ENDPOINT': o ponto final do host local correspondente à identidade gerenciada do serviço
- 'IDENTITY_HEADER': um código de autenticação único representando o serviço no nó atual
- 'IDENTITY_SERVER_THUMBPRINT' : Impressão digital do servidor de identidade gerenciado da malha de serviço

> [!IMPORTANT]
> O código do aplicativo deve considerar o valor da variável de ambiente 'IDENTITY_HEADER' como dados confidenciais - ele não deve ser registrado ou de outra forma disseminado. O código de autenticação não tem valor fora do nó local, ou após o processo de hospedagem do serviço ter terminado, mas representa a identidade do serviço Service Fabric, e por isso deve ser tratado com as mesmas precauções que o próprio token de acesso.

Para obter um token, o cliente executa as seguintes etapas:
- forma um URI concatenando o ponto final de identidade gerenciado (valor IDENTITY_ENDPOINT) com a versão da API e o recurso (audiência) necessário para o token
- cria uma solicitação GET http(s) para o URI especificado
- adiciona lógica de validação de certificado de servidor apropriada
- adiciona o código de autenticação (valor IDENTITY_HEADER) como um cabeçalho à solicitação
- envia a solicitação

Uma resposta bem-sucedida conterá uma carga json representando o token de acesso resultante, bem como metadados descrevendo-o. Uma resposta fracassada também incluirá uma explicação da falha. Veja abaixo mais detalhes sobre o manuseio de erros.

Os tokens de acesso serão armazenados em cache pela Service Fabric em vários níveis (nó, cluster, serviço de provedor de recursos), de modo que uma resposta bem-sucedida não implica necessariamente que o token foi emitido diretamente em resposta à solicitação do aplicativo do usuário. Os tokens serão armazenados em cache por menos de sua vida útil e, portanto, um aplicativo é garantido para receber um token válido. Recomenda-se que o código do aplicativo cachê-se todos os tokens de acesso que adquirir; a chave de cache deve incluir (uma derivação de) o público. 


> [!NOTE]
> A única versão de API aceita está atualmente `2019-07-01-preview`, e está sujeita a alterações.

Solicitação de exemplo:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
onde:

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | O ponto final de identidade gerenciado para aplicativos de malha de serviço, fornecido através da variável ambiente IDENTITY_ENDPOINT. |
| `api-version` | Um parâmetro de seqüência de consulta, especificando a versão API do Serviço de Token de Identidade Gerenciada; atualmente, o único `2019-07-01-preview`valor aceito é , e está sujeito a alterações. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Isso será refletido `aud` como a reivindicação (audiência) do token emitido. Este exemplo solicita um token para acessar o Azure Key\/Vault, cujo uri de id de aplicativo é https: /vault.azure.net/. |
| `Secret` | Um campo de cabeçalho de solicitação HTTP, exigido pelo Service Fabric Managed Identity Token Service for Service para autenticar o chamador. Esse valor é fornecido pelo tempo de execução do SF via IDENTITY_HEADER variável de ambiente. |


Exemplo de resposta:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
onde:

| Elemento | Descrição |
| ------- | ----------- |
| `token_type` | O tipo de token; neste caso, um token de acesso "Portador", o que significa que o apresentador ('portador') deste token é o assunto pretendido do token. |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `expires_on` | O carimbo de data e hora da expiração do token de acesso; representado como o número de segundos de "1970-01-01T0:0:0Z UTC" `exp` e corresponde à reivindicação do token. Neste caso, o token expira em 2019-08-08T06:10:11+00:00 (na RFC 3339)|
| `resource` | O recurso para o qual o token de `resource` acesso foi emitido, especificado através do parâmetro de seqüência de consulta da solicitação; corresponde à reivindicação 'aud' do token. |


## <a name="acquiring-an-access-token-using-c"></a>Adquirindo um token de acesso usando C #
O acima se torna, em C#:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Acessando o Key Vault a partir de um aplicativo de malha de serviço usando identidade gerenciada
Esta amostra se baseia no acima para demonstrar o acesso a um segredo armazenado em um Key Vault usando identidade gerenciada.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Tratamento de erros
O campo 'código de status' do cabeçalho de resposta HTTP indica o status de sucesso da solicitação; um status '200 OK' indica sucesso, e a resposta incluirá o token de acesso conforme descrito acima. A seguir, uma pequena enumeração de possíveis respostas de erro.

| Código de status | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| 404 Não Encontrado: | Código de autenticação desconhecido, ou o aplicativo não foi atribuído uma identidade gerenciada. | Retifique a configuração do aplicativo ou o código de aquisição de tokens. |
| 429 Número excessivo de solicitações. |  Limite de aceleração atingido, imposto por AAD ou SF. | Tentar novamente com Retirada Exponencial. Consulte as diretrizes abaixo. |
| o erro 4xx na solicitação. | Um ou mais parâmetros de solicitação estava incorreto. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  Os erros 4xx são erros de tempo de design.|
| Erro de 5xx do serviço. | O subsistema de identidade gerenciado ou o Azure Active Directory retornaram um erro transitório. | É seguro tentar novamente depois de um tempo. Você pode atingir uma condição de estrangulamento (429) ao tentar novamente.|

Se ocorrer um erro, o corpo de resposta HTTP correspondente contém um objeto JSON com os detalhes do erro:

| Elemento | Descrição |
| ------- | ----------- |
| código | Código do erro. |
| correlationId | Um ID de correlação que pode ser usado para depuração. |
| message | Descrição detalhada do erro. **As descrições de erros podem mudar a qualquer momento. Não dependa da mensagem de erro em si.**|

Erro de amostra:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

A seguir está uma lista de erros típicos de malha de serviço específicos para identidades gerenciadas:

| Código | Mensagem | Descrição | 
| ----------- | ----- | ----------------- |
| Cabeçalho secretonão encontrado | O segredo não é encontrado nos cabeçalhos de solicitação. | O código de autenticação não foi fornecido com a solicitação. | 
| Identidade gerenciadaNão encontrada | Identidade gerenciada não encontrada para o host de aplicativo especificado. | O aplicativo não tem identidade, ou o código de autenticação é desconhecido. |
| ArgumentonullorEmpty | O parâmetro 'recurso' não deve ser nulo ou vazio. | O recurso (audiência) não foi fornecido no pedido. |
| InvalidApiVersion | A versão api '' não é suportada. A versão suportada é '2019-07-01-preview'. | Versão de API ausente ou sem suporte especificada no URI de solicitação. |
| InternalServerError | Ocorreu um erro. | Um erro foi encontrado no subsistema de identidade gerenciado, possivelmente fora da pilha de malha de serviço. Causa mais provável é um valor incorreto especificado para o recurso (verifique se há '/'?) | 

## <a name="retry-guidance"></a>Repita a orientação 

Normalmente, o único código de erro retável é 429 (Muitas solicitações); erros internos do servidor/códigos de erro 5xx podem ser retriáveis, embora a causa possa ser permanente. 

Os limites de estrangulamento se aplicam ao número de chamadas feitas ao subsistema de identidade gerenciado - especificamente as dependências 'upstream' (o serviço Azure de identidade gerenciada ou o serviço de token seguro). O service Fabric armazena tokens em vários níveis no pipeline, mas dada a natureza distribuída dos componentes envolvidos, o chamador pode experimentar respostas de estrangulamento inconsistentes (ou seja, ser estrangulado em um nó/instância de um aplicativo, mas não em um nó diferente enquanto solicita um token para a mesma identidade.) Quando a condição de estrangulamento for definida, as solicitações subseqüentes do mesmo aplicativo podem falhar com o código de status HTTP 429 (Muitas Solicitações) até que a condição seja limpa.  

Recomenda-se que as solicitações não tentadas devido ao estrangulamento sejam repetidas com um recuo exponencial, da seguinte forma: 

| Índice de chamada | Ação ao receber 429 | 
| --- | --- | 
| 1 | Espere 1 segundo e tente novamente |
| 2 | Espere 2 segundos e tente novamente |
| 3 | Espere 4 segundos e tente novamente |
| 4 | Espere 8 segundos e tente novamente |
| 4 | Espere 8 segundos e tente novamente |
| 5 | Espere 16 segundos e tente novamente |

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure
Consulte [os serviços do Azure que suportam a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) para uma lista de recursos que suportam o Azure AD e seus respectivos IDs de recursos.

## <a name="next-steps"></a>Próximas etapas
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceda acesso a um aplicativo de malha de serviço do Azure a outros recursos do Azure](./how-to-grant-access-other-resources.md)
