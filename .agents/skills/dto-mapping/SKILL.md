---
name: dto-mapping
description: "Usala cuando una tarea crea o cambia un endpoint, un DTO o un mapper, o cuando una feature le expone datos a otra."
---

# Mapeo de DTOs

Explica cómo se arman los DTOs y su mapeo en una API REST con Spring Boot organizada por features. Las reglas valen para cualquier proyecto, y los ejemplos son de este. Si una regla choca con `docs/constitution.md`, gana la constitución.

## Cuándo aplica

- Crear un endpoint. Ejemplo: el alta de un equipo con `EquipmentController.createEquipment`.
- Sumarle un campo a un recurso. Ejemplo: agregar la marca a `EquipmentRequest`, `EquipmentResponse` y `EquipmentMapper`.
- Crear la api de una feature. Ejemplo: `catalog.api.CatalogApi` para que `rental` consulte equipos.

## Reglas

### Entrada y salida separadas: cada recurso tiene un record para la entrada y otro para la salida

Nunca se usa el mismo record para los dos lados de un endpoint. El `Request` representa lo que envía quien llama a la API; el `Response` representa lo que devuelve la aplicación.

```java
public record EquipmentRequest(@NotBlank String name, @NotBlank String serial) {}
public record EquipmentResponse(Long id, String name, String serial, EquipmentStatus status) {}
```

**Cómo se verifica:** ninguno todavía.

### La entrada trae solo los datos que conoce quien llama a la API

El `Request` contiene solo los datos que quien llama puede proporcionar. Lo que genera o decide otra capa queda afuera: el `id` lo pone la base de datos, y el estado inicial lo decide el `service`. El formato se valida en el propio `Request` con Bean Validation, y el `controller` lo marca con `@Valid`.

```java
public record EquipmentRequest(@NotBlank String name, @NotBlank String serial) {}
```

```java
@PostMapping
public ResponseEntity<EquipmentResponse> createEquipment(@Valid @RequestBody EquipmentRequest request) {
    return ResponseEntity.status(201).body(equipmentService.createEquipment(request));
}
```

`RentalRequest` sí trae `equipmentId`, porque quien alquila sabe qué equipo quiere.

**Cómo se verifica:** ninguno todavía.

### La salida no lleva entidades; las relaciones se reducen a los datos necesarios

El `Response` nunca contiene una entidad JPA. Si un recurso tiene una relación, se expone solo con los datos que necesita quien consume la API.

```java
public record RentalResponse(
    Long id,
    Long equipmentId,
    String equipmentName,
    LocalDate startDate,
    LocalDate endDate
) {}
```

**Cómo se verifica:** ninguno todavía.

### El mapper solo convierte datos

El mapper vive en el subpaquete `mapper` de su feature y tiene métodos como `toEntity` y `toResponse`. No usa `repository` ni decide reglas de negocio. Lo usa el `service`, donde la sesión de JPA sigue abierta; el `controller` no mapea.

```java
@Component
public class EquipmentMapper {
    public Equipment toEntity(EquipmentRequest request) {
        Equipment equipment = new Equipment();
        equipment.setName(request.name());
        equipment.setSerial(request.serial());
        return equipment;
    }

    public EquipmentResponse toResponse(Equipment equipment) {
        return new EquipmentResponse(
            equipment.getId(),
            equipment.getName(),
            equipment.getSerial(),
            equipment.getStatus()
        );
    }
}
```

**Cómo se verifica:** ninguno todavía.

### La api de una feature expone su propio record

Cuando una feature le expone datos a otra, lo hace con un record propio del subpaquete `api`, con solo los campos que necesita quien la usa. Nunca devuelve el `Response` del `controller`.

```java
public record EquipmentSummary(Long id, String name, BigDecimal dailyRate) {}
```

```java
public interface CatalogApi {
    Optional<EquipmentSummary> findEquipment(Long id);
}
```

**Cómo se verifica:** ninguno todavía.

## Qué evitar

- No usar el mismo record para la entrada y la salida de un endpoint.
- No pedir en un `Request` datos que pone otra capa de la aplicación, como el `id` o el estado inicial.
- No devolver una entidad JPA desde el `controller` ni dentro de un `Response`.
- No hacer que el mapper use un `repository` o decida reglas de negocio.
- No devolver `EquipmentResponse` u otro `Response` de `controller` desde la `api` de una feature.
