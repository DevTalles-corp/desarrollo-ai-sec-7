---
name: clean-code
description: "Usala cuando una tarea crea o cambia una clase o un método, o renombra una clase, un método o una variable."
---

# Clean Code

Reglas de escritura de código limpio para cualquier proyecto, con ejemplos de este. Si una regla choca con `docs/constitution.md`, gana la constitución.

## Cuándo aplica

- Crear una clase o un método. Ejemplo: crear `DailyPricing` para la tarifa por día.
- Cambiar un método que ya existe. Ejemplo: sumarle a `createRental` el cálculo del precio.
- Renombrar una clase, un método o una variable. Ejemplo: renombrar `calc` a `calculate`.

## Reglas

### Un nombre dice qué es o qué hace, con palabras del dominio y sin abreviaturas

Las clases, los atributos y las variables llevan sustantivos. Los métodos llevan verbos. Los booleanos empiezan con `is` o `has`. Un número o un texto del negocio también lleva nombre: va en una constante.

```java
public BigDecimal calculate(BigDecimal dailyRate, long days) {
    return dailyRate.multiply(BigDecimal.valueOf(days));
}

private static final int DAYS_PER_WEEK = 7;
```

**Cómo se verifica:** ninguno todavía.

### Un método hace una sola cosa y se lee como una lista de pasos

Un comentario que dice qué hace un bloque es una señal de que ese bloque puede ser un método con ese nombre. Un método recibe tres parámetros como máximo; si necesita más, los que van juntos pasan a un record. El constructor que recibe dependencias queda afuera de esta regla.

```java
public RentalResponse createRental(RentalRequest request) {
    checkEquipmentIsFree(request);
    BigDecimal price = calculatePrice(request);
    Rental rental = rentalMapper.toEntity(request, price);
    return rentalMapper.toResponse(rentalRepository.save(rental));
}
```

**Cómo se verifica:** ninguno todavía.

### Un comentario explica por qué, nunca qué

El qué lo dice el código. Si se necesita explicar qué hace una línea, el nombre no es suficiente.

```java
// La fórmula de cada tarifa la define la spec de alquileres
```

**Cómo se verifica:** ninguno todavía.

### Lo que no se usa se borra

Imports, variables, métodos y código comentado que no se usan se eliminan. La versión anterior queda en git.

**Cómo se verifica:** el `git diff` de la tarea no suma código comentado ni imports que no se usan.

## Qué evitar

- Nombres como `data`, `info` o `tmp`, porque no dicen qué representan.
- Un número o un texto del negocio suelto en el código, como un `7`, porque esconde su significado.
- Un método con bloques separados por comentarios o con más de tres parámetros, porque hace más de una cosa o recibe demasiados datos sueltos.
- Un comentario que repite la línea de abajo, porque no aporta nada.
- Dejar un método o un bloque comentado por las dudas, porque git ya guarda la versión anterior.
