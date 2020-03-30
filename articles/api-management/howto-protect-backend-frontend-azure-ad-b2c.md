---
title: Proteja o backend do SPA com o OAuth 2.0 usando o Azure Active Directory B2C e o Azure API Management.
description: Proteja uma API com OAuth 2.0 usando o Azure Active Directory B2C, o Azure API Management e o Easy Auth para ser chamado de um SPA JavaScript.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475469"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteja o backend do SPA com o OAuth 2.0, o Azure Active Directory B2C e o Azure API Management

Esse cenário mostra como configurar sua instância de gerenciamento de API do Azure para proteger uma API.
Usaremos o protocolo OAuth 2.0 com Ozure AD B2C, juntamente com o Gerenciamento de API para garantir um backend de funções do Azure usando easyAuth.

## <a name="aims"></a>Visa
Vamos ver como o Gerenciamento de API pode ser usado em um cenário simplificado com funções azure e Azure AD B2C. Você criará um aplicativo JavaScript (JS) chamando uma API, que faz sinais em usuários com Azure AD B2C. Em seguida, você usará os recursos de política validate-jwt do API Management para proteger a API do Backend.

Para defesa em profundidade, usamos EasyAuth para validar o token novamente dentro da API back-end.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir as etapas deste artigo, você precisa ter:
* Uma conta de armazenamento V2 de uso geral do Azure (StorageV2) para hospedar o aplicativo de página única JS frontend
* Uma instância de gerenciamento de API do Azure 
* Um aplicativo vazio do Azure Function (executando o tempo de execução do V2 .NET Core, em um Plano de Consumo do Windows) para hospedar a chamada API
* Um inquilino Azure AD B2C, vinculado a uma assinatura 

Embora na prática você usaria recursos na mesma região em cargas de trabalho de produção, para este artigo como fazer a região de implantação não é importante.

## <a name="overview"></a>Visão geral
Aqui está uma ilustração dos componentes em uso e do fluxo entre eles uma vez que este processo esteja concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

A seguir é apresentada uma visão geral das etapas:

1. Crie os aplicativos De Chamada AD B2C do Azure (Frontend, Gerenciamento de API) e API com escopos e conceda acesso à API
1. Crie as políticas de inscrição ou login para permitir que os usuários entrem no Azure AD B2C 
1. Configure o gerenciamento de API com os novos IDs e chaves do cliente Ad B2C do Azure AD Para habilitar a autorização do usuário OAuth2 no Console do Desenvolvedor
1. Construir a API de função
1. Configure a API de função para habilitar o EasyAuth com os novos IDs e chaves do cliente Azure AD B2C e bloqueie o APIM VIP 
1. Construa a definição de API no gerenciamento de API
1. Configurar o Oauth2 para a configuração de API de gerenciamento de API
1. Configure a diretiva **CORS** e adicione a política **validate-jwt** para validar o token OAuth para cada solicitação recebida
1. Construa o aplicativo de chamada para consumir a API
1. Faça upload da amostra js spa
1. Configure o Aplicativo cliente Sample JS com os novos IDs e chaves do cliente Azure AD B2C 
1. Teste o Aplicativo cliente

## <a name="configure-azure-ad-b2c"></a>Configurar azure AD B2C 
Abra a lâmina Azure AD B2C no portal e faça as seguintes etapas.
1. Selecione a guia **Aplicativos** 
1. Clique no botão 'Adicionar' e crie três aplicativos para os seguintes propósitos
   * O cliente do Frontend.
   * A API da função backend.
   * [Opcional] O portal de desenvolvedores de gerenciamento de API (a menos que você esteja executando o Azure API Management no nível de consumo, mais sobre este cenário mais tarde).
1. Defina webapp / Web API para todos os 3 aplicativos e defina 'Permitir fluxo implícito' para sim apenas para o Cliente Frontend.
1. Agora defina o APP ID URI, escolha algo único e relevante para o serviço que está sendo criado.
1. Use espaços reservados para as urls https://localhostde resposta por enquanto, como, atualizaremos essas urls mais tarde.
1. Clique em 'Criar', depois repita as etapas 2-5 para cada um dos três aplicativos acima, registrando o AppID URI, nome e ID de aplicativo para uso posterior para todos os três aplicativos.
1. Abra o Aplicativo do Portal do Desenvolvedor de Gerenciamento de API da lista de aplicativos e selecione a guia *Chaves* (em Geral) e clique em 'Gerar chave' para gerar uma chave auth
1. Ao clicar em salvar, grave a chave em algum lugar seguro para uso posterior - observe que este lugar é a única chance que você poderá visualizar e copiar esta chave.
1. Agora selecione a guia *Escopos Publicados* (Sob Acesso à API)
1. Crie e nomeie um escopo para sua API de função e grave o Escopo e preencha o valor total do escopo e clique em 'Salvar'.
   > [!NOTE]
   > Os escopos Azure AD B2C são efetivamente permissões dentro de sua API que outros aplicativos podem solicitar acesso através da lâmina de acesso a API a partir de seus aplicativos, efetivamente você acabou de criar permissões de aplicativos para sua chamada API.
1. Abra os outros dois aplicativos e, em seguida, olhe na guia *API Access.*
1. Conceda-lhes acesso ao escopo da API backend e ao padrão que já estava lá ("Acesse o perfil do usuário").
1. Gere-lhes uma chave cada uma, selecionando a guia *Chaves* em 'Geral' para gerar uma tecla auth e gravar essas teclas em algum lugar seguro para depois.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Crie um fluxo de usuário "Cadastre-se ou Faça Login"
1. Retornar à raiz (ou 'Visão geral') da lâmina Azure AD B2C 
1. Em seguida, selecione "Fluxos de usuário (Políticas)" e clique em "Novo fluxo de usuário"
1. Escolha o tipo de fluxo de usuário 'Cadastre-se e faça login'
1. Dê um nome à apólice e grave-a para depois.
1. Em seguida, em 'Provedores de identidade', verifique 'ID do usuário' (isso pode dizer 'E-mail se inscrever') e clique em OK. 
1. Em 'Atributos e reclamações do usuário', clique em 'Mostrar mais...' em seguida, escolha as opções de reivindicação que você deseja que seus usuários insiram e tenham retornado no token. Verifique pelo menos 'Nome de exibição' e 'Endereço de e-mail' para coletar e retornar e clique em 'OK', depois clique em 'Criar'.
1. Selecione a diretiva criada na lista e clique no botão 'Executar fluxo de usuário'.
1. Essa ação abrirá a lâmina de fluxo do usuário em execução, selecionará o aplicativo frontend e gravará o endereço do domínio b2clogin.com mostrado a lista 'Selecionar domínio'.
1. Clique no link na parte superior para abrir o 'ponto final de configuração openid bem conhecido', e registre os valores authorization_endpoint e token_endpoint, bem como o valor do link em si como o conhecido ponto final de configuração openid.

   > [!NOTE]
   > As políticas B2C permitem que você exponha os pontos finais de login Do Azure AD B2C para poder capturar diferentes componentes de dados e fazer login nos usuários de diferentes maneiras. Neste caso, configuramos um sinal ou sinal no ponto final, que expôs um ponto final de configuração bem conhecido, especificamente nossa política criada foi identificada na URL pelo parâmetro p=.
   > 
   > Uma vez feito isso – agora você tem uma plataforma funcional de identidade Business to Consumer que assinará com os usuários em vários aplicativos. 
   > Se você quiser, você pode clicar no botão 'Executar fluxo de usuário' aqui (para passar pelo processo de inscrição ou login) e ter uma noção do que ele vai fazer na prática, mas a etapa de redirecionamento no final falhará, pois o aplicativo ainda não foi implantado.

## <a name="build-the-function-api"></a>Construa a API de função
1. Volte para o seu inquilino Ad padrão do Azure no portal Azure para que possamos configurar itens em sua assinatura novamente 
1. Vá para a lâmina Function Apps do portal Azure, abra seu aplicativo de função vazia e crie uma nova função in-Portal 'Webhook + API' através do quickstart.
1. Cole o código de amostra abaixo em Run.csx sobre o código existente que aparece.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > O código de função de script c# que você acabou de colar simplesmente registra uma linha nos logs de funções e retorna o texto "Hello World" com alguns dados dinâmicos (a data e a hora).

3. Selecione "Integrar" na lâmina esquerda e selecione "Editor Avançado" no canto superior direito do painel.
4. Cole o código de amostra abaixo sobre o json existente.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Volte para a guia HttpTrigger1, clique em 'Obter url de função', e copie a URL que aparece.

   > [!NOTE]
   > As vinculações que você acabou de criar simplesmente dizem funções para responder em solicitações anônimas http GET para a URL que você acabou de copiar. (https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) Agora temos uma API https sem servidor escalável, que é capaz de retornar uma carga muito simples.
   > Agora você pode testar a chamada de chamada desta API de um navegador da Web usando a URL acima, você também pode tirar a parte ?code=secreta da URL e provar que as funções do Azure retornarão um erro de 401.

## <a name="configure-and-secure-the-function-api"></a>Configure e proteja a Função API
1. Duas áreas extras no aplicativo de função precisam ser configuradas (Auth e Restrições de Rede).
1. Em primeiro lugar, vamos configurar autenticação / autorização, então clique no nome &lt;&gt; do aplicativo de função (ao lado do ícone de funções Z) para mostrar a página de visão geral.
1. Em seguida, selecione a guia 'Recursos da plataforma' e selecione 'Autenticação / Autorização'.
1. Ative o recurso de autenticação do serviço de aplicativo.
1. Em 'Provedores de autenticação' escolha 'Diretório ativo do Azure' e escolha 'Avançado' no switch modo de gerenciamento.
1. Cole o ID de aplicação da Função Backend (do Azure AD B2C na caixa 'ID do cliente')
1. Cole o ponto final de configuração de id aberto bem conhecido a partir da diretiva de inscrição ou login na caixa URL do Emissor (gravamos essa configuração anteriormente).
1. Selecione OK.
1. Defina a ação a ser tomada quando a solicitação não for autenticada em "Login com o Diretório Ativo do Azure", e clique em Salvar.

   > [!NOTE]
   > Agora, sua API de função é implantada e deve lançar 401 respostas se a chave correta não for fornecida e deve retornar dados quando uma solicitação válida for apresentada.
   > Você adicionou segurança adicional de defesa em profundidade no EasyAuth, configurando a opção 'Login com Azure AD' para lidar com solicitações não autenticadas. Esteja ciente de que isso mudará o comportamento de solicitação não autorizada entre o aplicativo de função Backend e o Frontend SPA, pois o EasyAuth emitirá um redirecionamento 302 para AAD em vez de uma resposta 401 Não Autorizada, corrigiremos isso usando o Gerenciamento de API mais tarde.
   > Ainda não temos segurança IP aplicada, se você tiver uma chave válida e token OAuth2, qualquer um pode chamar isso de qualquer lugar - idealmente queremos forçar todas as solicitações a vir em gerenciamento de API.
   > Se você estiver usando o nível de consumo de gerenciamento de API, você não será capaz de realizar esse bloqueio por VIP, pois não há IP estático dedicado para esse nível, você precisará confiar no método de bloquear suas chamadas de API através da chave de função secreta compartilhada , portanto, os passos 11-13 não serão possíveis.

1. Feche a lâmina 'Autenticação / Autorização' 
1. Selecione 'Rede' e selecione 'Restrições de acesso'
1. Em seguida, bloqueie os IPs do aplicativo de função permitido supér8VIP da instância de gerenciamento de API. Este VIP é mostrado na gestão da API - seção de visão geral do portal.
1. Se você quiser continuar a interagir com o portal de funções, e para realizar as etapas opcionais abaixo, você deve adicionar seu próprio endereço IP público ou faixa CIDR aqui também.
1. Uma vez que há uma entrada permitida na lista, o Azure adiciona uma regra de negação implícita para bloquear todos os outros endereços. 

Você precisará adicionar blocos formatados de endereços do CIDR ao painel de restrições de IP. When you need to add a single address such as the API Management VIP, you need to add it in the format xx.xx.xx.xx.

   > [!NOTE]
   > Agora, sua API de função não deve ser callable de qualquer lugar que não seja através do gerenciamento de API ou do seu endereço.
   
## <a name="import-the-function-app-definition"></a>Importar a definição do aplicativo de função
1. Abra a *lâmina de gerenciamento de API*e abra sua *instância*.
1. Selecione a lâmina DE APIs na seção Gerenciamento de API da sua instância.
1. No painel 'Adicionar uma Nova API', escolha 'Function App', depois selecione 'Full' na parte superior do popup.
1. Clique em Procurar, escolha o aplicativo de função que você está hospedando a API dentro e clique em selecionar.
1. Dê à API um nome e uma descrição para o uso interno da API Management e adicione-a ao Produto 'ilimitado'.
1. Certifique-se de gravar a URL base para uso posterior e, em seguida, clique em criar.

## <a name="configure-oauth2-for-api-management"></a>Configurar o oauth2 para gerenciamento de API

1. Em seguida, selecione a lâmina Oauth 2.0 na guia de segurança e clique em 'Adicionar'
1. Dê valores para *Nome de Exibição* e *Descrição* para o ponto final de Oauth adicionado (esses valores aparecerão na próxima etapa como um ponto final Oauth2).
1. Você pode inserir qualquer valor na URL da página de registro do Cliente, pois esse valor não será usado.
1. Verifique o tipo de Concessão *Auth Implícita* e deixe verificado o tipo de concessão do código de autorização.
1. Mova-se para os campos *De autorização* e ponto final *do Token* e digite os valores capturados a partir do documento xml de configuração bem conhecido anteriormente.
1. Role para baixo e preencha um *parâmetro de corpo adicional* chamado 'resource' com o ID do cliente da função Backend a partir do registro do aplicativo Azure AD B2C
1. Selecione 'Credenciais do cliente', defina o ID do cliente para o ID do aplicativo do console Do desenvolvedor - pule essa etapa se estiver usando o modelo de Gerenciamento de API de consumo.
1. Defina o Client Secret para a chave que você gravou anteriormente - pule esta etapa se usar o modelo de gerenciamento de API de consumo.
1. Por último, agora registre o redirect_uri da concessão de código auth da API Management para uso posterior.

## <a name="set-up-oauth2-for-your-api"></a>Configure o Oauth2 para sua API
1. Sua API aparecerá no lado esquerdo do portal na seção 'Todas as APIs', abra sua API clicando nele.
1. Selecione a guia 'Configurações'.
1. Atualize suas configurações selecionando "Oauth 2.0" no botão de rádio de autorização do usuário.
1. Selecione o servidor Oauth que você definiu anteriormente.
1. Verifique a caixa de seleção 'Escopo de substituição' e digite o escopo que você gravou para a chamada de API backend mais cedo.

   > [!NOTE]
   > Agora temos uma instância de gerenciamento de API que sabe como obter tokens de acesso do Azure AD B2C para autorizar solicitações e entende nossa configuração Oauth2 Azure Active Directory B2C.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configure as políticas **CORS** e **validate-jwt**

> As seções a seguir devem ser seguidas independentemente do nível APIM utilizado. 

1. Volte para a guia de design e escolha "Todas as APIs", depois clique no botão de exibição de código para mostrar o editor de políticas.
1. Edite a seção de entrada e cole o xml abaixo para que ele leia como o seguinte.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Edite a url openid-config para corresponder ao seu conhecido ponto final Azure AD B2C para a política de inscrição ou login.
1. Editar o valor da reclamação para corresponder ao ID de aplicativo válido, também conhecido como ID do cliente para o aplicativo de API backend e salvar.

   > [!NOTE]
   > Agora, o gerenciamento de API é capaz de responder a solicitações de origem cruzada para aplicativos JS SPA, e executará o estrangulamento, limitação de taxa e pré-validação do token de auth JWT sendo passado ANTES de encaminhar a solicitação para a API de função.

   > [!NOTE]
   > A seção a seguir é opcional e não se aplica à camada **Consumo,** que não suporta o portal do desenvolvedor.
   > Se você não pretende usar o portal do desenvolvedor ou não puder usá-lo já que está usando o nível de Consumo, pule esta etapa e pule direto para ["Construir o JavaScript SPA para consumir a API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>[Opcional] Configure o portal do desenvolvedor

1. Abra a lâmina Azure AD B2C e navegue até o registro do aplicativo para o Portal do Desenvolvedor
1. Defina a entrada 'URL de resposta' para aquela que você anotou quando configurou o redirect_uri da concessão de código auth no Gerenciamento de API anteriormente.

   Agora que a autorização do usuário OAuth `Echo API`2.0 está ativada no , o Console do Desenvolvedor obtém um token de acesso para o usuário, antes de chamar a API.

1. Navegue para qualquer `Echo API` operação no portal do desenvolvedor e selecione Tente-o para **levá-lo** ao Console do Desenvolvedor.
1. Observe um novo item na seção **Autorização,** correspondente ao servidor de autorização que você acabou de adicionar.
1. Selecione o código de **autorização** da lista suspensa de autorização e você é solicitado a entrar no inquilino do Azure AD. Se você já fez o assinatura da conta, você pode não ser solicitado.
1. Após o login bem-sucedido, um `Authorization: Bearer` cabeçalho é adicionado à solicitação, com um token de acesso do Azure AD B2C codificado na Base64. 
1. Selecione **Enviar** e você pode chamar a API com sucesso.

   > [!NOTE]
   > Agora, o gerenciamento de API é capaz de adquirir tokens para o portal do desenvolvedor para testar sua API e é capaz de entender sua definição e renderizar a página de teste apropriada no portal de v.

1. Na lâmina de visão geral do portal de gerenciamento de API, clique em 'Portal do Desenvolvedor' para fazer login como administrador da API.
1. Aqui, você e outros consumidores selecionados de sua API podem testá-los e chamá-los de um console.
1. Selecione 'Produtos', escolha 'Ilimitado', depois escolha a API que criamos anteriormente e clique em 'TRY IT'
1. Desocultar a chave de assinatura da API e copiá-la em algum lugar seguro, juntamente com a url de solicitação que você precisará mais tarde.
1. Selecione também Implicit, a partir do oauth auth dropdown e você pode ter que autenticar aqui com um popup.
1. Clique em 'Enviar' e se tudo estiver bem, seu Aplicativo de Função deve responder de volta com uma mensagem de olá via gerenciamento de API com uma mensagem 200 OK e alguns JSON.

   > [!NOTE]
   > Parabéns, agora você tem a Azure AD B2C, API Management e Azure Functions trabalhando juntos para publicar, proteger e consumir uma API. Você deve ter notado que a API está de fato protegida duas vezes usando este método, uma vez com o Cabeçalho de Chave de Assinatura de Gerenciamento de API e uma vez com a Autorização: Portador JWT.
   > Você estaria correto, como este exemplo é um Aplicativo de Página Única JavaScript, usamos a Chave de Gerenciamento de API apenas para chamadas de limitação de taxa e cobrança.
   > A autorização e autenticação real é tratada pelo Azure AD B2C, e é encapsulada no JWT, que é validado duas vezes, uma vez pela API Management e, em seguida, pelo Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Construa o JavaScript SPA para consumir a API
1. Abra a lâmina das contas de armazenamento no portal Azure 
1. Selecione a conta criada e selecione a lâmina 'Site estático' na seção Configurações (se você não vir uma opção 'Site estático', verifique se criou uma conta V2).
1. Defina o recurso de hospedagem estático da Web como 'ativado', e defina o nome do documento de índice como 'index.html', em seguida, clique em 'salvar'.
1. Anote o conteúdo do Ponto Final Primário, pois este local é onde o site frontend será hospedado. 

   > [!NOTE]
   > Você pode usar o Azure Blob Storage + CDN rewrite ou o Azure App Service - mas o recurso de hospedagem Static Website do Blob Storage nos dá um contêiner padrão para servir conteúdo estático da Web / html / js / css do Azure Storage e inferirá uma página padrão para nós para trabalho zero.

## <a name="upload-the-js-spa-sample"></a>Faça upload da amostra js spa
1. Ainda na lâmina da conta de armazenamento, selecione a lâmina 'Blobs' na seção Serviço blob e clique no recipiente $web que aparece no painel da direita.
1. Salve o código abaixo para um arquivo localmente em sua máquina como index.html e, em seguida, carregue o índice de arquivo.html para o contêiner $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Navegue até o ponto final do site estático armazenado anteriormente na última seção.

   > [!NOTE]
   > Parabéns, você acabou de implantar um aplicativo javaScript single page para o Armazenamento Azure Já que não configuramos o aplicativo JS com suas chaves para a api ou configuramos o aplicativo JS com os detalhes do Azure AD B2C ainda – a página ainda não funcionará se você abri-lo.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configure o JS SPA para Azure AD B2C
1. Agora sabemos onde tudo está: podemos configurar o SPA com o endereço API de gerenciamento de API apropriado e o aplicativo Azure AD B2C correto/ IDs do cliente
1. Volte para a lâmina de armazenamento do portal Azure e clique em index.html e escolha 'Editar blob' 
1. Atualize os detalhes do auth para corresponder ao aplicativo front-end que você registrou no B2C anteriormente, observando que os valores 'b2cScopes' são para o backend da API.
1. A chave webApi e a url api podem ser encontradas no painel de teste de gerenciamento de API para a operação da API.
1. Crie uma chave de assinatura APIM, indo para o Gerenciamento de API de volta para a lâmina de gerenciamento de API, selecionando 'Assinaturas', e clicando em 'Adicionar assinatura' e salvando o registro. Clicar na Ellipsis (...) ao lado da linha criada permitirá que você mostre as teclas para que você possa copiar a tecla principal.
1. Deve se parecer com o código abaixo:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Clique em Salvar

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Defina os URIs de redirecionamento para o aplicativo frontend AD B2C do Azure
1. Abra a lâmina Azure AD B2C e navegue até o registro do aplicativo para o Aplicativo Frontend JavaScript
1. Defina a URL de redirecionamento para a que você anotou quando você configurar anteriormente o ponto final principal do site estático acima

   > [!NOTE] 
   > Essa configuração resultará em um cliente do aplicativo frontend recebendo um token de acesso com reivindicações apropriadas do Azure AD B2C.
   > O SPA será capaz de adicionar isso como um token portador no cabeçalho https na chamada para a API backend.
   > O Gerenciamento de API pré-validará previamente as chamadas de token e limite de taxa para o ponto final pela chave de assinante, antes de passar pela solicitação para a API de função Azure que recebe.
   > O SPA renderizará a resposta no navegador.

   > *Parabéns, você configurou Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization para trabalhar em perfeita harmonia!*

   > [!NOTE]
   > Agora temos um aplicativo simples com uma API simples e segura, vamos testá-la.

## <a name="test-the-client-application"></a>Teste o aplicativo do cliente
1. Abra a URL do aplicativo de exemplo que você anotou na conta de armazenamento que você criou anteriormente
1. Clique em "Entrar" no canto superior direito, este clique aparecerá no seu Azure AD B2C se inscrever ou entrar no perfil.
1. Postar O sinal na seção "Logd in as" da tela será preenchido a partir do seu JWT.
1. Agora clique em "Chamar Web Api", e você a página deve atualizar com os valores enviados de volta de sua API protegida.

## <a name="and-were-done"></a>E nós terminamos
As etapas acima podem ser adaptadas e editadas para permitir muitos usos diferentes do Azure AD B2C com gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).
* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).
* [Gerencie sua primeira API](import-and-publish.md).
