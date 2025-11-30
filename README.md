# GraphQL

## Query

### Anonymous query

| Sintaxis corta      |     | Sintaxis usando query |
|---------------------|-----|-----------------------|
| {<br/>#fields<br/>} |     | query {<br/>#fields<br/>}  |

### Named query

| Sintaxis corta                      |
|-------------------------------------|
| query queryName {<br/>#fields<br/>} |

#### Examples
```graphql
query theFirstPlanet {
    planet(planetID:1) {
        name
        population
    }
}

query allPeople {
    allPeople {
        people {
            name
        }
    }
}

query allSpecies {
    allSpecies {
        species {
            name
            averageLiespan
        }
    }
}
```
> **Nota:** En una misma consulta, puedes enviar diferentes querys.

### Fragment
Utilizados para heredar atributos en otras consultas.

#### Example
```graphql
{
    firstPerson: person(personID: "1") {
        ...personData
    }
    
    secondPerson: person(personID: "2") {
        ...personData
    }
    
    fragment personData on Person {
        name
        eyeColor
        gender
        hairColor
    }
}
```
### Variables
Las variables pueden ser utilizados en Anonymous Query y Named Query. </br>
Estás variables son representadas por el signo dolar

```graphql
query myQuery($var1: dataType1, $var2: dataType2) {

}

query myQuery($var1: dataType1, $var2: dataType2) {
    myFirstData(personID: $var1) {
        # 1st data fields
    }
    
    theSecondData(title: $var2) {
        # 2nd data fields
    }
}

query($var1: dataType1, $var2: dataType2) {
}
```

#### Optional & Required Variable
```graphql
query myQuery(
    $var1: dataType1 = 25 ---> optional with 25 as default
    $var2: dataType2! ---> required (the ! character)
) {
    myFirstData(personID: $var1) {
        # 1st data fields
    }
    
    theSecondData(title: $var2) {
        # 2nd data fields
    }
}
```
##### Examples
```graphql
query comparePerson($firstPersonId: ID!, $secondPersonId: ID!)
{
    firstPerson: person(personID: $firstPersonId) {
        ...personData
    }
    
    secondPerson: person(personID: $secondPersonId) {
        ...personData
    }
    
    fragment personData on Person {
        name
        eyeColor
        gender
        hairColor
    }
}
```
En clientes de GraphQl como Altair o Postman, se debe de configurar de la siguiente manera, el seteo de variables</br>
```json
{
  "firstPersonId": "1",
  "secondPersonId": "2"
}
```
### Directive
Permite en base a condiciones, se pueda incluir o skipear información.</br>
Existen 2 anotaciones que se colocan en la estructura del schema (@include, @skip), los cuales requieren de un parametro boolean.
#### Example
```graphql
query myConditionalQuery($showAge: Boolean!, $hideEmail: Boolean!){
    employee {
        name
        gender
        age @include(if: $showAge)
        email @skip(if: $hideEmail)
    }
}
```
```graphql
query comparePerson(
    $firstPersonId: ID!, 
    $secondPersonId: ID!,
    $showHomeworld: Boolean!,
    $hideStarship: Boolean!
)
{
    firstPerson: person(personID: $firstPersonId) {
        ...personData
    }
    
    secondPerson: person(personID: $secondPersonId) {
        ...personData
    }
    
    fragment personData on Person {
        name
        eyeColor
        gender
        hairColor
        homeworld @include(if: $showHomeworld) {
            name
            population
        }
        starshipConnection @skip(if: $hideStarship) {
            starship {
                name
                model
            }
        }
    }
}
```
```json
{
  "firstPersonId": "1",
  "secondPersonId": "2",
  "showHomeworld": true,
  "hideStarship": false
}
```

## GraphQL Schema
* Important part of GraphQL service
* GraphQL Schema Language
  * IDL (interface definition language)
  * SDL (schema definition language)
* File extension
  * schema.graphql
  * schema.graphqls
  * schema.gql

### Object
* Object name, fields, data type for each field constraint
* PascalCase object name, camelCase field name
* ! for mandatory (non-nullable) field
* Data type can be other object
* Field can be list
* Multiple object definitions on one schema
#### Example
```graphql
type Employee {
    name: String!
    email: String!
    gender: String
    addresses: [Address]
    salary: Float
}
```

### Data Type: Scalar
* Field must be resolve to concrete data
* Concrete data type called as **scalar**
* Built-in **scalar** data types
  * Int
  * Float
  * String
  * Boolean
  * ID

### Scalar for date / datetime
Custom scalar data type must be defined on GraphQL implementation.</br>

```graphql
scalar Date
scalar DateTime
type Employee {
    name: String!
    email: String!
    gender: String
    addresses: [Address]
    salary: Float
    birthDate: Date
    registeredDateTime: DateTime
}
```
> **Nota:** En Java estos tipos de datos deben ser mapeados de la siguiente manera.
> - Scalar Date to Java LocalDate
> - Scalar DateTime to Java OffsetDateTime

### Enumeration
Utilizado para definir constantes. </br>
* Enum name is PascalCase
* Enum value is UPPER_SNAKE_CASE

#### Example
```graphql
type Employee {
    name: String!
    email: String!
    gender: String
    addresses: [Address]
    salary: Float
    birthDate: Date
    registeredDateTime: DateTime
    preferredPayment: Currency
}

enum Currency {
    USD
    EUR
    JPY
}
```

### Argument
Se pueden definir argumentos opcionales o obligatorios. </br>
```graphql
fieldName(
    arg1: Arg1DataType = defaultValue1
    arg2: Arg2DataType!
) : dataType

type Employee {
    name: String!
    email: String!
    gender: String
    addresses: [Address]
    salary: (
        currency: Currency = USD
        bankCode: String!
    ) : Float
    birthDate: Date
    registeredDateTime: DateTime
    preferredPayment: Currency!
}
enum Currency {
    USD
    EUR
    JPY
}
```
Para estos casos, la manera de invocar al schema mediante una query, seria el siguiente:
```graphql
query {
    employees {
        name
        salary(currency: EUR bankCode: "mandatory")
    }
}

ó
 
query {
    employees {
        name
        salary(bankCode: "mandatory")
    }
}
```

### Query & Mutation Type
Syntaxes

```graphql
schema {
    # must define query
    query: QueryTypeName
    # optionaly define mutation
    mutation: MutationTypeName
}

type QueryTypeName {
    # query operation 1
    # query operation 2
}

type MutationTypeName {
    # mutation operation 1
    # mutation operation 2
}
```

#### Example
```graphql
schema {
    query: RootQuery
    mutation: RootMutation
}

type RootQuery {
    allEmployees: [Employee]
    oneEmployee(email: String!) : Employee
}

type RootMutation {
    setEmployeeEmail(newEmail: String!) : Employee
}
```

### Combining List & Required

```graphql
type Employee {
    name: String!
    emails: [String]
    # other fields
}
```

| Syntaxes           | Description                                                |
|--------------------|------------------------------------------------------------|
| emails: [String]   | Both list and element within list can be null              |
| emails: [String]!  | list cannot null, but element within list can null         |
| emails: [String!]  | list can null, but element within list cannot null         |
| emails: [String!]! | list cannot null, and all elements within list cannot null |



||emails: null|emails: [ ]|emails: [null]|emails: ["cleversalvador@gmail.com"]|
|--|-----|-----|-----|-----|
|emails: [String] |Valid|Valid|Valid|Valid|
|emails: [String]! |Invalid|Valid|Valid|Valid|
|emails: [String!] |Valid|Valid|Invalid|Valid|
|emails: [String!]! |Invalid|Valid|Invalid|Valid|

















