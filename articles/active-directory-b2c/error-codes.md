---
title: Referência do código de erro
titleSuffix: Azure AD B2C
description: Uma lista dos códigos de erro que podem ser retornados pelo serviço B2C do Azure Active Directory.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188657"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Códigos de erro: Azure Active Directory B2C

Os seguintes erros podem ser retornados pelo serviço Azure Active Directory B2C.

| Código do erro | Mensagem |
| ---------- | ------- |
| `AADB2C90002` | O recurso CORS '{0}' retornou um 404 não encontrado. |
| `AADB2C90006` | O uri redirecionamento '{0}fornecido na solicitação não está registrado{1}para o id do cliente ' ' ' . |
| `AADB2C90007` | O aplicativo associado ao{0}id do cliente ' ' não tem URIs de redirecionamento registrados. |
| `AADB2C90008` | A solicitação não contém um parâmetro de id do cliente. |
| `AADB2C90010` | A solicitação não contém um parâmetro de escopo. |
| `AADB2C90011` | O id{0}do cliente ' fornecido na solicitação não{1}corresponde ao id do cliente ' ' registrado na apólice. |
| `AADB2C90012` | O escopo{0}' ' fornecido em solicitação não é suportado. |
| `AADB2C90013` | O tipo de{0}resposta solicitado ' ' fornecido na solicitação não é suportado. |
| `AADB2C90014` | O modo de{0}resposta solicitado ' ' fornecido na solicitação não é suportado. |
| `AADB2C90016` | O tipo de afirmação do cliente solicitado{0}{1}' ' não corresponde ao tipo esperado '. |
| `AADB2C90017` | A afirmação do cliente fornecida na solicitação é inválida:{0} |
| `AADB2C90018` | O id{0}do cliente ' especificado na solicitação{1}não está registrado no inquilino '. |
| `AADB2C90019` | O recipiente chave com{0}identificação '{1}em inquilino ' não tem uma chave válida. Motivo: {2}. |
| `AADB2C90021` | O perfil{0}técnico ' ' não{1}existe na{2}política ' de inquilino ' ' ' ' ' . |
| `AADB2C90022` | Incapaz de retornar metadados{0}para a{1}política ' ' in tenant '. |
| `AADB2C90023` | O{0}perfil ' ' não contém{1}a chave de metadados necessária ''. |
| `AADB2C90025` | O{0}perfil '{1}' '{2}in policy ' in tenant{3}' ' não contém a chave criptográfica necessária ' '. |
| `AADB2C90027` | As credenciais básicas{0}especificadas para ' ' são inválidas. Verifique se as credenciais estão corretas e que o acesso foi concedido pelo recurso. |
| `AADB2C90028` | O certificado do{0}cliente especificado para ' é inválido. Verifique se o certificado está correto, contém uma chave privada e que o acesso foi concedido pelo recurso. |
| `AADB2C90031` | A{0}diretiva ' ' não especifica uma jornada de usuário padrão. Certifique-se de que a política ou seus pais especifiquem uma jornada de usuário padrão como parte de uma seção de parte que depende. |
| `AADB2C90035` | O serviço está temporariamente indisponível. Por favor, tente novamente depois de alguns minutos. |
| `AADB2C90036` | A solicitação não contém um URI para redirecionar o usuário para fazer logout. Especifique um URI no campo de parâmetros post_logout_redirect_uri. |
| `AADB2C90037` | Ocorreu um erro ao processar a solicitação. Entre em contato com o administrador do site que você está tentando acessar. |
| `AADB2C90039` | O pedido contém uma afirmação do{0}cliente, mas{1}a política fornecida ' in tenant ' está faltando um client_secret em RelyingPartyPolicy. |
| `AADB2C90040` | A jornada{0}do usuário ' ' não contém uma etapa de envio de reivindicações. |
| `AADB2C90043` | O prompt incluído na solicitação contém valores inválidos. Esperado 'nenhum', 'login', 'consentimento' ou 'select_account'. |
| `AADB2C90044` | A alegação '{0}' não é{1}apoiada pela reivindicação resolver ' ' ' . |
| `AADB2C90046` | Estamos tendo problemas para carregar seu estado atual. Você pode querer tentar começar sua sessão mais do início. |
| `AADB2C90047` | O recurso{0}' ' contém erros de script impedindo que ele seja carregado. |
| `AADB2C90048` | Uma exceção não tratada ocorreu no servidor. |
| `AADB2C90051` | Não foram encontrados provedores de sinistros adequados. |
| `AADB2C90052` | Senha ou nome de usuário inválido. |
| `AADB2C90053` | Um usuário com a credencial especificada não pôde ser encontrado. |
| `AADB2C90054` | Senha ou nome de usuário inválido. |
| `AADB2C90055` | O escopo{0}' ' fornecido na solicitação devehttps://example.com/calendar.readespecificar um recurso, como ' '. |
| `AADB2C90057` | O aplicativo fornecido não está configurado para permitir o fluxo Implícito OAuth. |
| `AADB2C90058` | O aplicativo fornecido não está configurado para permitir clientes públicos. |
| `AADB2C90067` | O logout de postagem redirecionar URI '{0}' tem um formato inválido. Especifique uma URLhttps://example.com/returnbaseada em https, como ' ou para clientes nativos use o cliente nativo do IETF URI 'urna:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | O aplicativo fornecido com{0}ID ' ' não é válido contra este serviço. Por favor, use um aplicativo criado através do portal B2C e tente novamente. |
| `AADB2C90075` | A troca{0}de reivindicações '{1}' especificada na etapa{2}' retornou resposta de erro HTTP com Code ' e Reason '{3}' . |
| `AADB2C90077` | O usuário não tem uma sessão existente e o{0}parâmetro de solicitação de solicitação tem um valor de ' '. |
| `AADB2C90079` | Os clientes devem enviar um client_secret ao resgatar uma concessão confidencial. |
| `AADB2C90080` | A concessão prevista expirou. Por favor, re-autenticar e tentar novamente. Tempo atual: {0}, Tempo {1}de concessão emitido: {2}, Conceder tempo de expiração da janela deslizante: . |
| `AADB2C90081` | O client_secret especificado não corresponde ao valor esperado para este cliente. Por favor, corrija o client_secret e tente novamente. |
| `AADB2C90083` | A solicitação está faltando {0}parâmetro necessário: . |
| `AADB2C90084` | Os clientes públicos não devem enviar um client_secret ao resgatar uma subvenção adquirida publicamente. |
| `AADB2C90085` | O serviço encontrou um erro interno. Por favor, reautenticar e tentar novamente. |
| `AADB2C90086` | O grant_type{0}fornecido não é suportado. |
| `AADB2C90087` | A subvenção fornecida não foi emitida para esta versão do ponto final do protocolo. |
| `AADB2C90088` | A subvenção fornecida não foi emitida para este ponto final. Valor Real: {0} e Valor Esperado:{1} |
| `AADB2C90092` | O aplicativo fornecido com{0}iD ' '{1}é desativado para o inquilino ' ' ' . Por favor, ative o aplicativo e tente novamente. |
| `AADB2C90107` | O aplicativo com{0}ID ' ' não pode obter um token de ID também porque o escopo openid não foi fornecido na solicitação ou o aplicativo não está autorizado para isso. |
| `AADB2C90108` | A etapa de{0}orquestração ' ' não especifica um CpimIssuerTechnicalProfileReferenceId quando se esperava. |
| `AADB2C90110` | O parâmetro de escopo deve incluir 'openid' ao solicitar um response_type que inclua 'id_token'. |
| `AADB2C90111` | Sua conta foi bloqueada. Contate seu suporte para desbloqueá-la e tente novamente. |
| `AADB2C90114` | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente depois. |
| `AADB2C90115` | Ao solicitar o 'código' response_type, o parâmetro de escopo deve incluir um recurso ou ID do cliente para tokens de acesso e 'openid' para tokens de ID. Além disso, inclua 'offline_access' para tokens de atualização. |
| `AADB2C90117` | O escopo{0}' ' fornecido no pedido não é suportado. |
| `AADB2C90118` | O usuário esqueceu sua senha. |
| `AADB2C90120` | O parâmetro de{0}idade máxima ' especificado na solicitação é inválido. A idade máxima deve ser{1}um inteiro{2}entre ' e ' inclusive ' inclusive. |
| `AADB2C90122` | A entrada{0}para ' ' recebida na solicitação falhou na validação da solicitação HTTP. Certifique-se de que a entrada não contém caracteres como < ou &. |
| `AADB2C90128` | A conta associada a essa concessão não existe mais. Por favor, reautenticar e tentar novamente. |
| `AADB2C90129` | A concessão foi revogada. Por favor, reautenticar e tentar novamente. |
| `AADB2C90145` | Nenhum número de telefone não verificado foi encontrado e a política não permite que um usuário digite número. |
| `AADB2C90146` | O escopo{0}' fornecido na solicitação especifica mais de um recurso para um token de acesso, que não é suportado. |
| `AADB2C90149` | Script{0}' ' não conseguiu carregar. |
| `AADB2C90151` | O usuário excedeu o número máximo para repetições para autenticação multifatorial. |
| `AADB2C90152` | Uma solicitação de pesquisa multifatorial não obteve uma resposta do serviço. |
| `AADB2C90154` | Uma solicitação de verificação de vários fatores não conseguiu obter um id de sessão do serviço. |
| `AADB2C90155` | Uma solicitação de verificação de{0}vários fatores falhou com a razão ' '. |
| `AADB2C90156` | Uma solicitação de validação de vários{0}fatores falhou com a razão ' '. |
| `AADB2C90157` | O usuário excedeu o número máximo para repetições para uma etapa auto-afirmada. |
| `AADB2C90158` | Um pedido de validação auto-afirmado{0}falhou com a razão ' '. |
| `AADB2C90159` | Um pedido de verificação auto-afirmado falhou com a razão '{0}'. |
| `AADB2C90161` | Uma resposta de envio auto-afirmada{0}falhou com a razão ' '. |
| `AADB2C90165` | A mensagem de início saml com{0}id ' ' não pode ser encontrada no estado. |
| `AADB2C90168` | A solicitação HTTP-Redirect não contém o{0}parâmetro necessário ' ' para uma solicitação assinada. |
| `AADB2C90178` | O certificado{0}de assinatura não tem chave privada. |
| `AADB2C90182` | O code_verifier fornecido não corresponde ao code_challenge associado |
| `AADB2C90183` | O code_verifier fornecido é inválido |
| `AADB2C90184` | O code_challenge_method fornecido não é suportado. Os valores suportados são simples ou S256 |
| `AADB2C90188` | O perfil técnico SAML '{0}especifica uma{1}URL PartnerEntity de ' ',{2}mas buscar os metadados falha com a razão '. |
| `AADB2C90194` | A{0}alegação ' ' especificada para o token do portador não está presente nas reivindicações disponíveis. Reivindicações{1}disponíveis ' '. |
| `AADB2C90205` | Este aplicativo não tem permissões suficientes contra este recurso web para executar a operação. |
| `AADB2C90206` | Um intervalo ocorreu inicializando o cliente. |
| `AADB2C90208` | O parâmetro de id_token_hint fornecido está expirado. Por favor, forneça outro token e tente novamente. |
| `AADB2C90209` | O parâmetro id_token_hint fornecido não contém uma audiência aceita. Valores de{0}audiência válidos: ' '. Por favor, forneça outro token e tente novamente. |
| `AADB2C90210` | O parâmetro id_token_hint fornecido não pôde ser validado. Por favor, forneça outro token e tente novamente. |
| `AADB2C90211` | A solicitação continha um cookie de estado incompleto. |
| `AADB2C90212` | A solicitação continha um cookie de estado inválido. |
| `AADB2C90220` | O recipiente-chave{0}no inquilino '{1}com identificador de armazenamento ' existe, mas não contém um certificado válido. O certificado pode estar vencido ou seu certificado pode se tornar ativo no futuro (nbf). |
| `AADB2C90223` | Ocorreu um erro na higienização do recurso cors. |
| `AADB2C90224` | O fluxo do proprietário do recurso não foi habilitado para o aplicativo. |
| `AADB2C90225` | O nome de usuário ou senha fornecido na solicitação é inválido. |
| `AADB2C90226` | A exchange de token especificada só é suportada no HTTP POST. |
| `AADB2C90232` | O parâmetro id_token_hint fornecido não contém um emissor aceito. Emissores válidos:{0}' '. Por favor, forneça outro token e tente novamente. |
| `AADB2C90233` | O parâmetro id_token_hint fornecido falhou na validação da assinatura. Por favor, forneça outro token e tente novamente. |
| `AADB2C90235` | O id_token fornecido está vencido. Por favor, forneça outro token e tente novamente. |
| `AADB2C90237` | O id_token fornecido não contém uma audiência válida. Valores de{0}audiência válidos: ' '. Por favor, forneça outro token e tente novamente. |
| `AADB2C90238` | O id_token fornecido não contém um emissor válido. Valores do emissor{0}válidos: ' '. Por favor, forneça outro token e tente novamente. |
| `AADB2C90239` | O fornecido id_token a validação de assinatura falha. Por favor, forneça outro token e tente novamente. |
| `AADB2C90240` | O id_token fornecido é malformado e não pode ser analisado. Por favor, forneça outro token e tente novamente. |
| `AADB2C90242` | O perfil técnico SAML '{0}especifica PartnerEntity CDATA que{1}não pode ser carregado por motivo '. |
| `AADB2C90243` | A chave/segredo do cliente do IDP não está configurada corretamente. |
| `AADB2C90244` | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |
| `AADB2C90248` | O fluxo do proprietário de recursos só pode ser usado por aplicativos criados através do portal de admin B2C. |
| `AADB2C90250` | O ponto final de login genérico não é suportado. |
| `AADB2C90255` | A troca de sinistros{0}especificada no perfil técnico ' ' não completou como esperado. Você pode querer tentar começar sua sessão mais do início. |
| `AADB2C90261` | A exchange{0}de reivindicações '{1}especificada na etapa ' retornou a resposta de erro HTTP que não pôde ser analisado. |
| `AADB2C90272` | O parâmetro id_token_hint não foi especificado na solicitação. Por favor, forneça token e tente novamente. |
| `AADB2C90273` | Uma resposta inválida foi{0}recebida: ' ' |
| `AADB2C90274` | Os metadados do provedor não especificam um único serviço de logout ou a vinculação de ponto final não é uma das 'urna:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect' ou 'urna:oasis:names:tc:SAML:2:bindings:HTTP-POST'. |
| `AADB2C90276` | O pedido não é consistente{0}com a{1}configuração de{2}controle '{3}':{4}' ' em tecnicProfile ' para política ' ' inquilino '. |
| `AADB2C90277` | A etapa de{0}orquestração '{1}de '{2}de jornada do usuário ' de política ' ' ' não contém uma referência de definição de conteúdo. |
| `AADB2C90279` | O id do{0}cliente fornecido ' ' não corresponde à id do cliente que emitiu a concessão. |
| `AADB2C90284` | O aplicativo com{0}identificador ' não foi concedido consentimento e não pode ser usado para contas locais. |
| `AADB2C90285` | O aplicativo com{0}identificador ' ' não foi encontrado. |
| `AADB2C90288` | UserJourney com id{0}' referenciado{1}em TechnicalProfile ' para{2}resgate de token{3}de atualização para inquilino ' ' não existe na política ' ou qualquer uma de suas políticas básicas. |
| `AADB2C90289` | Encontramos um erro de conexão com o provedor de identidade. Tente novamente mais tarde. |
| `AADB2C90296` | O aplicativo não foi configurado corretamente. Entre em contato com o administrador do site que você está tentando acessar. |
| `AADB2C99005` | A solicitação contém um parâmetro de escopo inválido{0}que inclui um caractere ilegal ' '. |
| `AADB2C99006` | O Azure AD B2C não consegue encontrar{0}o aplicativo de extensões com o id do aplicativo ' '. Por https://go.microsoft.com/fwlink/?linkid=851224 favor, visite para mais informações. |
| `AADB2C99011` | O valor dos{0}metadados ' ' não{1}foi especificado{2}no TechnicalProfile ' na política ' ' . |
| `AADB2C99013` | A combinação{0}de grant_type{1}e token_type fornecidas não é suportada. |
| `AADB2C99015` | O{0}perfil '{1}' in{2}policy ' in tenant ' está faltando todas as InputClaims necessárias para o fluxo de credencial de senha do proprietário de recursos. |
