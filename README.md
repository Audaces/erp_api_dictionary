# Dicionários

A API Audaces para ERP, que chamaremos a partir de agora como API, é um serviço web RESTfull no formato JSON.

Apesar disso, ela consegue se comunicar com ERPs no formato JSON e XML, atraves de uma estrutura de dados que chamamos de dicionário. 

Caso o ERP use JSON, será necessário apenas um dicionário no formato JSON (que é o formato nativo da API)

Caso o ERP use XML, será necessário um par dicionários, um no formato XML (para comunicação com o ERP) e outro no formato JSON (para fazer a tradução dos resultados)


Cada endpoint da API mapeado no dicionário, chama o endpoint correspondente do ERP para obter seus dados e o converte no formato da API.

Os possiveis endpoints que devem ser mapeados no dicionário Json são:

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

O primeiro nivel das chaves do dicionário são os endpoints expostos pela API audaces:

* auth : contem as informações para autenticar na API do ERP
* raw_material : contem as informações obter materiais do ERP
* activity : contem as informações obter atividades do ERP. É apenas para leitura de dados
* finished_product : contem as informações obter modelos do ERP. É apenas para leitura de dados
* aditional_customfields_list : contem as informações obter campos personalizados do ERP. É apenas para leitura de dados
* variants : contem as informações obter variantes do ERP. É apenas para leitura de dados
* garment : contem as informações enviar dados de modelo para o ERP. É apenas para escrita de dados
* picture : contem as informações obter imagens do ERP. É apenas para leitura de dados
* picture_save : contem as informações para enviar dados de imagem para o ERP. É apenas para escrita de dados
* orders : contem as informações obter ordens de compra do ERP. É apenas para leitura de dados

Cada chave dessas, possui a seguinte estrututa:

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

Descritos como segue:

* endpoint: tipo string, é a URL do endpoint da api do erp
* method: tipo string, é o tipo de requisição do endpoint do erp. Valores possiveis: POST, GET, PUT, DELETE
* send_as_json: tipo booleano, informa a requisição ao erp deve ser em formato json. Valores possiveis: true, false 
* queryparam: tipo objeto, são os parametros que devem ser enviados na query da requisição da api do erp. Caso não seja necessário, dele ser um objeto json vazio  ` {} `
* bodyparam: tipo objeto, são os parametros que devem ser enviados no body da requisição da api do erp. Caso não seja necessário, dele ser um objeto json vazio  ` {} `
* output: tipo objeto, contém as informações de como os dados do erp serão lidos e interpretados pela API


## Endpoint de Autenticação

Caso o ERP necessite de autenticação, o dicionário "auth" deve ser implementado

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

Por ser um dicionário global, os dados de autenticação do usuário, como login e senha, por exemplo, NÃO devem estar presentes no dicionário. Eles serão configurados na conta do serviço da API do usuario que consumirá os dados


## Demais endpoints

Vamos definir a forma de obtenção de dados cadastrados no ERP.
Este formato é valido para todos os demais endpoints descritos nesta documentação: `raw_material`, `activity`, `finished_product`, `aditional_customfields_list`, `variants`, `garment`, `picture`, `picture_save`, `orders`

Usaremos um exemplo para ilustrar a configuração de um endpoint do dicionário

Supondo um ERP hipotético, que ao fazer um GET para `https://api.exemplo.com/materiais?page=1&order=1` ele devolva o seguinte json:

```json
{
    "count": 4,
    "totalPages": 1,
    "items": [
        {
            "productCode" : "",
            "productName" : "",
            "referenceName" : "",
            "description" : "",
            "cst" : "",
            "measuredUnit" : "",
            "referenceName" : "",
            "suppliers_name" : "",
            "descriptive" : ""
        },
        {...},
        {...},
        {...}
    ]
}       

```

O dicionário para este endpoint seria:

```json
{
    "<endpoint>" : {
        "endpoint" : "https://api.exemplo.com/materiais",
        "method" : "GET",
        "send_as_json" : true,
        "queryparam" : {
            "page": 1,
            "order": "1"
        },
        "bodyparam" : {
        },
        "output" : {
            "result_list_key" : "items",
            "keys" : {
                "uid" : "productCode",
                "name" : "productName",
                "reference" : "referenceName",
                "description" : "description",
                "value" : "cst",
                "measure_unit" : "measuredUnit",
                "product_group" : "referenceName",
                "suplier" : "suppliers_name",
                "notes" : "descriptive"
            },
            "custom_keys" : {
                "reference_code": "ReferenceCode",
                "Observação": "referenceName",
                "fornecedores" : {
                    "suppliers" : {
                        "type": "string", 
                        "value": "", 
                        "editable": "false", 
                        "options": "name"
                    }
                }
            }
        }
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

Agora vamos detalhar a chave `output`, que são os dados de retorno do erp:


* result_list_key: a resposta da chamada do ERP trouxe um objeto com dados da requisição e a lista de dados. `result_list_key` é o nome da chave onde vem dos dados do erp, que neste exemplo é `items`
* keys: é um objeto json e representa os campos mandatórios da API. As chaves deste objeto são fixas e seus valores são as chaves correspondentes no ERP. Exemplo, `"uid" : "productCode"` significa que o a API usa como `uid` é chamado de `productCode` pelo ERP
* custom_keys: são os campos adicionais, ou seja, caso se deseje obter algum campo do ERP que não esteja nos campos padrão da API. ele pode ter 2 formatos:
  * "chave" : "valor" - neste caso segue a mesma regra da chave `key` acima descrita
  * "chave" : {objeto} - no caso do valor retornado pelo ERP ser um array. neste caso, teremos <chave_API> : { <chave_ERP> : {} }


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
