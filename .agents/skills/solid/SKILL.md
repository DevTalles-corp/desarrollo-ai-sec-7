---
name: solid
description: "Usala cuando una tarea crea o hace crecer un service, agrega una interfaz o una implementación, suma una variante de un comportamiento o hace que una feature use otra."
---

# Principios SOLID

Explica cómo se aplican los principios SOLID en una aplicación Spring Boot organizada por features. Las reglas valen para cualquier proyecto, y los ejemplos son de este. Si una regla choca con `docs/constitution.md`, gana la constitución.

## Cuándo aplica

- Crear un service o sumarle un método. Ejemplo: sumarle a `RentalService` el registro de un alquiler.
- Sumar una variante de un comportamiento. Ejemplo: la tarifa por mes.
- Crear una interfaz o una implementación. Ejemplo: `PricingStrategy` y `WeeklyPricing`.
- Hacer que una feature use otra. Ejemplo: `rental` necesita consultar un equipo de `catalog`.

## Reglas

### Responsabilidad única: cada clase tiene un solo motivo para cambiar

Lo que cambia por el mismo motivo va junto, y lo que cambia por motivos distintos va en clases distintas. Antes de sumar un método a una clase, el agente se pregunta quién pediría ese cambio. Si es otro motivo, como las tarifas, una notificación o una integración externa, va en su propia clase. Una clase puede tener varios métodos si todos cambian por el mismo motivo.

Al registrar un alquiler, `RentalService` valida que las fechas no se superpongan con otro alquiler del mismo equipo y lo guarda con su precio. Todo eso cambia cuando cambia la forma de alquilar, así que va junto en `RentalService`. El cálculo del precio cambia cuando cambian las tarifas, así que va en su propia clase, y `RentalService` le pide el precio.

**Cómo se verifica:** ninguno todavía.

### Abierto/cerrado: una clase está abierta a extensión y cerrada a modificación

Una variante nueva de un comportamiento se suma con una clase nueva. La variante implementa la interfaz del comportamiento, sobrescribe su método y lleva `@Component` con nombre. El service recibe por constructor un `Map<String, Interfaz>` y busca la variante por el nombre que trae el pedido; si el nombre no está en el Map, lanza una excepción de la feature, creada según la skill `error-handling`. Las clases que ya existen quedan como están. Se aplica cuando las variantes crecen; con dos variantes que no cambian, alcanza una clase con dos métodos. El código completo está en `examples/strategy.md`: leelo antes de sumar una variante.

Para agregar la tarifa por mes se crea `MonthlyPricing` con `@Component("monthly")`, y `RentalService` queda como está.

**Cómo se verifica:** el `git diff` de la tarea muestra la clase nueva y ningún cambio en el código de producción del service; los tests pueden cambiar.

## Qué evitar
