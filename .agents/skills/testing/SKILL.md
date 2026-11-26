---
name: testing
description: "Usala cuando una tarea crea o cambia un test de escenario, o suma una tabla a la base."
---

# Testing

Es una skill del proyecto. Explica cómo se escriben los tests de escenario y desarrolla la sección Tests de `docs/constitution.md`.

## Cuándo aplica

- Crear el test de un escenario, como el E1 en `RentalScenarioTests`.
- Sumar un escenario a una clase de tests que ya existe.
- Crear una tabla nueva, que también se suma a `clean.sql`.

## Reglas

### Dónde viven y cómo se agrupan

Una clase de tests de escenario por feature, en su paquete. El test recorre toda la feature, así que no va en un subpaquete como `rental/controller`.

```java
package com.devtalles.rental.rental;

@SpringBootTest
@AutoConfigureMockMvc
@Sql("/db/clean.sql")
class RentalScenarioTests {
    // ...
}
```

**Cómo se verifica:** ninguno todavía.

### Nombres

La clase se llama `<Recurso>ScenarioTests`. El método, en inglés, dice la situación y el resultado. El id del escenario va solo en `@DisplayName`.

```java
@Test
@DisplayName("E1: Alquilar un equipo ya alquilado responde 409")
void rentingAlreadyRentedEquipmentReturns409() throws Exception {
    // ...
}
```

**Cómo se verifica:** ninguno todavía.

### Base limpia

`@Sql("/db/clean.sql")` sobre la clase vacía la base antes de cada test. El script, en `src/test/resources/db/clean.sql`, trunca las tablas de las features.

```java
@SpringBootTest
@AutoConfigureMockMvc
@Sql("/db/clean.sql")
class RentalScenarioTests {
    // ...
}
```

```sql
TRUNCATE TABLE rental, equipment RESTART IDENTITY CASCADE;
```

**Cómo se verifica:** ninguno todavía.

### Dependencias

`@Autowired` sobre el campo, en lugar del constructor: a la clase de test la crea JUnit, así que las ventajas del constructor no aplican.

```java
@Autowired
private MockMvc mockMvc;

@Autowired
private ObjectMapper objectMapper;
```

**Cómo se verifica:** ninguno todavía.

### Estado inicial

Por los endpoints, con métodos privados del test que hacen el POST con MockMvc. El repository solo cuando la API no permite crear el dato, como un alquiler con fechas pasadas.

```java
private Long createEquipment(EquipmentRequest request) throws Exception {
    String response = mockMvc.perform(post("/equipment")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
        .andExpect(status().isCreated())
        .andReturn()
        .getResponse()
        .getContentAsString();

    return objectMapper.readValue(response, EquipmentResponse.class).id();
}
```

**Cómo se verifica:** ninguno todavía.
