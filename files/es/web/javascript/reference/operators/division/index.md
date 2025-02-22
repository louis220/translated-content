---
title: Division (/)
slug: Web/JavaScript/Reference/Operators/Division
tags:
  - JS
  - JavaScript
  - Operador de Division
  - Operadores
  - Referências
translation_of: Web/JavaScript/Reference/Operators/Division
original_slug: Web/JavaScript/Referencia/Operadores/Division
---
{{jsSidebar("Operators")}}

El operador de división (`/`) produce el cociente de sus operandos donde el operando izquierdo es el dividendo y el operando derecho es el divisor.

{{EmbedInteractiveExample("pages/js/expressions-division.html")}}

## Sintaxis

```
Operador: x / y
```

## Ejemplos

### Division basica

```js
1 / 2              // 0.5

Math.floor(3 / 2) // 1

1.0 / 2.0         // 0.5
```

### Division por cero

```js
2.0 / 0     // Retorna Infinity

2.0 / 0.0   // Retorna Infinity, Dado que 0.0 === 0

2.0 / -0.0  // Retorna -Infinity
```

## Especificaciones

| Specification                                                                                            |
| -------------------------------------------------------------------------------------------------------- |
| {{SpecName('ESDraft', '#sec-multiplicative-operators', 'Division operator')}} |

## Compatibilidad con Navegadores

{{Compat("javascript.operators.division")}}

## Ver también

- [Addition operator](/es/docs/Web/JavaScript/Reference/Operators/Addition)
- [Subtraction operator](/es/docs/Web/JavaScript/Reference/Operators/Subtraction)
- [Multiplication operator](/es/docs/Web/JavaScript/Reference/Operators/Multiplication)
- [Remainder operator](/es/docs/Web/JavaScript/Reference/Operators/Remainder)
- [Exponentiation operator](/es/docs/Web/JavaScript/Reference/Operators/Exponentiation)
- [Increment operator](/es/docs/Web/JavaScript/Reference/Operators/Increment)
- [Decrement operator](/es/docs/Web/JavaScript/Reference/Operators/Decrement)
- [Unary negation operator](/es/docs/Web/JavaScript/Reference/Operators/Unary_negation)
- [Unary plus operator](/es/docs/Web/JavaScript/Reference/Operators/Unary_plus)
