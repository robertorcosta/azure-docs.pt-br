---
title: Habilitar o TLS de ponta a ponta no Gateway de Aplicativo Azure
description: Este artigo é uma visão geral do suporte a TLS de ponta a ponta do gateway de aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 286c9329be38055808571d8d32c724d27a61cbf3
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855883"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral do término do TLS e TLS de ponta a ponta com o gateway de aplicativo

O protocolo TLS, anteriormente conhecido como protocolo SSL (SSL), é a tecnologia de segurança padrão para estabelecer um vínculo criptografado entre um servidor Web e um navegador. Esse link garante que todos os dados passados entre o servidor Web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo dá suporte ao encerramento de TLS no gateway, bem como à criptografia TLS de ponta a ponta.

## <a name="tls-termination"></a>Encerramento de TLS

O gateway de aplicativo dá suporte à terminação TLS no gateway, após o qual o tráfego normalmente flui sem criptografia para os servidores de back-end. Há várias vantagens de fazer o término do TLS no gateway de aplicativo:

- **Melhor desempenho** – o maior impacto de desempenho ao fazer a descriptografia de TLS é o handshake inicial. Para melhorar o desempenho, o servidor que faz a descriptografia armazena em cache as IDs da sessão TLS e gerencia os tíquetes da sessão TLS. Se isso for feito no gateway de aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se isso for feito nos servidores de back-end, sempre que as solicitações do cliente passarem para um servidor diferente, o cliente deverá se autenticar novamente. O uso de tíquetes TLS pode ajudar a atenuar esse problema, mas eles não têm suporte de todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores back-end – o** processamento de SSL/TLS é muito intensivo de CPU e está ficando mais intensivo à medida que os tamanhos de chave aumentam. A remoção desse trabalho dos servidores de back-end permite que eles se concentrem no que são mais eficientes no fornecimento de conteúdo.
- **Roteamento inteligente** – descriptografando o tráfego, o gateway de aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para rotear solicitações.
- **Gerenciamento de certificados** – os certificados só precisam ser comprados e instalados no gateway de aplicativo e não em todos os servidores de back-end. Isso economiza tempo e dinheiro.

Para configurar a terminação de TLS, é necessário que um certificado TLS/SSL seja adicionado ao ouvinte para permitir que o gateway de aplicativo derive uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado TLS/SSL precisa estar no formato PFX (troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

> [!IMPORTANT] 
> Observe que o certificado no ouvinte requer que toda a cadeia de certificados seja carregada. 


> [!NOTE] 
>
> O gateway de aplicativo não fornece nenhum recurso para criar um novo certificado ou enviar uma solicitação de certificado a uma autoridade de certificação.

Para que a conexão TLS funcione, você precisa garantir que o certificado TLS/SSL atenda às seguintes condições:

- Que a data e a hora atuais estão dentro do intervalo de datas "válido de" e "válido para" no certificado.
- o "Common Name" (CN) do certificado corresponde ao cabeçalho de host na solicitação. Por exemplo, se o cliente estiver fazendo uma solicitação para `https://www.contoso.com/`, o CN deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificados com suporte para terminação de TLS

O gateway de aplicativo dá suporte aos seguintes tipos de certificados:

- Certificado de AC (autoridade de certificação): um certificado de autoridade de certificação é um certificado digital emitido por uma autoridade de certificação (CA)
- Certificado EV (validação estendida): um certificado EV é um certificado que está em conformidade com as diretrizes de certificado padrão do setor. Isso tornará a barra de localizador do navegador verde e publicará o nome da empresa também.
- Certificado curinga: esse certificado dá suporte a qualquer número de subdomínios com base em *. site.com, em que o subdomínio substituiria o *. No entanto, ele não dá suporte a site.com, portanto, caso os usuários estejam acessando seu site sem digitar o "www" líder, o certificado curinga não abordará isso.
- Certificados autoassinados: os navegadores cliente não confiam nesses certificados e avisam o usuário de que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Os certificados autoassinados são bons para testes ou ambientes em que os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados autoassinados.

Para obter mais informações, consulte [Configurar a terminação de TLS com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [limites do gateway de aplicativo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo de certificado TLS/SSL com suporte.

## <a name="end-to-end-tls-encryption"></a>Criptografia TLS de ponta a ponta

Alguns clientes podem não desejar comunicação não criptografada com os servidores de back-end. Isso pode ocorrer devido a requisitos de segurança, requisitos de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para esses aplicativos, o gateway de aplicativo dá suporte à criptografia TLS de ponta a ponta.

O TLS de ponta a ponta permite que você transmita com segurança dados confidenciais para o back-end criptografado enquanto ainda aproveita os benefícios dos recursos de balanceamento de carga de camada 7 que o gateway de aplicativo fornece. Alguns desses recursos têm uma afinidade de sessão baseada em cookies, um roteamento baseado em URL, suporte para o roteamento com base em sites ou a capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação TLS de ponta a ponta, o gateway de aplicativo encerra as sessões de TLS no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. Em seguida, o gateway de aplicativo inicia uma nova conexão TLS com o servidor back-end e criptografa novamente os dados usando o certificado de chave pública do servidor back-end antes de transmitir a solicitação para o back-end. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final. O TLS de ponta a ponta é habilitado pela definição da configuração de protocolo na [configuração de http de back-end](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) como https, que é então aplicada a um pool de back-end.

A política TLS aplica-se ao tráfego de front-end e backend. No front-end, o gateway de aplicativo atua como o servidor e impõe a política. No back-end, o gateway de aplicativo atua como o cliente e envia as informações de protocolo/codificação como a preferência durante o handshake de TLS.

O gateway de aplicativo se comunica apenas com as instâncias de back-end que têm o certificado na lista de permissões com o gateway de aplicativo ou cujos certificados são assinados por autoridades de certificação conhecidas em que o certificado CN corresponde ao nome do host nas configurações de back-end HTTP. Isso inclui os serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

Se os certificados dos membros no pool de back-end não forem assinados por autoridades de certificação bem conhecidas, cada instância no pool de back-end com TLS de ponta a ponta habilitado deverá ser configurada com um certificado para permitir a comunicação segura. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como Azure App aplicativos Web do serviço e o gerenciamento de API do Azure.

> [!NOTE] 
>
> O certificado adicionado à **configuração de http de back-end** para autenticar os servidores de back-end pode ser o mesmo que o certificado adicionado ao **ouvinte** para terminação TLS no gateway de aplicativo ou diferente para aumentar a segurança.

![cenário de TLS de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS 1.2 são roteadas para servidores de back-end no Pool1 usando o TLS de ponta a ponta.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>TLS de ponta a ponta e lista de permissões de certificados

O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Serviço de Aplicativo do Azure.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS de ponta a ponta com a SKU v2

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Certificados assinados por autoridades de certificação bem conhecidas cujo CN corresponde ao nome do host nas configurações de back-end HTTP não exigem nenhuma etapa adicional para que o TLS de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS e a investigação de integridade e o caminho de dados seriam habilitados para TLS. Se você estiver usando Azure App serviço ou outros serviços Web do Azure como seu back-end, eles serão implicitamente confiáveis e nenhuma outra etapa será necessária para o TLS de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado TLS/SSL seja confiável, esse certificado do servidor back-end deve ter sido emitido por uma autoridade de certificação incluída no repositório confiável do gateway de aplicativo. se o certificado não tiver sido emitido por uma autoridade de certificação confiável, o gateway de aplicativo verificará se o certificado da AC emissora foi emitido por uma autoridade de certificação confiável e assim por diante até que uma AC confiável seja encontrada (ponto em que uma conexão confiável, segura será estabelecida) ou nenhuma AC confiável possa ser encontrada (nesse ponto, o gateway de aplicativo marcará o back-end como não íntegro). Portanto, é recomendável que o certificado do servidor back-end contenha as CAs raiz e intermediária.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, para habilitar o TLS de ponta a ponta no SKU v2, um certificado raiz confiável deverá ser definido. O Gateway de Aplicativo só se comunicará com back-ends cujo certificado raiz do certificado do servidor corresponda a um da lista de certificados raiz confiáveis na configuração http de back-end associada ao pool.

> [!NOTE] 
>
> O certificado autoassinado deve fazer parte de uma cadeia de certificados. Não há suporte para um único certificado autoassinado sem nenhuma cadeia no SKU v2.

- Além da correspondência de certificado raiz, o gateway de aplicativo também valida se a configuração de host especificada na configuração http de back-end corresponde à do nome comum (CN) apresentado pelo certificado TLS/SSL do servidor de back-end. Ao tentar estabelecer uma conexão TLS com o back-end, o gateway de aplicativo define a extensão Indicação de Nome de Servidor (SNI) para o host especificado na configuração http de back-end.
- Se **escolher nome do host do endereço de back-end** for escolhido em vez do campo host na configuração http de back-end, o cabeçalho SNI sempre será definido como o FQDN do pool de back-end e o CN no certificado TLS/SSL do servidor de back-end deverá corresponder ao FQDN. Não há suporte para membros do pool de back-end com IPs neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre o TLS de ponta a ponta, vá para [Configurar o TLS de ponta a ponta usando o gateway de aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicativo usando o TLS de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
