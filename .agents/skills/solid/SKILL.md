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

## Qué evitar
