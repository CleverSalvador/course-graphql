# GraphQL

## Query

### Anonymous query

| Sintaxis corta      |     | Sintaxis usando `query` |
|---------------------|-----|-------------------------|
| {<br/>#fields<br/>} |     | query {<br/>#fields<br/>}    |

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


