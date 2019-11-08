---
title: Crie um Modelo de Locatário (Versão Prévia) – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Gere automaticamente um modelo de fala personalizada que aproveita seus dados do Office 365 para oferecer um reconhecimento de fala ideal para termos específicos da organização que sejam seguros e compatíveis.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 85b9291ee24c024ebc8ce81ddba46d04f7744081
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504592"
---
# <a name="create-a-tenant-model-preview"></a>Crie um Modelo de Locatário (Versão Prévia)

O Modelo de Locatário é um serviço de aceitação para clientes do Office 365 Enterprise que gera automaticamente um modelo de reconhecimento de fala personalizada dos dados do Office365 de sua organização. O modelo criado é otimizado para termos técnicos, jargões e nomes de pessoas, tudo de maneira segura e em conformidade.

> [!IMPORTANT]
> Caso sua organização se inscreva com o Modelo de Locatário, o serviço de Fala poderá acessar o modelo de linguagem de sua organização, que é gerado pelos recursos do Office 365, como emails e documentos. O administrador do Office 365 de sua organização pode ativar/desativar o uso do modelo de linguagem em toda a organização usando o Portal de Administração do Office 365.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Registre-se para usar um Modelo de Locatário no Centro de Administração do Microsoft 365
> * Obtenha uma chave de assinatura de Fala
> * Crie um Modelo de Locatário
> * Implante um Modelo de Locatário
> * Use um Modelo de Locatário com o SDK de Fala

![Diagrama do Modelo de Locatário](media/tenant-language-model/tenant-language-model-diagram.png)

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Registre-se usando o Centro de Administração do Microsoft 365

Antes de implantar seu Modelo de Locatário, primeiro é necessário registrá-lo usando o Centro de Administração do Microsoft 365. Essa tarefa poderá ser concluída somente pelo Administrador do Microsoft 365.

1. Entre no [Centro de Administração do Microsoft 365](https://admin.microsoft.com ).
2. No painel esquerdo, selecione **Configurações** e, em seguida, **Aplicativos**.

   ![Diagrama do Modelo de Locatário](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Localize e selecione os **Serviços de Fala do Azure**.

   ![Diagrama do Modelo de Locatário](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Clique na caixa de seleção e salve.

Caso precise desativar o Modelo de Locatário, navegue de volta para esta tela, desmarque a caixa de seleção e salve.

## <a name="get-a-speech-subscription-key"></a>Obtenha uma chave de assinatura de Fala

Para usar um Modelo de Locatário com o SDK de Fala, será necessário um recurso de Fala e uma chave de assinatura associada a ele.

1. Faça logon no [Portal do Azure](https://aka.ms/azureportal).
2. Selecione **Criar um recurso**.
3. Na barra de pesquisa, digite: **Fala**.
4. Selecione **Fala** e clique em **Criar**.
5. Siga as instruções na tela para criar o recurso. Não se esqueça de:
   * **O local** é definido como **eastus** ou **westus**.
   * **O tipo de preço** é definido como **S0**.
6. Clique em **Criar**.
7. O recurso será criado após alguns minutos. A chave de assinatura está disponível na seção **Visão Geral** para seu recurso.

## <a name="create-a-model"></a>Criar um modelo

Depois que o administrador tiver habilitado o Modelo de Locatário para sua organização, será possível criar um modelo de linguagem com base em seus dados do Office 365.

1. Entre no [Estúdio de Fala](https://speech.microsoft.com/).
2. No canto superior direito, localize e clique no ícone de engrenagem (configurações) e, em seguida, selecione **configurações do Modelo de Locatário**.

   ![Menu Configurações](media/tenant-language-model/tenant-language-settings.png)

3. Neste momento, você verá uma mensagem informando se está qualificado para criar um Modelo de Locatário.
   > [!NOTE]
   > Os clientes do Office 365 Enterprise na América do Norte estão qualificados para criar um Modelo de Locatário (inglês). Caso seja um cliente do CLB (Sistema de Proteção de Dados do Cliente) ou da CK (Chave de Cliente), esse recurso não estará disponível. Para determinar se você é um cliente do Sistema de Proteção de Dados do Cliente ou da Chave de Cliente, siga estas instruções:
   > * [Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Chave de Cliente](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)

4. Em seguida, selecione **Aceitar**. Você receberá um email com instruções quando seu Modelo de Locatário estiver pronto.

## <a name="deploy-your-model"></a>Implantar o seu modelo

Quando o Modelo de Locatário estiver pronto, siga estas etapas para implantar seu modelo:

1. Clique no botão **Exibir modelo** no email de confirmação que recebeu ou entre no [Estúdio de Fala](https://speech.microsoft.com/).
2. No canto superior direito, localize e clique no ícone de engrenagem (configurações) e, em seguida, selecione **configurações do Modelo de Locatário**.

   ![Menu Configurações](media/tenant-language-model/tenant-language-settings.png)

3. Clique em **Implantar**.
4. Quando o modelo for implantado, o status será alterado para **Implantado**.

## <a name="use-your-model-with-the-speech-sdk"></a>Use seu modelo com o SDK de Fala

Agora que você implantou o modelo, poderá usá-lo com o SDK de Fala. Nesta seção, você usará o código de exemplo fornecido para chamar o Serviço de Fala usando a autenticação do Azure AD.

Vamos analisar o código que você usará para chamar o SDK de Fala no C#. Neste exemplo, você executará o reconhecimento de fala usando um Modelo de Locatário. Este guia presume que sua plataforma já está configurada. Caso precise de ajuda para configurar, consulte o [Início Rápido: Reconheça a fala, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Copie este código em seu projeto:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Em seguida, será necessário recompilar e executar o projeto na linha de comando. Alguns parâmetros deverão ser atualizados antes de executar o comando.

1. Substitua `<Username>` e `<Password>` pelos valores de um usuário de locatário válido.
2. Substitua `<Subscription-Key>` pela chave de assinatura de seu recurso de Fala. Esse valor está disponível na seção **Visão Geral** para seu recurso de Fala no [portal do Azure](https://aka.ms/azureportal).
3. Substitua `<Endpoint-Uri>` pelo ponto de extremidade abaixo. Substitua `{your-region}` pela região onde o recurso de Fala foi criado. Essas regiões têm suporte: `westus`, `westus2` e `eastus`. As informações de sua região estão disponíveis na seção **Visão Geral** do recurso de Fala no [portal do Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Execute o comando:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Próximas etapas

* [Estúdio de Fala](https://speech.microsoft.com/)
* [SDK da fala](speech-sdk.md)
