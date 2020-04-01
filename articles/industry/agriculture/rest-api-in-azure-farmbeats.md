---
title: Referências
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 717a09d8377a7b95fe24300cc65222f307e7419f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437518"
---
# <a name="references"></a>Referências

Este artigo descreve as APIs Azure FarmBeats.

## <a name="rest-api"></a>API REST

As APIs Azure FarmBeats fornecem às empresas agrícolas uma interface RESTful padronizada com respostas baseadas em JSON para ajudá-lo a tirar proveito dos recursos do Azure FarmBeats, tais como:

- APIs para obter sensores, câmera, drone, tempo, satélite e dados terrestres curados.
- Normalização e contextualização de dados entre provedores de dados comuns.
- Recursos de acesso e consulta esquematizados em todos os dados ingeridos.
- Geração automática de metadados que podem ser consultados, com base em características agronômicas.
- Os agregados de seqüência de tempo gerados automaticamente para a construção rápida do modelo.
- Mecanismo integrado da Fábrica de Dados Do Azure para construir facilmente pipelines personalizados de processamento de dados.

## <a name="application-development"></a>Desenvolvimento de aplicativo

As APIs FarmBeats contêm documentação técnica da Swagger. Para obter informações sobre todas as APIs e suas solicitações ou respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

A tabela a seguir resume todos os objetos e recursos no FarmBeats Datahub:

| Objetos e recursos | Descrição
--- | ---|
Farm | A fazenda corresponde a um local físico de interesse dentro do sistema FarmBeats. Cada fazenda tem um nome de fazenda e uma identificação única da fazenda. |
Dispositivo  | O dispositivo corresponde a um dispositivo físico presente na fazenda. Cada dispositivo tem um ID de dispositivo único. Um dispositivo é tipicamente provisionado para uma fazenda com um ID de fazenda.
DeviceModel  | DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é gateway ou nó.
Sensor  | O sensor corresponde a um sensor físico que registra valores. Um sensor é normalmente conectado a um dispositivo com um ID do dispositivo.
Modelo de sensor  | SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
Telemetria  | A telemetria fornece a capacidade de ler mensagens de telemetria para um determinado sensor e intervalo de tempo.
Trabalho  | O trabalho corresponde a qualquer fluxo de trabalho de atividades que são executadas no sistema FarmBeats para obter uma saída desejada. Cada trabalho está associado a um id de trabalho e tipo de trabalho.
JobType  | JobType corresponde a diferentes tipos de trabalho suportados pelo sistema. Os tipos de trabalho definidos pelo sistema e definidos pelo usuário estão incluídos.
ExtendedType  | ExtendedType corresponde à lista de tipos definidos pelo sistema e pelo usuário no sistema. ExtendedType ajuda a configurar um novo sensor, cena ou tipo de arquivo de cena no sistema FarmBeats.
Partner (parceiro)  | O parceiro corresponde ao parceiro de integração de sensores e imagens da FarmBeats.
Cena  | A cena corresponde a qualquer produção gerada no contexto de uma fazenda. Cada cena tem uma id de cena, fonte de cena, tipo de cena, e iD da fazenda associada a ela. Cada id de cena pode ter vários arquivos de cena associados a ele.
Arquivo de cena |SceneFile corresponde a todos os arquivos gerados para uma única cena. Uma única cena ID pode ter vários IDs de SceneFile associados a ele.
Regra  |A regra corresponde a uma condição para que dados relacionados à fazenda acionem um alerta. Cada regra está no contexto dos dados de uma fazenda.
Alerta  | O alerta corresponde a uma notificação, que é gerada quando uma condição de regra é atendida. Cada alerta está no contexto de uma regra.
RoleDefinition  | RoleDefinition define ações permitidas e proibidas para um papel.
RoleAssignment  |RoleAssignment corresponde à atribuição de uma função para um usuário ou um diretor de serviço.

### <a name="data-format"></a>Formato de dados

JSON é um formato de dados independente de linguagem comum que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte o site da [JSON.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Autenticação e autorização

As solicitações HTTP para a API REST são protegidas com o Azure Active Directory (Azure AD).
Para fazer uma solicitação autenticada às APIs REST, o código do cliente requer autenticação com credenciais válidas antes de poder chamar a API. A autenticação é coordenada entre os vários atores pelo Azure AD. Ele fornece ao seu cliente um token de acesso como prova da autenticação. O token é então enviado no cabeçalho de autorização HTTP de solicitações de API REST. Para saber mais sobre a autenticação do Azure AD, consulte [o Azure Active Directory](https://portal.azure.com) para desenvolvedores.

O token de acesso deve ser enviado em solicitações de API subseqüentes, na seção de cabeçalho, como:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Cabeçalhos de solicitação HTTP

Aqui estão os cabeçalhos de solicitação mais comuns que você deve especificar quando você faz uma chamada de API para o Azure FarmBeats Datahub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo  | O formato da solicitação (Tipo<format>de conteúdo: aplicação/ ). Para APIs do Azure FarmBeats Datahub, o formato é JSON. Content-Type: application/json
Autorização  | Especifica o token de acesso necessário para fazer uma chamada de API. Autorização:> de acesso <do Portador
Aceitar | O formato da resposta. Para APIs do Azure FarmBeats Datahub, o formato é JSON. Aceitar: aplicação/json

### <a name="api-requests"></a>Pedidos de API

Para fazer uma solicitação de API REST, você combina o método HTTP (GET, POST, PUT ou DELETE), o URL para o serviço de API, o URI a um recurso para consultar, enviar dados, atualizar ou excluir e, em seguida, adicionar um ou mais cabeçalhos de solicitação HTTP.

O URL para o serviço de API é\<sua URL do Datahub, por exemplo, https:// seu nome de site datahub>.azurewebsites.net.

Opcionalmente, você pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

A seguinte solicitação de amostra é usada para obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT requerem um órgão de solicitação JSON.

A seguinte solicitação de amostra cria um dispositivo. Esta solicitação tem entrada JSON com o órgão de solicitação.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parâmetros de consulta

Para chamadas REST GET, você pode filtrar, limitar o tamanho e classificar os dados em uma resposta de API, incluindo um ou mais parâmetros de consulta no URI de solicitação. Para obter os parâmetros de consulta, consulte a documentação da API e as chamadas individuais GET.
Por exemplo, quando você consulta a lista de dispositivos (GET call on /Device), os seguintes parâmetros de consulta podem ser especificados:

![Lista de dispositivos](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Tratamento de erros

ApIs do Azure FarmBeats Datahub retornam os erros HTTP padrão. Os códigos de erro mais comuns são os seguintes:

 |Código do erro             | Descrição |
 |---                    | --- |
 |200                    | Sucesso |
 |201                    | Criar (Pós) Sucesso |
 |400                    | Solicitação inválida. Há um erro na solicitação. |
 |401                    | Não autorizado. O chamador da API não está autorizado a acessar o recurso. |
 |404                    | Recurso não encontrado |
 |5XX                    | Erro do servidor interno. Os códigos de erro que começam com 5XX significa que há algum erro no servidor. Consulte os registros do servidor e a seção a seguir para obter mais detalhes. |


Além dos erros HTTP padrão, as APIs do Azure FarmBeats Datahub também retornam erros internos no seguinte formato:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Neste exemplo, quando uma fazenda foi criada, o campo obrigatório "Nome" não foi especificado na carga de entrada. A mensagem de erro resultante seria:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Adicione usuários ou registros de aplicativos ao Azure Active Directory

As APIs do Azure FarmBeats podem ser acessadas por um usuário ou um registro de aplicativo no Azure Active Directory. Para criar um registro de aplicativo no Azure Active Directory, siga estas etapas:

1. Acesse o [portal do Azure](https://portal.azure.com)e selecione inscrições > do**Aplicativo**de >  **Diretório Ativo do Azure**Novo**registro**. Alternativamente, você pode usar uma conta existente.
2. Para uma nova conta, faça o seguinte:

    - Insira um nome.
    - Selecione **Contas apenas neste diretório organizacional (único inquilino)**.
    - Use os valores padrão no resto dos campos.
    - Selecione **Registrar**.

3. No painel de visão **geral** de registro de aplicativos novo e existente, faça o seguinte:

    - Capture o **ID** do cliente e **o ID do inquilino**.
    - Vá para **Certificados e Segredos** para gerar um novo segredo de cliente e capturar o **Cliente-Segredo**.
    - Volte para **Visão Geral**e selecione o link ao lado de Gerenciar o aplicativo no diretório **local**.
    - Vá para **Propriedades** para capturar o **ID do objeto**.

4. Vá para o seu<yourdatahub>Datahub Swagger (https:// .azurewebsites.net/swagger/index.html) e faça o seguinte:
    - Vá para a **API RoleAssignment**.
    - Execute um POST para criar um objeto **RoleAssignment** para o **ID de objeto** que você acabou de criar.

  > [!NOTE]
  > Para obter mais informações sobre como adicionar usuários e registro do Active Directory, consulte [O Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Depois de terminar as etapas anteriores, o registro do aplicativo (cliente) pode ligar para as APIs do Azure FarmBeats usando um token de acesso via autenticação do portador.

Use o token de acesso para enviá-lo em solicitações de API subseqüentes na seção de cabeçalho como:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
