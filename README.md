# Dicionários de Integração para API Audaces ERP

## Índice
1. [Introdução](#introdução)
2. [Estrutura dos Dicionários](#estrutura-dos-dicionários)
3. [Endpoints](#endpoints)
4. [Exemplo de Configuração](#exemplo-de-configuração)
5. [Formatos JSON e XML](#formatos-json-e-xml)
6. [Convenções](#convenções)
7. [Considerações Finais](#considerações-finais)

## Introdução

Este projeto fornece uma estrutura padronizada para integração da **API Audaces ERP** com diferentes sistemas de gestão empresarial (ERPs). A API Audaces é um serviço web **RESTful** que utiliza o formato **JSON** como padrão de comunicação, mas também suporta integrações com ERPs que utilizam **XML**. 

A integração é realizada por meio de **dicionários**, que são arquivos de configuração que mapeiam os endpoints da API Audaces para os endpoints correspondentes do ERP. Esses dicionários permitem que a API traduza as requisições e respostas entre os dois sistemas, garantindo uma comunicação eficiente e consistente.


### Objetivo

O objetivo deste projeto é simplificar e padronizar o processo de integração com diferentes ERPs, fornecendo:

- **Flexibilidade**: Suporte para ERPs que utilizam JSON ou XML.
- **Escalabilidade**: Adição de novos ERPs com facilidade, apenas criando novos dicionários.
- **Padronização**: Estrutura consistente para todos os dicionários, facilitando a manutenção e o entendimento.

### Como Funciona

1. **Dicionários JSON**: Para ERPs que utilizam JSON, é necessário apenas um dicionário no formato JSON.
2. **Dicionários JSON e XML**: Para ERPs que utilizam XML, são necessários dois dicionários:
   - Um no formato XML para comunicação com o ERP.
   - Outro no formato JSON para traduzir as respostas do ERP para o formato da API Audaces.

Cada dicionário contém informações detalhadas sobre os endpoints do ERP, como:
- URL do endpoint.
- Método HTTP (GET, POST, PUT, DELETE).
- Parâmetros de consulta (query parameters).
- Parâmetros do corpo da requisição (body parameters).
- Estrutura de saída (output) para mapear os dados retornados pelo ERP.

### Benefícios

- **Redução de Esforço**: Evita a necessidade de desenvolver integrações personalizadas para cada ERP.
- **Manutenção Simplificada**: Alterações nos endpoints do ERP podem ser feitas diretamente nos dicionários, sem necessidade de alterar o código da API.
- **Compatibilidade**: Suporte para uma ampla variedade de ERPs, independentemente do formato de comunicação.

### Público-Alvo

Este projeto é destinado a desenvolvedores e integradores que precisam conectar a API Audaces a diferentes sistemas ERP. Ele é especialmente útil para empresas que utilizam múltiplos ERPs e desejam centralizar a integração em uma única solução.


## Estrutura dos Dicionários

Os dicionários são arquivos de configuração que definem como a API Audaces ERP interage com os diferentes sistemas ERP. Eles mapeiam os endpoints da API Audaces para os endpoints correspondentes do ERP, especificando os detalhes necessários para realizar requisições e interpretar as respostas. A estrutura dos dicionários é padronizada, garantindo consistência e facilidade de manutenção.

### Estrutura Geral

Um dicionário é composto por um conjunto de chaves no primeiro nível, onde cada chave representa um endpoint da API Audaces. Abaixo está um exemplo básico de um dicionário:

```json
{
    "auth" : { },
    "raw_material" : { },
    "activity" : { },
    "finished_product" : { },
    "aditional_customfields_list" : { },
    "variants" : { },
    "garment" : { },
    "picture" : { },
    "picture_save" : { },
    "orders" : { }
}
```
Descrição dos Endpoints

Cada chave no primeiro nível do dicionário representa uma funcionalidade específica da API Audaces:

- **auth**: Contém as informações necessárias para autenticar na API do ERP.
- **raw_material**: Define como obter materiais do ERP.
- **activity**: Define como obter atividades do ERP (somente leitura).
- **finished_product**: Define como obter modelos do ERP (somente leitura).
- **aditional_customfields_list**: Define como obter campos personalizados do ERP (somente leitura).
- **variants**: Define como obter variantes do P (somente leitura).
- **picture_save**: Define como enviar imageERP (somente leitura).
- **garment**: Define como enviar dados de modelos para o ERP (somente escrita).
- **picture**: Define como obter imagens do ERns para o ERP (somente escrita).
- **orders**: Define como obter ordens de compra do ERP (somente leitura).


## Endpoints

Estrutura de Cada Endpoint

Cada endpoint segue uma estrutura padronizada, como mostrado abaixo:

```json
{    
    "endpoint" : "",
    "method" : "POST",
    "send_as_json" : false,
    "queryparam" : { },
    "bodyparam" : { },
    "output" : { }
}
```

**Descrição dos Campos**

- **endpoint**: (string) URL do endpoint da API do ERP.
- **method**: (string) Método HTTP utilizado para a requisição. Valores possíveis: POST, GET, PUT, DELETE.
- **send_as_json**: (boolean) Indica se a requisição ao ERP deve ser enviada no formato JSON. Valores possíveis: true, false.
- **queryparam**: (objeto) Parâmetros enviados na query string da requisição. Caso não sejam necessários, deve ser um objeto vazio { }.
- **bodyparam**: (objeto) Parâmetros enviados no corpo da requisição. Caso não sejam necessários, deve ser um objeto vazio { }.
- **output**: (objeto) Define como os dados retornados pelo ERP serão interpretados pela API.

Detalhes do Campo `output`

O campo `output` é responsável por mapear os dados retornados pelo ERP para o formato esperado pela API Audaces. Ele possui a seguinte estrutura:

```json
{
    "result_list_key": "items",
    "keys": {
        "uid": "productCode",
        "name": "productName",
        "reference": "referenceName",
        "description": "description",
        "value": "1.0",
        "measure_unit": "measuredUnit",
        "product_group": "referenceName",
        "suplier": "suppliersName",
        "notes": "descriptive"
    },
    "custom_keys": {
        "reference_code": "ReferenceCode",
        "Observação": "referenceName",
        "fornecedores": {
            "suppliers": {
                "type": "string",
                "value": "",
                "editable": "false",
                "options": "name"
            }
        }
    }
}
```

**Descrição dos Subcampos de _output_**

- **result_list_key**: (string) Nome da chave que contém a lista de resultados na resposta do ERP. Exemplo: "items".
- **keys**: (objeto) Mapeia os campos obrigatórios da API Audaces para os campos correspondentes no ERP. As chaves são fixas na API Audaces, enquanto os valores correspondem às chaves do ERP.
- **custom_keys**: (objeto) Define campos adicionais que não são obrigatórios na API Audaces, mas que podem ser úteis. Pode ter dois formatos:
    - `"chave": "valor"`: Mapeia diretamente um campo adicional.
    - `"chave": { "objeto" }`: Utilizado quando o valor retornado pelo ERP é um array ou objeto complexo.

Exemplo de Configuração de Endpoint

Suponha que um ERP retorne o seguinte JSON ao fazer uma requisição GET para https://api.exemplo.com/materiais?page=1&order=1:

```json
{
    "count": 4,
    "totalPages": 1,
    "items": [
        {
            "productCode": "123",
            "productName": "Produto Exemplo",
            "referenceName": "REF123",
            "description": "Descrição do Produto",
            "cst": "10.00",
            "measuredUnit": "UN",
            "suppliers_name": "Fornecedor Exemplo",
            "descriptive": "Observação"
        }
    ]
}
```

O dicionário para este endpoint seria configurado assim:

```json
{
    "finished_product": {
        "endpoint": "https://api.exemplo.com/materiais",
        "method": "GET",
        "send_as_json": true,
        "queryparam": {
            "page": 1,
            "order": "1"
        },
        "bodyparam": { },
        "output": {
            "result_list_key": "items",
            "keys": {
                "uid": "productCode",
                "name": "productName",
                "reference": "referenceName",
                "description": "description",
                "value": "cst",
                "measure_unit": "measuredUnit",
                "product_group": "referenceName",
                "suplier": "suppliers_name",
                "notes": "descriptive"
            }
        }
    }
}
```

### Endpoint de Autenticação

Caso o ERP necessite de autenticação, o endpoint `auth` deve ser implementado

```json
{
    "auth" : {
        "endpoint" : "https://api.exemplo.com/login",
        "bodyparam" : {
        },
        "queryparam" : {
        },
        "method" : "POST",
        "send_as_json" : false,
        "headers" : {
            "accept": "application/json",
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "expires" : "expires_in"
    }
}
```

Por ser um dicionário global, os dados de autenticação do usuário, como login e senha NÃO devem estar presentes no dicionário. Eles serão identificados pelas tags: `{erp_user}` e `{erp_password}`. E serão substituidas pelas credenciais durante a execução da requisição. Exemplo de uso:

```json
{
    "auth" : {
        "endpoint" : "https://api.exemplo.com/login",
        "bodyparam" : {
            "nome": "{erp_user}",
            "senha": "{erp_password}"
        },
        "queryparam" : {
        },
        "method" : "POST",
        "send_as_json" : false,
        "headers" : {
            "accept": "application/json",
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "expires" : "expires_in"
    }
}

```

Dividiremos em 2 gandes partes: entrada e saida de dados:

* Entrada de dados:
  * `endpoint` estamos chamando a url do ERP
  * `method` o tipo de chamada foi um GET
  * `send_as_json` enviado no formato json
  * `queryparam` passando os parametros
  * `bodyparam` não foi necessário
* Saída de dados:
  * `output` forma como a API interpretará os resultados


## Exemplo de configuração

Exemplo de Configuração: `finished_product`

```json
{
    "finished_product": {
        "endpoint": "https://api.erp-example.com/products",
        "method": "GET",
        "send_as_json": true,
        "queryparam": {
            "page": 1,
            "limit": 50,
            "type": "finished"
        },
        "bodyparam": { },
        "output": {
            "result_list_key": "items",
            "keys": {
                "uid": "product_id",
                "name": "product_name",
                "reference": "product_reference",
                "description": "product_description",
                "value": "price",
                "measure_unit": "unit",
                "product_group": "category",
                "suplier": "supplier_name",
                "notes": "additional_info"
            }
        }
    }
}
```

Exemplo de Configuração: `raw_material`

```json
{
    "raw_material": {
        "endpoint": "https://api.erp-example.com/materials",
        "method": "POST",
        "send_as_json": true,
        "queryparam": { },
        "bodyparam": {
            "filter": {
                "type": "raw_material",
                "status": "active"
            }
        },
        "output": {
            "result_list_key": "materials",
            "keys": {
                "uid": "material_id",
                "name": "material_name",
                "description": "material_description",
                "measure_unit": "unit",
                "stock_quantity": "quantity",
                "notes": "remarks"
            }
        }
    }
}
```

Exemplo de Configuração: `activity`

```json
{
    "activity": {
        "endpoint": "https://api.erp-example.com/activities",
        "method": "GET",
        "send_as_json": true,
        "queryparam": {
            "status": "active",
            "type": "manufacturing"
        },
        "bodyparam": { },
        "output": {
            "result_list_key": "activities",
            "keys": {
                "uid": "activity_id",
                "name": "activity_name",
                "description": "activity_description",
                "start_date": "start_date",
                "end_date": "end_date",
                "status": "status"
            }
        }
    }
}
```

## Formatos JSON e XML

Quando um erp usa formato `json`, basta ter um dicionário, como no exemplo acima.

Quando um erp usa formato `xml`, precisamo ter dois, um no formato xml (a ser enviado ao ERP) e um no formato json (para tradução do response do erp)

### XML

O formato do dicionário XML é:

```xml
<AudacesApiErp>
    <finished_product>
        (...)
    </finished_product>
    <raw_material>
        (...)
    </raw_material>
</AudacesApiErp>
```

O Documento xml deve conter como chave raiz `AudacesApiErp` e nas chaves "filhas", os tipos de entidade que a API usa. São elas:  `raw_material`, `activity`, `finished_product`, `aditional_customfields_list`, `variants`, `garment`, `picture`, `picture_save`, `orders`

Dentro de cada chave filha, deve conter os parametros que serão enviados ao ERP

Como mencionado anteriormente, há a necessidade de dicionário em formato `json` (que tem o mesmo formato já descrito), que possibilida a tradução do response do ERP. Assim:

```json
{
    "auth" : {
        (...)
    },
    "finished_product" : {
        "endpoint" : "<url_do_erp>",
        "method" : "POST",
        "send_as_json" : false,
        "bodyparam" : {
        },
        "queryparam" : {
        },
        "output" : {
            (...)
        }
    },
    (...)
}
```

**Vamos ilustrar com um exemplo:**

Supondo que temos um ERP com a seguinte caracteristica:

Ao fazer um `POST` para `https://erp-example.net` obtivemos o seguinte response
![image](https://github.com/Audaces/erp_api_dictionary/assets/525641/f3500c73-d7f2-4b66-8669-1ac18c075c23)


para este caso, teremos o seguinte par de dicionários:

Dicionário XML:
```xml
<AudacesApiErp>
    <finished_product>
        <ErpName>
            <Authentication user="{user}" password="{password}" />
            <IdUser>{IdUser}</IdUser>
            <Command>
                <Name>Modelos</Name>
                <Parameters>
                    <Parameter id="access_key">{access_key}</Parameter>
                    <Parameter id="type">bearer</Parameter>
                </Parameters>
            </Command>
        </ErpName>
    </finished_product>
</AudacesApiErp>
```

Repare que no dicionário tem valores entre chaves, exemplo `{IdUser}`. Esses valores estão na configuração do usuario.

Em outras palavas, a API tem um registro de usuarios e lá deve constar esses valores a serem substituidos. Neste exemplo, a configuração do usuario é:
```json
{
    "user": "login usano erp",
    "password": "senha do erp",
    "IdUser": "5",
    "filter": {
        "finished_product": {
            "id_setor": [1]
        },
        "raw_material": {
            "id_setor": [10,12]
        }
    }
}
```
O campo filter serve para filtrar valores.

Dicionário Json complementar:
```json
{
    "auth" : {
        "endpoint" : "https://erp-example.net",
        "bodyparam" : {
        },
        "queryparam" : {
        },
        "method" : "POST",
        "send_as_json" : false
    },
    "finished_product" : {
        "endpoint" : "https://erp-example.net",
        "method" : "POST",
        "send_as_json" : false,
        "bodyparam" : {
        },
        "queryparam" : {
        },
        "output" : {
            "result_list_key" : "",
            "keys" : {
                "uid" : "id_colecao",
                "name" : "desc_colecao"
            },
            "xml_res" : {
                "keys" : "ErpName.Response.C.D",
                "values_array" : "ErpName.Response.R",
                "values_data" : "D"
            }
        }
    }
}
```

Repare no objeto `xml_res`, ele representa os valores de response do ERP.

* `keys` é onde vem as chaves na resposta XML do ERP - `<ErpName> <Response> <C> <D> (...) </D> </C> (...) `
* values_array é o array onde vem as valores na resposta XML do ERP - `<ErpName> <Response> <R>  (...) </R> (...) `
* values_data é a chave do array de resultados, neste caso, `D` - `<ErpName> <Response> <R> <D> (...) </D> </R> (...) `


## Convenções

Para lidar com dados dinâmicos e/ou sensíveis, utilizamos convenções específicas que ajudam a identificar e processar esses valores de forma segura e padronizada. Abaixo estão as simbologias e seus respectivos usos:

### 1. **Dados Fornecidos pelos Usuários**
- Representados entre **chaves** `{}`.
- Esses dados são fornecidos diretamente pelo usuário e substituídos dinamicamente durante a execução da requisição.
- **Exemplo**: 
  - `{erp_user}`: Nome de usuário do ERP.
  - `{erp_password}`: Senha do ERP.

### 2. **Dados Fornecidos pelo Audaces IDEA**
- Representados entre **porcentagens** `%%`.
- Esses dados são gerados ou fornecidos automaticamente pelo sistema Audaces IDEA.
- **Exemplo**:
  - `%REFERENCE_ID%`: Identificador de referência gerado pelo sistema.

### 3. **Índices em Listas**
- Representados entre **colchetes** `[]`.
- Utilizados para acessar elementos específicos dentro de uma lista retornada pelo ERP.
- **Exemplo**:
  - `retorno[0].Itens`: Acessa o primeiro item da lista `Itens` no objeto `retorno`.

### 4. **Acesso a Dados em Estruturas JSON Complexas**
- Utiliza a notação de ponto `.` para navegar por objetos aninhados.
- Permite acessar valores específicos dentro de estruturas JSON com múltiplos níveis.
- **Exemplo**:
  - `variants.custom_fields.Cor.value`: Acessa o valor do campo `Cor` dentro de `custom_fields` no objeto `variants`.

### 5. **Convenções para Respostas em XML**
- Quando o ERP retorna dados no formato XML, utilizamos as seguintes convenções para mapear os valores:
  - **`keys`**: Define as chaves na resposta XML do ERP.
    - **Exemplo**: `<ErpName> <Response> <C> <D> (...) </D> </C> (...)`
  - **`values_array`**: Define o array onde os valores estão localizados na resposta XML.
    - **Exemplo**: `<ErpName> <Response> <R> (...) </R> (...)`
  - **`values_data`**: Define a chave do array de resultados.
    - **Exemplo**: `<ErpName> <Response> <R> <D> (...) </D> </R> (...)`

### Resumo das Convenções

| Simbologia       | Descrição                                      | Exemplo                          |
|-------------------|-----------------------------------------------|-----------------------------------|
| `{}`             | Dados fornecidos pelos usuários               | `{erp_user}`, `{erp_password}`   |
| `%%`             | Dados fornecidos pelo Audaces IDEA            | `%REFERENCE_ID%`                 |
| `[]`             | Índices em listas                             | `retorno[0].Itens`               |
| `.`              | Acesso a dados em JSON com múltiplos níveis   | `variants.custom_fields.Cor.value` |
| `keys`           | Chaves na resposta XML                        | `<ErpName> <Response> <C> <D>`   |
| `values_array`   | Array de valores na resposta XML              | `<ErpName> <Response> <R>`       |
| `values_data`    | Chave do array de resultados na resposta XML  | `<ErpName> <Response> <R> <D>`   |

Essas convenções garantem que os dados sejam tratados de forma consistente e segura, independentemente do formato ou da complexidade da integração com o ERP.


## Considerações Finais

A estrutura padronizada dos dicionários permite que novos ERPs sejam integrados de forma rápida e eficiente. Ao seguir este modelo, é possível garantir que a API Audaces ERP funcione de maneira consistente, independentemente das particularidades de cada ERP. Além disso, a separação clara entre entrada e saída de dados facilita a manutenção e a escalabilidade do projeto.
