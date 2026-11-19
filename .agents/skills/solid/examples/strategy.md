# Ejemplo de Strategy para tarifas de alquiler

```java
package com.devtalles.rental.rental.service;

import java.math.BigDecimal;

public interface PricingStrategy {

    BigDecimal calculate(BigDecimal dailyRate, long days);
}
```

```java
package com.devtalles.rental.rental.service;

import org.springframework.stereotype.Component;

import java.math.BigDecimal;

@Component("daily")
public class DailyPricing implements PricingStrategy {

    @Override
    public BigDecimal calculate(BigDecimal dailyRate, long days) {
        // La fórmula de cada tarifa la define la spec de alquileres.
        ...
    }
}
```

```java
package com.devtalles.rental.rental.service;

import org.springframework.stereotype.Component;

import java.math.BigDecimal;

@Component("weekly")
public class WeeklyPricing implements PricingStrategy {

    @Override
    public BigDecimal calculate(BigDecimal dailyRate, long days) {
        // La fórmula de cada tarifa la define la spec de alquileres.
        ...
    }
}
```

```java
package com.devtalles.rental.rental.service;

import com.devtalles.rental.rental.exception.UnknownPricingException;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;
import java.util.Map;

@Service
public class RentalService {

    private final Map<String, PricingStrategy> pricingStrategies;

    public RentalService(Map<String, PricingStrategy> pricingStrategies) {
        this.pricingStrategies = pricingStrategies;
    }

    @Transactional
    public RentalResponse createRental(RentalRequest request) {
        PricingStrategy strategy = pricingStrategies.get(request.pricingType());

        if (strategy == null) {
            throw new UnknownPricingException(request.pricingType());
        }

        BigDecimal dailyRate = ...; // el precio por día del equipo, que viene de catalog
        BigDecimal price = strategy.calculate(dailyRate, request.days());

        // ... resto de la lógica del alquiler
    }
}
```
